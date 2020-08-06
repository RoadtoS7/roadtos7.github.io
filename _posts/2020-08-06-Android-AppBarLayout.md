---
layout: post
title:  "Android - AppBarLayout"
date:   2020-08-06 22:12:17 -400
lastmod : 2020-08-06 22:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
---

## AppBarLayout이란?
`AppBarLayout`은 스크롤 제스쳐에 따라서 AppBar가 사라지거나 줄어드는 등, AppBar와 관련된 여러가지 동작을 정의할 수 있도록 도와주는 레이아웃입니다.

AppBarLayout 은 수직적인 방향성만을 가진 (oritented 속성이 verticall) LinearLayout이라서  AppBarLayout의 자식뷰들은 세로로 차례대로 배치됩니다. 또한, material design app bar의 많은 특징을 구현하고 있어, 스크롤링 제스쳐들과 관련된 많은 기능들을 구현하고 있습니다.  

<br/>

AppBarLayout도 Layout이기 때문에 다른 Layout들과 마찬가지로 ViewGroup에 해당합니다.  
AppBarLayout의 자식 뷰들에게 스크롤 제스처에 따라서 해당 뷰들이 어떻게 동작할지를 지정해줘야 합니다.  
자식 뷰들의 동작은 코드 상에서 setScrollFlags(Int)로 지정하거나 혹은 xml상에서 app:layout_crollFlags로 지정할 수 있습니다.

<br/>

게다가 AppBarLayout이 CoordinatorLayout의 자식 뷰(정확히는 직계 자식 뷰)로 사용될때, AppBarLayout의 대다수의 기능을 사용할 수 있습니다. CoordinatorLayout이 아닌 다른 ViewGroup의 자식 뷰로서 존재한다면, 대부분의 기능을 사용할 수 없습니다.  
(직계자식 뷰란, 특정 ViewGroup의 자식이면서, 해당 ViewGroup만을 하나의 부모로 두는 뷰를 말합니다.)

<br/>

AppBarLayout은 언제 스크롤 동작이 일어나는지 알기 위해서, 스크롤이 되는 뷰를 형제 뷰로 가져야합니다.  
스크롤이 되는 형제 뷰와 AppBarLayout은 바인딩이 되어야하는데, 바인딩 작업은 `AppBarLayout.ScrollingViewBehavior` 클래스를 이용하여 이루어집니다. 즉, AppBarLayout은 화면에서 어떤 뷰 스크롤 됨에 따라서 여러가지 동작을 하는데, 스크롤 동작이 일어나는 뷰의 동작(behavior)을 `AppBarLayout.ScrollingViewBehavior`의 인스턴스로 지정해야 합니다.  
지정하는 방법으로는 프로그래밍적으로 코드상에서 지정하거나 혹은 `AppBarLayout.ScrollingViewBehavior`을 String 리소스로 등록한 후, xml상에서 속성으로 지정할 수도 있습니다.  
(형제 뷰란, 같은 부모를 가지면서 부모 아래에서 레벨이 같은 뷰를 말합니다. 쉽게 말하자면, 같은 ViewGroup내에 속하는 뷰들 중 들여쓰기기 된 정도가 같은 뷰를 말합니다.)


차례대로 코드 예시와 함께 다시 알아보도록 하겠습니다.

```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
         xmlns:android="http://schemas.android.com/apk/res/android"
         xmlns:app="http://schemas.android.com/apk/res-auto"
         android:layout_width="match_parent"
         android:layout_height="match_parent">

     <androidx.core.widget.NestedScrollView
             android:layout_width="match_parent"
             android:layout_height="match_parent"
             app:layout_behavior="@string/appbar_scrolling_view_behavior">

         <!-- Your scrolling content -->

     </androidx.core.widget.NestedScrollView>

     <com.google.android.material.appbar.AppBarLayout
             android:layout_height="wrap_content"
             android:layout_width="match_parent">

         <androidx.appcompat.widget.Toolbar
                 ...
                 app:layout_scrollFlags="scroll|enterAlways"/>

         <com.google.android.material.tabs.TabLayout
                 ...
                 app:layout_scrollFlags="scroll|enterAlways"/>

     </com.google.android.material.appbar.AppBarLayout>

 </androidx.coordinatorlayout.widget.CoordinatorLayout>
 ```

<br/>

하나씩 떼어서 살펴보도록하겠습니다.
AppBarLayout의 자식 요소들은 스크롤 제스쳐에 따라서 어떻게 동작할지를 코드상에서 지정하거나 혹은 xml 상에서 속성으로 지정해야 한다고 했습니다. 우리가 보는 예시는 xml 예시에 해당하기 때문에 아래와 같이 `app:layout_scrollFlags`속성을 통해서 지정함을 볼 수 있습니다.

