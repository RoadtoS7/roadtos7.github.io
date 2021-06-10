---
layout: post
title: "Paging - With Network and DB (1)"
date: 2021-02-01 16:21:17 -400
lastmod: 2021-02-01 16:21:17 -400
sitemap:
  changefreq: daily
  priority: 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

## COLLATE
테이블의 각 칼럼 이름 뒤에 잇는 `COLLATE`절은 해당 칼럼내에 있는 텍스트 항목(entry)에 사용되는 병합 순서(일련의 데이터 항목의 순서를 정하기 위해 쓰는 임의의 논리적 순서)를 정의한다.
기본 값은 `CREATE TABLE`문에서 해당 열에 대해 정의된 병합 순서에 해당한다.  
만약 달리 정의된 병합 순서가 없다면 기본 제공된 BINARY 결합 시퀀스가 사용된다.  

이렇게만 설명하면 이해가 되지 않을 수 있지만, 간단히 말해서 `COLLATE`는 SQL엔진이 내부적으로 데이터를 정렬하는 방식을 말한다.  
Binary Collation(BINARY 병합 시퀀스)는 이진수 비교를 통해서 정렬한다.  
일반적으로 비트 패턴을 체크하기 때문에, 가장 빠른 정렬이다.

Binary Collation에 대해서 예시를 통해 살펴보겠다.  
Binary Collation은 문자열을 바이트마다 비교한다. 따라서 A, B, a, b를 대소문자를 구분하지 않는(NO CASE) 순서로 정렬하면 a, A, b, B가 된다.  

Binary Collation의 장점은 문자열 비교가 매우 빠르고 간단하기 때문에 속도가 빠르다는 것이다.  

COLLATE NOCASE는 대소문자를 구분하는 쿼리에도 영향을 미친다.  
만약 'aa'를 값으로 가지는 칼럼과 'aA'를 값으로 가지는 칼럼이 둘다 존재할 때, 다음 쿼리문을 실행한다면 'aa'를 값으로 가지는 것만 쿼리의 결과로 반환된다.
  
{% highlight SQL %}
select * from table where col = 'aa'
{% endhighlight %}

하지만 COLLATE NOCASE를 해당 칼럼에 설정해두었다면, 'aa', 'aA'를 가지는 칼럼 둘다 반환된다.  

쿼리에서 COLLATE를 지정할 수도 있다. 즉, 해당 칼럼에 COLLATE를 설정해두지 않았더라도, 쿼리할 때 적용하도록 할 수 있는 것이다.  
단, 쿼리문에서 지정한 경우는 칼럼에 설정해 둔 경우보다 속도가 느리다.

{% highlight SQL %}
select * from table where col = 'aa' COLLATE NOCASE
{% endhighlight %}

   
  

 