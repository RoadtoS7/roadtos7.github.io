---
layout: post
title: "Android App Launch Process"
date: 2021-08-03 22:08:17 -400
lastmod: 2021-08-03 22:08:17 -400
sitemap:
  changefreq : daily
  priority : 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---


이 포스팅은 사용자가 아이콘을 클릭했을 때 앱이 어떻게 앱이 시작되는지를 설명한다.  

## 안드로이드 앱 특징
1. Multiple Entry Point  
   - 여러개의 컴포넌트로 이루저여 있고, 각 컴포넌트는 우리 앱 뿐만 아니라 다른 앱에서도 실행시킬 수 있다.
   
   - 각 컴포넌트는 애플리케이션의 entry point 역할을 한다.
   
   - 따라서 main()함수와 같이 하나의 entry point 만을 갖는 전통적인 애플리케이션과는 다르다.

2. Own Little World
   - 모든 안드로이드 앱은 분리된 프로세스에서 실행된다.
   
   - Java Runtime (Dalvik VM 혹은 ARM)에서 각각 자신만의 인스턴스를 갖고 있으며, unique한 user ID를 갖는다.

## 언제 안드로이드 프로세스가 실행되는가?
안드로이드 프로세스는 자신이 필요할 때 실행된다.  

사용자나 다른 시스템 컴포넌트가 우리 애플리케이션에 속한 컴포넌트를 요구하면, 안드로이드 시스템은 앱이 아직 실행되지 않은 경우에 앱의 새로운 
프로세스를 시작한다.  
여기서 말하는 컴포넌트는 서비스가 될 수도 있고, 액티비티가 될 수도 있으며, intent 리시버가 될 수 있다.  

일반적으로 프로세스는 시스템에서 kill하기 전까지 계속 실행된다.  

애플리케이션 프로세스는 이렇듯 필요시에 생되고 lanch 액티비티가 화면에 보이기 전까지 많은 일을 수행한다.  


### 모든 앱은 자신만의 process를 가지고 시작된다.  

기본적으로 안드로이드 앱은 자신만의 안드로이드 프로세스에서 실행된다.  
안드로이드 프로세스는 Linux 프로세스에 해당한다. 따라서 시작될 때 무조건 하나의 스레드를 갖고 있다.  
예를 들어서, 이메일에 있는 하이퍼 링크를 클릭한다면 브라우저를 통해서 웹페이지가 띄워진다. 이메일 앱과 브라우저는 분리된 2개의 애플리케이션이다. 그리고 각자 독립된 프로세스 내에서 실행된다.   

클릭 이벤트는 안드로이드 플랫폼이 새로운 브라우저 프로세스를 시작하도록 하고, 이 프로세스 내에서 브라우저 액티비티 인스턴스가 생성된다.  
다른 애플리케이션에 있는 액티비티도 마찬가지로 시작된다.  

<br/>
<br/>

## Zygote: 새로운 생명, 새로운 프로세스를 생성
안드로이드 시스템이 실행되는 과정을 살펴보자.  
대부분의 Linux 기반 시스템과 마찬가지로 안드로이드 시스템도 부터 로더가 커널을 로드하고 init 프로세스를 시작한다.  

이 init 프로세스가 "daemons"라고 불리는 로우 레벨 Linux 프로세스들을  생성한다. 예를 들어서 android debug daemon, USB daemon 등이 해당한다.  
이러한 데몬들은 통상적으로 radio 인터페이스와 같이 로우 레벨 하드웨어 인터페이스를 다룬다.

이후, init 프로세스가 `Zygote` 라는 프로세스를 시작한다.  

이 프로세스가 나머지 애플리케이션 프로세스를 생성한다.   
또한 Dalvik 가상 머신 인스턴스를 가장 처음 초기화하는 프로세스이다.  
Android 애플리케이션 프레임워크에서 사용하는 모든 공통 클래스들과 시스템에 설치된 다양한 앱을 사용 전에 미리 로드해놓는다.  따라서 다른 프로세스를 생성하기 위해 복제될 준비를 한다.   
그리고 새로운 애플리케이션 프로세스를 위한 VM을 생성하는 요청을 받기 위한 소켓 인터페이스를 리슨하는 작업을 시작한다.  
이 인터페이스를 통해서 요청이 들어오면, 자신을 fork하여 미리 초기화된 VM 인스턴스를 갖는 새로운 프로세스를 만든다.  

