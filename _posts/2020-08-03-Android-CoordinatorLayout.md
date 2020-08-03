---
layout: post
title:  "Android - CoordinatorLayout"
date:   2020-08-03 12:20:17 -400
lastmod : 2020-08-03 12:20:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
---

## CoordinatorLayout
CoordinatorLayout은 주된 두가지의 역할을 가지고 있습니다.  
1. 최상위 어플리케이션 장식 혹은 chrome layout으로 사용됩니다.  

2. 자식 뷰들과 상호작용하기 위한 컨테이너로 사용됩니다.

- CoordinatorLayout은 자식 뷰들에 대한 `Behavior`를 지정함으로써 자식 뷰들에게 다양한 상호작용을 제공할 수 있습니다. 또한 `Behavior`를 지정하면, 자식 뷰들간의 상호작용을 정의할 수 있습니다.

- CoordinatorLayout의 자식 뷰들은 `@CoordinatorLayout.DefaultBehavior`를 이용하여 디폴트 동작(Behavior)를 가질 수 있습니다.  

- 다양한 동작(Behavior)들이 다양한 상호작용과 레이아웃의 수정을 위해서 사용될 수 있습니다.  
예를 들어서 슬라이딩 드로워와 패널부터 다른 요소들이 움직이거나 애니메이션이 일어날 때, 해당 요소에 붙어서 나타나는 스와이프 디미서블한 요소들


<div>
<img src="https://lh3.googleusercontent.com/X764WfgPllVG50Pl52COwAHHDw57K7j4JDG5fE5hByBfBEWj-WCfjVPfsXPlIDEokVgEwmAscsVKM4gCY1zea717FPgGy0nAXn-A=w1064-v0" alt="drawing" width="500px"/>
<div>

![스와이프 디미서블한 요소들](https://miro.medium.com/max/1000/1*_b108ACcWXgt_N-fXzzGzQ.png){width: 300px;}



- CoordinatorLayout의 자식들은 `anchor`라는 것을 가질 수도 있습니다.
- `anchor`는 플로팅 뷰를 다른 컨텐츠 화면에 띄울 때, 사용되는 뷰입니다.
- `anchor`는 CoordinatorLayout의 자식 뷰여야만 합니다.
- 일반적으로 자기 자신이나, 자신의 자식뷰를 `anchor`로 지정하지 않습니다.

- 또한 자식 뷰들에대해 `CoordinatorLayout.LayoutParams.insetEdge`을 정의함으로써 `CoordinatorLayout`안에서 어떻게 위치할지를 정할 수 있습니다.  

- 게다가 자식 뷰에 `CoordinatorLayout.LayoutParams.dodgeInsetEdges`를 설정해 놓으면, 여러개의 뷰를 동일한 위치로 설정해놓았더라도, 뷰가 겹치지 않도록 뷰들이 이동합니다.

<br/>
<br/>
<br/>
<br/>
