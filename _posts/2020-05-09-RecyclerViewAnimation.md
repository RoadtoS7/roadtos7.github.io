---
title:"RecyclerView에 애니메이션 효과주기"
date: "2020-05-09 17:55:17 -400"
categories: Android
---

## RecyclerView
RecyclerView는 반복되는 아이템 목록을 뷰로 나타낼 때 쉽게 구현할 수 있도록 도와줍니다.  
ListView에서 발전된 형태로 알려져 있습니다.  
RecyclerView에도 애니메이션을 추가할 수 있다는 사실을 알고 계신가요?  
이번 포스팅에서는 RecyclerView에 애니메이션을 추가하는 "간단한" 방법에대해서 알아보도록 하겠습니다.  

## RecycleView에 애니메이션 추가하기
RecyclerView에 애니메이션을 추가하는 방법은 여러가지가 있습니다.
예를 들어서
- Custom ItemAnimator 를 구현합니다.  
- RecyclerView Adapter의 onBindViewHolder() 함수에 애니메이션을 핸들링 하는 코드를 구현합니다.  
이와 같은 방법이 있습니다.  

하지만 우리는 이와는 다른 방법에 대해서 알아보려고 합니다!  
바로 LayoutAnimation 을 사용해서 애니메이션을 적용하는 것 입니다.  
이 방법은 쉽우면서도 구현하는데 필요한 코드는 몇줄 되지 않습니다.  
그리고 RecyclerView 뿐만 아니라 ViewGroup의 하위 클래스에 해당하는 모든 View들에 사용할 수 있습니다.  


## LayoutAnimation 을 이용하기
구현하는 방법은 간략히 다음 순서와 같습니다.  
1. 각 아이템에 대하여 애니메이션을 정의합니다.
2. 정의한 애니메이션을 이용하여 LayoutAnimation 을 만듭니다.  
3. 2번에서 만든 LayoutAnimation 을 이요하여 프로그래밍 적으로, 그리고 XML안에 적용합니다.  

## 위에서 아래로 아이템이 나타나는 애니메이션
우선 위에서 아래로 아이템이 나타나는 애니메이션부터 살펴보겠습니다.  

res/anim 폴더를 만들고 다음과 같은 코드를 가지는 xml파일을 만들어주세요.  

```

```
위코드가 애니메이션에 대한 코드 입니다.







