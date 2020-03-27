---
title: "머신러닝을 위한 Pythonic Code 01 - Split & Join"
date: 2020-03-24 22:20:17 -0400
categories: MachineLearning
--- 

머신러닝은 정말 해보고 싶었던 분야였는데, 드디어 시작해보려고 한다.  
프로젝트와 함께 진행되는 터라 설렘반 두려움 반이지만 그 만큼 실력을 기를 수 있을 것 같아서 설렘이 조금 더 큰 것 같다.  
그럼 머신러닝을 위한 Python Code에 대해 시작해보겠다.  

<br/>
<br/>

## Pythonic Code
Pythonic Code란 말 그대로 파이썬스타일 코드이다.  
우리가 Pythinic Code를 알아야하는 이유는 현재 Python 고급 프로그램 개발자들이 Pythonic Code를 작성하고 있기 때문에,  
그 코드를 읽기 위해서라도 이에 대해 아는 것이 중요하다.  

<br/>
<br/>

예시로 여러 단어들을 하나로 붙일 때 기존에 방식으로 작성하면 다음과 같이 반복문과 + 연산자를 이용하게 된다.
```
colors = ['red','blue','green']
result = ''
for i in colors:
  result += s
```

<br/>
<br/>

하지만 Pythonic Code에서는 join이라는 함수를 사용하여 list에 있는 각 요소들을 합쳐 문자열로 만드는 방식을 사용한다.  
```
>>> colors = ['red','blue','green']
>>> result = ''.join(colors)
>>> result
'redbluegreen'
```

즉, Pythonic Code에서는 파이썬 특유의 기능을 이용하여 효율적으로 코드를 작성할 수 있다.  


<br/>
<br/>

<br/>
이렇게 Pythonic Code에 대한 필요성과 간략한 설명을 했다.  
다음으로는 split(), join()이라는 python 특유의 함수에 대해서 알아보겟다.  

<br/>

<br/>
<br/>


## split(), join()
split()과 join()을 통해서는 리스트에 있는 요소들을 합쳐서 문자열로 만들거나,
혹은 문자열을 구분문자를 기준으로 나눠서 문자열로 만들 수 있다.  

<br/>
<br/>

### split()
- split()은 string 타입의 값을 구분자를 기준으로 나눠서 리스트 타입으로 반환한다.  
먼저 코드를 살펴보겠다.  

```
>>> items = 'zero one two three'.split()
>>> print(itmes)
['zero', 'one', 'two', 'three']
```

<br/>
<br/>

- 머신러닝에서 컴퓨터에게 문자를 이해시킬 때 BackUp Word라는 방법을 사용한다. 이때 split()을 사용하면 유용하다.
- 문자열.split(기준문자) 형태로 사용한다.
```
>>> example = 'python, jquery, javascript'
>>> example.split(',')
['python', ' jquery', ' javascript']
```

<br/>
<br/>

- 다음 코드는 만들어진 리스트의 각 값을 변수에 unpacking 하는 예시이다.
- = (할당연산자)의 좌측에 변수들을 나열하고 우측에 리스트를 두면, 
우측에 위치한 리스트의 각 요소들이 좌측에 위치한 변수들에 할당된다. 이러한 방법을 unpacking 이라고 한다.  
```
>>> example = 'python, jquery, javascript'
>>> a, b, c = example.split(',')
>>> a
'python'
>>> b
' jquery'
>>> c
' javascript'
```


<br/>
<br/>


그렇다면 "."을 기준으로 문자열을 나누고 Unpacking하는 코드는 다음과 같다.
```
>>> example = 'cs50.gachon.edu'
>>> subdomain, domain, tld = example.split('.')
>>> subdomain
'cs50'
>>> domain
'gachon'
>>> tld
'edu'
```


<br/>
<br/>
<br/>
<br/>

### join()

join() 함수는 리스트의 각요소를 합쳐서 문자열로 만드는 함수이다.
- 문자타입리스트.join(기준문자) 형태로 사용한다.
결과적으로 기준문자를 기준으로 문자타입 리스트가 합쳐져서 하나의 문자열이 반환된다.

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>



> 이 포스팅은 부스트코스의 머신러닝을 위한 Python 내용을 바탕으로 만들어졌습니다.
