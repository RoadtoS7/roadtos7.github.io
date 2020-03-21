---
title: "Python_01 Class(클래스)"
date: 2020-03-21 23:36:07 -0400
categories: Python
---

# Class

파이썬에서도 자바와 같이 클래스가 존재한다.  
이번 포스팅에서는 자바의 클래스와 파이썬에서의 클래스의 주된 차이점을 다루려고 한다.  
</br>
</br>


```
Class Calculator:
  def __setData(self, first, second):
    self.first = first
    self.second = second
```

## 1. 클래스 멤버함수의 첫번째 인자로 항상 self 로 명명되어지는 인자가 들어간다.  

self를 통해서 멤버함수를 호출하는 객체가 전달된다.  

## 2. 멤버함수를 호출할 때는 self 파라미터에 해당하는 값은 쓰지 않는다.  

다음 코드와 같이 setData 를 호출할 때는 self에 해당하는 값은 명시하지 않는다.  

```
 a = Calculaotor()
 a.setData(2, 4)
```

## 3. 멤버 변수를 명시하지 않더라도 멤버함수 안에서 선언한 변수가 자동으로 생성되어 멤버변수가 된다.  

a.setData(2, 4) 의 함수 수행문을 살펴본다면 self는 함수를 호출한 객체인 a이기 때문에 다음과 같이 해석된다.  
```
>>> a.first = 2 
# a 객체에 객체 변수 first 가 생성되고 여기에 값 4가 저장된다.

>>> a.second = 4
# a 객체에 객체 변수 second 가 생성되고 여기에 값 2가 저장된다.

>>> print(a.first)
2
>>> print(a.second)
4
```

print(a.first), print(a.second) 의 결과가 정상적으로 출력되는 것으로 볼 때, 멤버변수를 따로 선언하지 않았지만  
first와 second 라는 멤버변수가 생성되었음을 알 수 있다.  


