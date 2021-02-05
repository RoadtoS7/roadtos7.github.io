---
layout: post
title:  "Android - Application Class"
date:   2021-02-05 18:16:17 -400
lastmod : 2021-02-05 18:16:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

## Application Class
안드로이드 앱의 액티비티나 서비스와 같은 컴포넌트를 포함하는 Base Class이다.
`Application` class를 상속받아서 직접 만들 수 있는데, 상속받아서 직접 만든 것인지의 여부의 관계없이 
`Application`클래스는 우리 애플리케이션/패키지의 프로세스가 생성될 때 어떤 클래스보다도 가장 먼저 인스턴스화 된다.

     