zygote 프로세스의 과정이 끝난 후, init 프로세스는 runtime process를 시작한다.

그리고 나서, zygote는 `system server`라는 프로세스를 생성한다.  
system server는 안드로이드 플랫폼의 핵심 서비스들을 생성한다. 대표적으로는 activity manager service, hardware service 등을 생성한다.  

이 작업까지 완료되면, 가장 첫번째 앱 프로세스인 런처 애플리케이션(시작 프로그램)을 시작할 준비가 되었다.  런처 애플리케이션은 홈화면을 표시하는 애플리케이션이다.  

<br/>
<br/>

## 런처 애플리케이션에서 사용자가 앱 아이콘을 클릭하면 무슨 일이 일어날까?  

클릭 이벤트는 `startActivity(intent)` 호출로 변환된다.
그리고 Binder IPC를 통해서 ActivityManagerService 로 전달된다.  

그리고 ActivityManagerService는 다음과 같은 여러 단계를 거쳐서 `startActivity(intent)` 호출을 처리한다.

1. intent 객체의 목적지에 대한 정보를 수집한다.  

    이 작업은 PackageManager 객채의 resolveIntent() 메서드를 호출하여 이루어 진다. 
    이때 디폴트 파라미터로 PackageManager.MATCH_DEFAULT_ONLY, PackageManager.GET_SHARED_FILES 플래그가 사용된다.  

2. 목적지에 대한 정보는 intent 객체에 다시 저장하여 이 작업을 다시 하지 않도록 예방한다.

3. 유저가 목적지 컴포넌트를 실행할 권한이 있는지 체크한다.  
이 작업은 `grantUriPermissionChecked()` 메서드를 호출하여 이루어진다.  

1. 만약 유저가 권한을 가지고 있다면, ActivityManagerService는 타겟 액티비티가 새로운 테스크에서 생성되어야 하는지를 체크한다.  
새로운 테스크 생성 여부는 FLAG_ACTIVITY_NEW_TASK와 FLAG_ACTIVITY_CLEAR_TOP과 같은 인텐트 플레그에 따라서 결정된다.  

3. ProcessRecord 인스턴스가 목적지 프로세스에 이미 존재 하는지를 체크한다.  
만약 ProcessRecord 인스턴스가 존재하지 않는다면, ActivityManager는 목적지 컴포넌트를 생성하기 위해 새로운 프로세스를 시작해야 한다.  

이 과정을 거쳐서 최종적으로 애플리케이션이 실행된다.  

이 과정을 나타낸 도식화하면 다음과 같다.  

<img src="https://miro.medium.com/max/1400/1*PgmbDe-cd9Z8l3_8cw2yww.png" width=700/>  


<br/>
<br/>

## 프로세스가 시작될 때까지의 크게 3 단계로 이루어져 있다.  

1. Process 생성
2. Process와 애플리케이션 연결
3. Activity 시작 or 서비스 시작 or 인텐트 리시버 시작

### 1. Process 생성

ActivityManagerService는 startProcessLocked() 메서드를 호출하여 새로운 프로세스를 생성한다.  
이 메서드는 소켓 인터페이스를 통해서 Zygote 프로세스에게 새로운 프로세스 생성을 요청한다.  
Zygote는 fork를 호출하고, ActivityThread 객체를 생성하는 ZygoteInit.main() 함수를 호출한다.  이 함수는 새롭게 생성된 프로세스의 id를 결과값으로 반환한다.  

모든 프로로세스는 기본적으로 하나의 스레드를 가지고 있다.  
메인 스레드는 메시지큐에 있는 메시지를 처리하기 위해 Looper 인스턴스를 가지고 있다. 그리고 메인 스레드는 매번 run() 메서드를 호출할 대마다 Looper.loop()을 호출한다.  
Looper의 역할은 메시지 큐에서 메시지를 pop하여 각 메시지를 처리하는 데 필요한 함수를 호출한다.  
메시지를 처리 후, ActivityThread는 Looper.prepareLoop() 그리고 Looper.loop()을 차례로 호출하여 message loop을 메시지 loop을 시작한다. 

