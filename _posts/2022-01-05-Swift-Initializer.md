---
layout: post
title: "[Swift] Initializer"
date: 2022-01-05 11:22:17 -400
lastmod: 2022-01-06 15:13:17 -400
sitemap:
changefreq : daily
priority : 1.0
categories:
- Swift
  background: "/img/classic_blue.jpeg"
---

## 초기화(Initialization)
- Class, Struct, Enum의 인스턴스를 사용하기 위한 준비 작업을 수행하는 것

- `initializer`라는 메서드에서 초기화를 수행한다.

### '준비 작업' 종류
- 초기화의 정의에서 말하는 준비 작업은 Class, Struct, Enum의 프로퍼티의 값을 설정하는 것이다.

- `initializer` 가 실행이 끝났을 때는 모든 프로퍼티의 값이 지정되어 있어야 한다‼️

### 프로퍼티의 값을 지정하는 방법 
1. `initializer`를 사용
2. default value를 설정

### :one: initializer를 사용하는 방법
1. initializer를 호출하면 인스턴스가 생성된다. 
2. syntax

{% highlight Swift %}

init() {
  // 초기화 작업을 수행한다.
}

{% endhighlight %}

### :two: default value를 설정하는 방법
1. 프로퍼티를 선언과 동시에 값을 할당할 수 있다. 이때 프로퍼티에 할당하는 값을 default value라고 한다.

### :bulb: tip




### `deinitializer`
- 클래스는 `deinitializer`를 가질 수 있다.

- 인스터스가 메모리에서 해제되기 전에 수행된다.

- 사용하는 이유: 커스텀으로 메모리 해제 해주어야 하는 것들을 정의한다.

<br/>

###    