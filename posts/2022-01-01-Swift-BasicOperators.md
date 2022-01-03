---
layout: post
title: "[Swift] Basic Operator"
date: 2022-01-01 11:22:17 -400
lastmod: 2022-01-01 15:13:17 -400
sitemap:
changefreq : daily
priority : 1.0
categories:
- Programming Paradigm
  background: "/img/classic_blue.jpeg"
---

> 이 포스팅은 docs.swift.org의 Basic Operator 내용 중, 다른 프로그래밍 언어와 차별화되는 swift만의 특징을 발췌, 요약한 포스팅입니다.

## Assigment Operator
- 할당 연산자의 우측에 튜플을 사용하면, 튜플을 분해하여 여러개의 변수, 상수의 값으로 할당할 수 있다.

{% highlight Swift %}

let (x, y) = (1, 2)

{% endhighlight %}

<br/>
<br/>

## 사칙 연산자
- Swift의 사칙 연산자는 overflow를 발생시키지 않는다.
- overflow를 일으키는 연산자를 따로 만들어두엇었다.
- `+` 연산자가 string concetation을 지원한다.

### 나머지 연산자
- Swift의 나머지 연산자: `%`
- Swift의 나머지 연산자는 modulo 연산자와는 다르다.
  - `a % b`의 연산 결과와 `a % -b` 의 연산 결과가 같다.
  
<br/>
<br/>

## 비교 연산자
### `==`, `!=` 🆚 `===`, `!==`
- 인스턴스를 비교할 때 좌측 연산자와 우측 연산자 간에 차이가 난다.
- 좌측 연산자는 인스턴스의 프로퍼티 값들이 같은지 확인한다. (같으면 `==`에서 true를 반환한다.)
- 우측 연산자는 인스턴스의 주소값이 같은지 확인한다. 다시 말해서, 두 개의 객체가 동일한 인스턴스를 참조하는지를 확인한다. (같으면 `===`에서 true를 반환한다.)

{% highlight Swift %}

class Student {
  private var name: String
  
  init(name: String) {
    self.name = name
  }
}

let a = Student(name: "a")
let b = Student(name: "a")

print(a == b) // true
print(a === b) // false

{% endhighlight %}

- 위 예시에서 `a`, `b` 는 모두 `Student` 클래스의 객체이며, `name` 프로퍼티의 값이 같다.
따라서 `==` 결과값은 `true`이다. 반면에 둘은 서로 다른 인스턴스를 생성하여 참조하고 있으므로 `===` 연산자의 결과값은 `false` 이다. 

### 튜플 비교 
- 서로 다른 2개의 튜플을 비교하는 것은 튜플의 구성요소를 왼쪽에 위치한 것부터 오른쪽에 위치한 것 순으로, 차례대로 비교하는 방식으로 이루어진다.  
- 만일 첫번째 위치한 요소들을 비교한 결과, 어느 한 쪽이 크거나 작다면 튜플의 나머지 값들은 비교하지 않고 첫번째 위치한 요소 간의 비교 결과를 튜플 전체에 대한 비교 결과로 반환한다.  
- 설명만 들을 경우 잘 이해해되지 않을 수 있다. 예시를 통해 더 자세히 살펴보자.


{% highlight Swift %}

(1, "zebra") < (2, "apple") // true

{% endhighlight %}

- 위 예시에서 가장 처음으로 이루어지는 작업은 두 튜플의 0번째 인덱스에 위치한 값인 `1`과 `2`를 비교하는 것이다.  
- `1`과 `2`를 비교하면, `2`가 더 크다.  0번째 인덱스에 위치한 값이 서로 다르기 때문에, 나머지 튜플의 요소는 비교하지 않는다.   
- 그리고 두 튜플을 비교한 결과가 바로 0번째 인덱스에 위치한 값(여기에서는 `1`과 `2`가 이에 해당한다.)을 비교한 결과가 된다.
- 따라서 오른쪽에 위치한 `(2, "apple")`의 튜플이 `(1, "zebra")`보다 더 큰 튜플이며, 따라서 위 연산 결과로 `true`가 반환된다.

{% highlight Swift %}

(2, "apple") < (2, "bird") // true

{% endhighlight %}

- 위 예시에서는 두 튜플의 0번째 인덱스에 위치한 값인 `2`와 `2`를 서로 비교한다. 비교 결과 두 값이 서로 같으므로, 0번째 인덱스에서 넘어가서 1번째 인덱스에 위치한 값을 비교한다.
- 1번째 인덱스에 위치한 `"apple"`, `"bird"`를 비교한 결과, `"bird"`의 값이 더 크므로 두 튜플 중 값이 더 큰 것은 `(2, "bird")`가 된다.  

{% highlight Swift %}

(4, "dog") == (4, "dog") // true

{% endhighlight %}

- 위 예시에서는 두 튜플의 0번째 인덱스에 위치한 값과 1번째 인덱스에 위치한 값이 모두 서로 같으므로, `==` 결과 true가 반환된다.  

- 튜플을 비교할 때 주의사항‼️: 튜플을 이루는 값들이 모두 비교 연산자를 사용할 수 있는 타입인 경우에만, 해당 튜플을 비교할 수 있다. 

{% highlight Swift %}

("blue", -1) < ("purple", 1) // true
("blue", false) < ("purple", true) // Error

{% endhighlight %}

- 위 예시에서 첫번째 줄은 두 튜플의 타입이 (String, Int)이다. 튜플을 이루는 구성요소의 타입인 String, Int 모두 비교 가능한 타입이므로, 튜플에 비교 연산자를 사용할 수 있다.
- 반면에 두번째 줄은 튜플의 타입이 (String, Bool)이다. 튜플의 1번째 인덱스 구성요소가 비교가 불가능한 Bool 타입이므로 이 튜플에는 비교 연산자를 사용할 수 없다.  

| Swift에서 비교 연산자를 이용해 비교할 수 있는 최대 튜플의 사이즈는 7이다.  따라서 8개 이상의 구성요소를 갖는 튜플을 비교하기 위해서는 직접 비교 연산자를 구현해야 한다.  

<br/>
<br/>

## 삼항 연산자 



