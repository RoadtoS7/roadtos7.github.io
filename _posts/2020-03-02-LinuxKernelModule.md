---
title: "리눅스 커널 모듈: 2. Hello World"
date: 2020-03-01 20:36:07 -0400
categories: LinuxKernelModule
---

# Chapter 2. Hello World
## 2.1 Hello, World(part 1): The Simplest Moudle
커널 모듈 프로로그래밍의 여러가지 특징을 다루기 위해서 hello world program 시리즈로 시작하겠습니다.

다음 코드는 가장 간단한 모듈입니다. 작성만 하고 아직은 컴파일 하지 않을 것입니다. 다음 장에서 모듈 컴파일에 대해서 다룰 것입니다.  

* Example 2-1. hello-1.c

```
/*  
 *  hello-1.c - The simplest kernel module.
 */
#include <linux/module.h>	/* Needed by all modules */
#include <linux/kernel.h>	/* Needed for KERN_INFO */

int init_module(void)
{
	printk(KERN_INFO "Hello world 1.\n");

	/* 
	 * A non 0 return means init_module failed; module can't be loaded. 
	 */
	return 0;
}

void cleanup_module(void)
{
	printk(KERN_INFO "Goodbye world 1.\n");
}
```

커널 모듈은 두가지 함수를 반드시 가져야 합니다.
첫번째는 "start"(initialization) function인 init_moudle() 함수입니다. 이 함수는 모듈이 insmod명령어를 통해서 커널에 로딩될 때 호출됩니다.   
그리고 "end"(cleanup) function인 cleanup_module()함수 입니다. 이 함수는 모듈에 rnmod명령어가 실행되기 직전에 실행됩니다. 즉, 모듈이 커널에서 제거될 때 호출됩니다.  

사실 kerenl 2.3.13부터 상황이 변해서 여러분들이 start function 과 end function의 이름을 원하는 것으로 지정할 수 있습니다.  
이 방법에 대해서는 2.3장에서 다루도록 하겠습니다.  
사실 바뀐 방법이 권장되는 방법입니다. 하지만 많은 사람들이 여전히 start function 과 end function 으로 init_moudle(), cleanu_module()이라는 이름을 사용하고 있습니다.  

<br/>
<br/>

전형적으로 init_module()은 커널에 핸들러를 등록하거나 혹은 커널의 한 함수를 자신의 코드로 대체합니다.  
(보통 대체하는 코드는 어떤 역할을 하는 코드여서 원래 함수를 호출합니다.)  
cleanup_module()함수는 일반적으로 init_module()함수가 한 작업을 원래대로 되될리는 작업을 합니다. 그래서 모듈이 안전하게 메모리에서 내려올 수 있습니다.  

<br/>
<br/>
마지막으로 모든 커널 모듈은 linux/module.h 헤더파일을 포함해야 합니다.  
우리는 printk() log level인 KERN_ALERT를 위한 macro expansion 하기 위해서 linux/kernel.h이 필요한데, 이 부분에 대해서는 Section 2.1.1. 장에서 알아보도록 하겠습니다.  

<br/>
<br/>

### 2.1.1 Introducing printk()
여러분들의 예상과 다르게 printk()는 사용자에게 정보를 전달하기 위한 것이 아닙니다.  
심지어 hello-1 예제에서는 출력목적으로 사용했지만요.  
이 함수를 실행하면 커널의 logging mechanism 이 수행되고 이 logging mechanism 은 수행한 함수의 정보를 (log에) 기록하거나 혹은 경고를 알립니다.  
따라서 각각의 printk() 문은 우선순위와 함께 사용됩니다.  
앞에서 봤던 <1> 과  KERN_ALERT가 우선순위에 해당합니다. 총 8가지의 우선순위가 존재하며 커널은 이를 위한 커널은 이 8 가지 우선순위에 대한 매크로들을 가지고 있습니다.  
그래서 우리는 우선순위를 반드시 숫자로 명시할 필요가 없습니다. 각각의 숫자나 그에 대응되는 매크로는 linux/kernel.h 헤더파일에서 확인할 수 있습니다.  
만약 여러분이 우선순위 수준을 명시하지 않는다면, 디폴트 우선순위인 DEFAULT_MESSAGE_LOGLEVEL이 사용될 것입니다.

<br/>
<br/>
우선순위 매크로들을 시간을 내서 꼭 읽어보시기를 바랍니다. 그 헤더파일은 각각의 우선순위가 어떤 것을 의미하는지도 담고 있습니다.  
실제로 printk()를 사용할 때 <4>와 같은 숫자를 사용하지 말고 KERN_WARNING을 사용할 것을 권장합니다.  

<br/>
<br/>
만약 우선순위가 int console_loglevel보다 낮다면, 이러한 우선순위를 가진 메시지는 현재 터미널에서 출력될 것입니다.  
만약 syslogd 와 klogd 가 실행되고 있다면, 이 메시지는 실제로 콘솔에 출력되는지 여부와는 상관없이 무조건 /var/log/messages 파일에도 추가될 것입니다.  
우리는 예제에서 printk() 메시지가 그저 로그파일에 기록되는 것이 아니라 여러분의 콘솔에 출력되도록 높은 우선순위에 해당되는 KERN_ALERT를 사용했습니다.  
여러분이 실제 모듈을 작성할 때는 그때 상황에서 의미있는 우선순위를 사용하길 원할 것입니다.  

<br/>
<br/>

