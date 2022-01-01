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
- 주의할 점은 첫번째 요소(인덱스 0에 위치한 요소)들의 값이 서로 같을 때에만 두번째 요소의 값을 비교한다.  마찬가지로 두번째에 위치한 값이 서로 같을 때에만 세번째에 위치한 값들을 서로 비교한다.

{% highlight Swift %}

(1, "zebra") < (2, "apple") // true

{% endhighlight %}

위의 예시에서 첫번째 줄은 `1`과 `2`를 비교한 후, `zebra`와 `apple`을 비교한다. 