이러한 일련의 메서드 호출을 자세히 그림으로 나타내면 다음과 같다.   
<img src="https://miro.medium.com/max/2000/1*JymZYEYdVk5kGA0q7qA8ig.png" width=700>

<br/>
<br/>

## 2. 애플리케이션 바인딩
다음 단계에서는 생성된 프로세스와 애플리케이션을 연결한다.  
이 작업은 thread객체의 bindApplication() 함수를   호출하여 이루어진다. 

이 메서드는 메시지 큐에 BIND_APPLICATION 메시지를 보낸다.   
이 메시지는 Handler 객체에 의해서 꺼내지고, 꺼내진 메시지는 Handler 객체의 handleMessage()의해서 처리된다.  
handleMessage()는 각 메시지에 맞는 처리 작업을 수행하는데, 이 경우에는 handleBindApplication()이 호출된다.  
handleBindApplcation()은 makeApplication()을 호출하여 애플리케이션에 속하는 클래스들을 메모리에 로드한다.  

이 과정을 그림으로 나타내면 다음과 같다.  
<img src="https://miro.medium.com/max/2000/1*UmwlBmdwJip3AdKtSVypjQ.png" width=700>

## 3. 액티비티 시작하기
앞선 단계를 거쳐서 시스템은 애플리케이션을 담당할 프로세스를 생성 완료했다.  
그리고 이 프로세스의 메모리에는 애플리케이션의 클래스들이 로드되어 있다.  
액티비티를 시작하는 함수 호출 순서는 새롭게 프로세스가 시작된 경우나 기존에 프로세스가 이미 실행된 경우나 동일하다.  

여러 함수 호출 중에서도 실질적으로 액티비티를 시작시키는 메서드는 realStartActivity() 메서드이다. 이 메서드는 application 스레드 객체의 scheduleLaunchActivity() 함수를 호출한다.  
이 메서드는 LAUNCH_MESSAGE를 메시지 큐에 전달하고, 이 메시지는 handleLaunchActivity 메서드에 의해서 처리된다.  

예를 들어서 Video Browser 애플리케이션을 사용자가 실행했다고 가정할 때, 액티비티를 시작하는 함수 호출 단계는 다음과 같다.  

<img src="https://miro.medium.com/max/2000/1*lMIXA7wMIxntOtF0P7Xk_w.png" width=700>  

이 과정을 거친 후, 액티비티는 onCreate() 메서드가 되면서 자신의 생명주기를 관리하기 시작한다.  
onRestart()함수가 호출되면 포그라운드로 액티비티가 오게 되고 onStart()가 호출되면 사용자와 상호작용하기 시작한다.  

## 참고
안드로이드 애플리케이션의 실행 유형은 다음과 같이 3 종류가 있다.

1. Cold start  
프로세스를 생성 후 애플리케이션이 까지 실행하는 경우   
즉, 새로운 프로세스를 생성하고, 리소스를 초기화하여 애플리케이션 인터페이스를 보여주는 것까지 전부 실행하는 경우를 나타낸다.  
다른 애플리케이션 실행 유형과 다르게 가장 오랜 시간이 걸린다.  

2. Hot start  
애플리케이션 프로세스는 기존에 실행되어 있어서 액티비티가 디바이스 화면에 나타나도록 포그라운드로 액티비티를 가져오는 작업만 하면된다.  
애플리케이션을 실행 작업이 가장 짧은 시간을 걸리는 실행유형이다.
1. Warm start  
이 메서드는 cold start 작업의 일부만 수행한다.  
예를 들어서 프로세스는 이미 생성되어 있지만 화면에 보여져야할 actvity를 재생성해야 하는 경우이다.  
애플리케이션 실행 시간이 두번째로 가장 많이 걸린다.  







---
참고  
[Android Application Launch exlained: from Zygote to your Activity.onCreatE()](https://medium.com/android-news/android-application-launch-explained-from-zygote-to-your-activity-oncreate-8a8f036864b)