## 2.2 Compling Kerenl Modules
커널 모듈은 일반적인 사용자 프로그램과는 다소 다른 컴파일이 필요합니다.  
이전의 커널 버전은 사용자 프로그램과는 다른 설정에 대해서 더 자세하게 다루도록 요구했습니다.  
(이 설정들은 Makefiles에 저장되어 있습니다.)  
비록 계층적으로 조직되어 있다고 하더라도, 많은 불필요한 설정들이 서브레벨 Makefile들에 축적되어 있습니다. 그리고 불필요한 설정들은 Makefile들을 크고 다소 크고 유지하기 어렵게 게 만듭니다.  
다행히 이런 것들을 해결해줄 새로운 방식이 등장했는데, 그것이 바로 kbuild 입니다.  
그리고 외부에서 가져올 수 있는 모듈을 위한 빌드 프로세스는 완전히 표준 kerenl build mechanism에 포함되었습니다.  
공식적으로는 커널의 일부분이 아닌 모듈들을 컴파일하는 방법에 대해서 더 배우기 위해서는 linux/Documentation/kbuild/modules.txt파일을 확인하기를 바랍니다.  

hello-1.c 모듈을 컴파일하기 위해서 간단한 Makefile을 보도록 하겠습니다.

**Example 2-2. Makefile for a basic kernel module
```
obj-m += hello-1.o
all:
  make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
  
clean:
  make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```
첫 번째 라인이 정말 필요하다는 기술적인 관점에서 볼 때, "all"과 "clean" 부분은 단순히 편의를 위해서만 추가되었다.  

<br/>
<br/>
이제 여러분은 make명령어를 통해 모듈을 컴파일 할 수 있다. make 명령어로 컴파일을 했기 때문에 다음과 같은 출력결과를 얻게 될 것입니다.  

```
hostname:~/lkmpg-examples/02-HelloWorld# make
make -C /lib/modules/2.6.11/build M=/root/lkmpg-examples/02-HelloWorld modules
make[1]: Entering directory `/usr/src/linux-2.6.11'
  CC [M]  /root/lkmpg-examples/02-HelloWorld/hello-1.o
 Building modules, stage 2.
  MODPOST
  CC      /root/lkmpg-examples/02-HelloWorld/hello-1.mod.o
  LD [M]  /root/lkmpg-examples/02-HelloWorld/hello-1.ko
make[1]: Leaving directory `/usr/src/linux-2.6.11'
hostname:~/lkmpg-examples/02-HelloWorld#
```

kernel 2.6 에서는 새로운 파일 네이밍 컨벤션을 도입했다는 것에 주의하시기 바랍니다. 커널 모둘은 이제 .ko 확장자를 갖습니다.
(예전에는 .o 확장자를 가졌습니다.) 이 확장자를 통해서 커널 모듈을 object파일들하고 쉽게 구분할 수 있게 되었습니다.
이렇게 확장자를 변경한 이유는 커널모듈이 .modinfo 라는 부분을 추가적으로 가지고 있기 때문입니다.  
.modinfo 부분은 모듈이 어디에 보관되어 있는지에 대한 정보에 해당합니다. 이 정보가 왜 유용한지에 대해서 보겠습니다.

<br/>
<br/>

modinfo hello-*.ko 명령어를 사용해서 .modinfo 섹션에 어떤 정보들이 담겨있는지 볼 수 있습니다.
```
hostname:~/lkmpg-examples/02-HelloWorld# modinfo hello-1.ko
filename:       hello-1.ko
vermagic:       2.6.11 preempt PENTIUMII 4KSTACKS gcc-3.3
depends:
```

별로 특별한게 없어보입니다. 하지만 우리가 나중에 다룰 예제인 hello-5.ko를 대상으로 명령어를 실행한다면 결과가 다르게 출력됩니다.  
```
hostname:~/lkmpg-examples/02-HelloWorld# modinfo hello-5.ko
filename:       hello-5.ko
license:        GPL
author:         Peter Jay Salzman
vermagic:       2.6.11 preempt PENTIUMII 4KSTACKS gcc-3.3
depends:
parm:           myintArray:An array of integers (array of int)
parm:           mystring:A character string (charp)
parm:           mylong:A long integer (long)
parm:           myint:An integer (int)
parm:           myshort:A short integer (short)
hostname:~/lkmpg-examples/02-HelloWorld# 
```

여기서는 많은 유용한 정보들을 볼 수 있습니다.  
bugreports를 위한 author나 license 정보 심지어는 이 모듈이 입력받는 파라미터에 대한 짧은 설명도 볼 수 있습니다.  
커널모듈에 대한 Makefiles에 관한 추가적은 정보는 linux/Documentation/kbuild/makefiles.txt 파일에 있습니다.  
MakeFiles를 해킹하기 전에 이 정보와 관련된 파일들을 읽어본다면 많을 일을 줄이는데 도움이 될 것입니다.  

이제 insmod ./hello-1.ko 명령어를 이용하여 컴파일된 모듈을 커널에 심어보겠습니다.  
<br/>
<br/>

커널에 적재된 모든 모듈은 /proc/modules 파일에 적혀있습니다.  
위 명령어를 실행한 후에 /proc/modules 파일에 cat명령어를 이용하여 여러분의 모듈이 커널에 적재되었는지 확인하세요.  
만약 있다면 축하합니다. 이제 여러분은 리눅스 커널 코드의 저자가 된 것입니다.  

그리고 rmmod hello-1 명령어를 통해서 커널로부터 모듈을 제거할 수 있니다.  
/var/log/messages를 자세히 살펴보면 모듈이 커널에 올라가고 제거된 사실이 시스템 로그파일에 기록되었는지를 확인할 수 있ㅅ브니다.  

다음으로는 새로운 예제를 다루어보겠습니다. init_module()의 리턴문 내용이 위에 보이시나요?  
init_module()의 리턴값을 음수로 바꾸어서 다시 컴파일해본 후 모듈을 다시 로드해보겠습니다.  
 
<br/>
<br/>
<br/>
<br/>
<br/>


