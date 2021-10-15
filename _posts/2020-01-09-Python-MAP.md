---
title: "Python - map"
date: 2020-01-09 14:25:28 -0400
background: "/img/classic_blue.jpeg"
categories: Language
---

# map
알고리즘 문제에서 한 줄에 여러개의 입력값을 받아서 리스트를 생성하는 코드를 짜던 중, MAP이라는 것을 알게 되었다.
이 김에 Map에 대한 포스팅을 해보려 한다.

> Mmap 이란
> 리스트의 요소를 지정된 함수로 처리해주는 함수이다.
> map 은 원본 리스트를 변경하지 않고 새로운 리스트를 생성한다.

list(map(함수, 리스트))
tuple(map(함수, 튜플))



**리스트 요소를 형변환 할 때 사용하기 편리하다**

```
  >>> a = [1.2, 2.5, 3.7, 4.6]
  >>> a = list(map(int, a))
  >>> a
  [1, 2, 3, 4]
```

위의 코드에서 map에 int와 list를 넣으면 리스트의 모든 요소에 int()를 적용하여 반환한다. 그리고 바깥쪽의 list()가 실행되어
map의 결과를 다시 리스트로 만들어 준다.


![map과정](https://dojang.io/pluginfile.php/13699/mod_page/content/3/022019.png)



**map에는 리스트뿐 만만 아니라 반복 가능한 모든 객체를 넣을 수 있다.**

아래 코드는 range를 이용하여 숫자를 만든뒤 map을 이용하여 문자열로 변환해서 출력하는 코드이다.

```
>>> a = list(map(str, range(10)))
>>> a
['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
```

range를 이용하여 0부터 9까지의 숫자를 만들고 이를 문자열로 형변환하여 출력하고 있다.


> Tip: input().split()의 결과는 리스트였어서 여러개의 정수나 실수를 입력받을 때 map하고 같이 사용될 수 있었다.



**map을 이용하여 형변환 한것을 map객체가 반환된다. 따라서 list나 tuple로 형변환하여 사용하는 것이 편리하다.**

```
>>> a = map(int, input().split())
10 20 (입력)
<map object at 0x03DFB0D0>
>>> list(a)
[10, 20]
```

> Tip : map이 반환하는 map 객체는 이터레이터라서 변수 여러개에 저장하는 언패킹이 가능하다. 따라서
> a, b = map(int, input().split())) 처럼 list로 형변환하지 않아도 a, b에 입력된 것을 차례대로 하나씩 담을 수 있다.

a, b = map(int, input().split())을 풀어서 쓰면 다음과 같은 코드가 된다.
```
x = input().split() # input().split()의 결과로 문자열 리스트가 반환된다, x = 문자열 리스트
m = map(int, x) # 리스트의 요소가 int로 변환된다, 결과로 나오는 것은 map객체이다. m = map 객체
a, b = m # 맵 객체는 변수 여러 개에 저장할 수 있다. a와 b에 map객체의 구성요소가 하나씩 담긴다.
```

위 포스트는 파이썬 코딩 도장의 [리스트에 map 사용하기](https://dojang.io/mod/page/view.php?id=2286)를 참조했습니다.