```xml
<androidx.appcompat.widget.Toolbar
        ...
        app:layout_scrollFlags="scroll|enterAlways"/>

<com.google.android.material.tabs.TabLayout
        ...
        app:layout_scrollFlags="scroll|enterAlways"/>
```

<br/>

스크롤 제스쳐에따른 자식 뷰들의 동작 플래그는 `|` 기호를 이용하여 여러개를 나열할 수 있습니다.

`app:layout_scrollFlags="scroll|enterAlways`에서 `scroll`은 스크롤 제스쳐에 따라 영향을 받는 것을 의미합니다. 즉, 스크롤 제스처에 따라서 해당 뷰가 특정한 움직임을 하거나 사라지는 등의 작업을 하고 싶다면 `scroll`속성을 부여한 후, 원하는 동작에 대한 플래그를 추가해야 합니다. 만약 `scroll`속성을 부여하지 않는다면, 이후에 어떤 동작 플래그를 추가하든 간에 스크롤 제스처가 일어나도 아무런 동작도 하지 않습니다.  

<br/>

`app:layout_scrollFlags="scroll|enterAlways`에서 `enterAlways`의 의미는 해당 동작 플래그가 지정된 자식뷰는 스크롤 제스쳐가 입력되었을 때, 앞서서 언제 스크롤이 일어나는지를 판단하기 위한 기준으로 지정한 형제 뷰가 스크롤 되는지 여부에 관계없이, 스크롤 된다는 것입니다.  

<br/>

아래 예시 코드를 통해서 실제 코드에서 `app:layout_scrollFlags`의 값에 따라 자식 뷰들이 어떻게 동작하는지 살펴보도록 하고 여기서는 AppBarLayout의 자식 뷰들에는 `scrollFlags`속성을 지정해야 한다는 것만 알고 넘어가도록 하겠습니다.  

<br/>

두번째로 `AppBarLayout`은 `CoordinatorLayout`의 자식뷰로 사용될 때, `AppBarLayout`의 대다수의 기능을 사용할 수 있다고 했습니다. 위의 예시 에서도 마찬가지로 `AppBarLayout`이 `CoordinatorLayout`의 자식뷰로 사용되고 있음을 확인할 수 있습니다.  

<br/>

마지막으로, `AppBarLayout`은 스크롤 동작이 일어나는지 알기 위해, 스크롤이 되는 형제뷰를 가져야합니다. 또한 둘간의 바인딩이 이루어지기 위해서 스크롤이 되는 형제뷰의 `behavior`속성으로 `AppBarLayout.ScrollingViewBehavior`인스턴스를 지정하여 둘간의 바인딩을 해줘야 합니다.

```xml
<androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
```
위 예시에서는 바인딩 방법 중 `AppBarLayout.ScrollingViewBehavior`클래스 이름을 String 리소스로 만든 후, `app:layout_behavior`의 속성으로 지정하는 방법을 사용하여, 형제 뷰와 `AppBarLayout` 간에 바인딩을 해주었습니다.  

정리하자면 `AppBarLayout`은 스크롤 제스쳐에 따라서 `App`

<br/>


다음으로는 화면이 스크롤될 때 AppBarLayout이 사라지는 효과를 주기 위해 사용되는  `CollapsingToolBarLayout`에 대해서 알아보도록 하겠습니다.  


## CollapsingToolBarLayout 이란?
`CollapsingToolBarLayout`이란 이란 `Toolbar`의 wrapper이기 때문에 자식으로 항상 `Toolbar`를 가지며, 부모로는 항상 `AppBarLayout`을 가집니다.  
스크롤 되었을 때 사라지는 AppBar를 구현할 때 쓰입니다.  

또한 `CollapsingToolBarLayout`에서는 AppBar가 사라질 때 자식 뷰들이 다양한 동작을 할 수 있는 기능을 제공한다. 예를 들어서, 자식 뷰가 collpase mode로 `COLLAPSE_MODE_PARALLAX`를 갖는다면, 현재 화면에서 자식뷰가 보이는 부분이 전체의 몇퍼센트인지에 따라서 자식 뷰가 완전히 스크롤 되어 사라질지 아니면 완전히 보여질지를 결정할 수 있다.  







- 참고자료:
[AppBarLayout에 대한 안드로이드 공식 문서](https://developer.android.com/reference/com/google/android/material/appbar/AppBarLayout)
