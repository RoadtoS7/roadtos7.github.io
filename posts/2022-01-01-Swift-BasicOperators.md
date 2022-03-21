---
layout: post
title: "[Swift] Basic Operator"
date: 2022-03-04 11:22:17 -400
lastmod: 2022-01-20 15:13:17 -400
sitemap:
changefreq : daily
priority : 1.0
categories:
- Programming Paradigm
  background: "/img/classic_blue.jpeg"
---

> 이 포스팅은 docs.swift.org의 Basic Operator 내용 중, 다른 언어와 차별화되는 swift만의 특징을 발췌, 요약한 포스팅입니다.

## Assigment Operator
- 할당 연산자의 우측에 튜플을 사용하면, 튜플을 분해하여 여러개의 변수, 상수의 값으로 할당할 수 있다.

{% highlight Kotlin %}
let (x, y) = (1, 2)
{% endhighlight %}

