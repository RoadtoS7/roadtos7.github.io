---
layout: post
title:  "Kotlin - open"
date:   2020-08-13 14:12:17 -400
lastmod : 2020-08-13 14:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Kotlin
background: "/img/classic_blue.jpeg"
---

## open
Android의 `ViewPager2`를 공부하던 중, 구글에서 제공한 `ViewPager2`예시 코드에 open 이라는 키워드가 쓰여서, 이에 대해 본격적으로 알아보려고 합니다.  

Kotlin 공식 문서를 살펴보면, 기본적으로 코틀린의 모든 클래스는 상속이 불가능합니다. 그리고 코틀린에서는 상속 불가능한 클래스를 `final` 타입이라고 합니다.  
상속 가능한 클래스로 만들기 위해서는 `open`키워드를 사용해야 합니다. 말 그대로, 상속에 열려있는 클래스를 만든다고 생각하면 이해하기 쉽습니다.  

![image](https://user-images.githubusercontent.com/57262833/90097538-1fa26200-dd71-11ea-9ee2-854848d96f03.png)

위의 예시에서 전자제품을 나타내는 `ElectronicProduct` 클래스를 선언한 후, 이 클래스를 상속받는 `RiceCooker` 클래스를 만들었습니다. 그런데 `ElectronicProduct` 클래스의 경우 `open` 키워드를 사용하지 않고, 선언했기 때문에 상속이 불가능한 `final` 타입입니다. 따라서 예시와 같이 빨간 줄로 에러가 뜨는 것을 확인할 수 있습니다.  

<img src=https://user-images.githubusercontent.com/57262833/90097693-86c01680-dd71-11ea-9dd3-e8bc987932b8.png width=500px>

이번에는 `ElectronicProduct`클래스에 `open` 키워드를 추가했으며, 결과적으로 `RiceCooker`의 선언문에서 `ElectronicProduct` 떴던 빨간 줄이 사라지고 오류가 해결된 것을 확인할 수 있습니다.  


그리고 위의 예시에서 `open`키워드를 쓰지 않아 발생하지 않은 "the type is final, so it cannot be inherited from" 에러 외에, 또 하나의 에러("This type has a constructor, and thus must be initialized here"이라는 에러가 존재했습니다.  

Kotlin에서는 상속을 할 때, 부모 클래스에 기본 생성자(primary constructor)가 존재하는 경우, 상속 구분에서 기본 생성자를 이용하여, 초기화해야 합니다. 위의 예시에서도 `class RiceCooker(): ElectronicProduct`라고 부모 클래스 자리에 클래스 이름만 쓰는 것이 아닌 생성자를 사용하여 `class RiceCooker:ElectronicProduct()` 와 같이 사용하고 있음을 확인할 수 있습니다.  

<br/>
<br/>
