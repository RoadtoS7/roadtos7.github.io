---
layout: post
title:  "Android - Header in RecyclerView"
date:   2020-12-28 12:22:17 -400
lastmod : 2020-12-28 12:22:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

## :page_with_curl: 리사이클러뷰에 헤더 넣기
- 리사이클러뷰에 헤더를 넣는 것은 기본적으로 리사이클러뷰내 아이템이 똑같은 레이아웃을 사용하는 것이 아니라 **일부는 다른 레이아웃을 사용**하도록 하는 방법을 이용한다.

### :one: 헤더 넣는 코드의 위치
- 리사이클러뷰는 뷰에 해당할 뿐이고, 데이터나 아이템 레아이웃에 대해서 알지 못한다.
- `LayoutManager`의 역할은 아이템을 자신만의 방식대로 배치하는 것이다.
- `Adapter`의 역할은 화면에 표시할 데이터를 조정하고 뷰 홀더를 리사이클러뷰에게 전달하는 것이다.
- 따라서 헤더를 만드는 코드는 `Adpater`에 위치해야 한다.

### :two: 헤더 넣는 방법
- 리사이클러뷰에서 모든 데이터는 인덱스 0번부터 시작하며, 인덱스 번호를 통해 아이템 뷰와 연결된다.
- 다음 그림과 같이, 인덱스 0번 데이터는 인덱스 0번 뷰와 연결되고,  
인덱스 1번 데이터는 인덱스 1번 뷰와 연결된다.

[Data] -> [Item View]  
[0: RecyclerView Data] -> [0: RecyclerView ItemView]  
[1: RecyclerView Data] -> [1: RecyclerView ItemView]  
[2: RecyclerView Data] -> [2: RecyclerView ItemView]  

#### 첫번째 방법
- 따라서 헤더를 표시하는 첫번째 방법은 인덱스를 이용하여 헤더를 위치시킬 위치를 찾은 다음,   
해당 인덱스에서는 다른 ViewHolder를 사용하도록 하는 것이다.
- 이렇게 하면 `Adapter`가 헤더가 표시될 위치를 추적해야 한다.    
(왜냐하면 Adapter에서는 `position` 값 즉, 데이터 인덱스에 따라서 ViewHolder를 다르게 정해줄 수 있기 때문이다.)  
- 예를 들어서 테이블의 가장 상단에 헤더를 표시하고자 한다면, 0번 인덱스 아이템에 대한 ViewHolder를 반환할 때, 헤더 부분을 포함한 ViewHolder를 반환해야 한다.
- 그럼 다음과 같은 형태로 리사이클러뷰 데이터와 아이템간의 매핑이 이루어진다.   

[Data] -> [Item View]  
[0: Header]  
[0: RecyclerView Data] -> [0: RecyclerView ItemView]  
[1: RecyclerView Data] -> [1: RecyclerView ItemView]  
[2: RecyclerView Data] -> [2: RecyclerView ItemView]

#### 두번째 방법
- 앞선 방법이 헤더를 위치시킬 인덱스에 대해서 헤더를 포함한 뷰 홀더를 반환시키는 것이였다면,  
두번째 방법은 리사이클러뷰에 표시될 데이터를 수정시키는 것이다.
- 리사이클러뷰로 나타낼 데이터는 모두 리스트에 저장되어 있다. 따라서 리스트가 헤더를 나타낼 데이터를 포함하도록 수정하면 된다. 
- 이 방법은 위의 방법보다는 이해하기 쉽지만, 리스트 객체 설계 방식을 잘 고려해야한다.
- 설계 방식 중 가장 간단한 것은 헤더를 나타내는 데이터 객체와 나머지 리사이클러뷰 데이터 객체, 
이렇데 다른 2가지의 타입을 하나의 리스트에 두는 것이다.
- 이 방식으로 구현되면, 리사이클러뷰 어댑터는 자신에게 전달된 데이터를 그저 전달된 데이터대로 ViewHolder를 반환하면 된다.
- 다시 말해서, 리스트의 0번째 데이터는 헤더이고, 1번째 데이터는 실제 리사이클러뷰로 표시할 데이터이며,   
이는 리사이클러 뷰와 1대 1로 맵핑되는 형태이다.

[Data] -> [Item View]  
[0: Header] -> [0: Header]   
[0: RecyclerView Data] -> [0: RecyclerView ItemView]  
[1: RecyclerView Data] -> [1: RecyclerView ItemView]  
[2: RecyclerView Data] -> [2: RecyclerView ItemView]

## :three: 정리
- 두 방법은 모두 나름의 장점과 단점이 있다.
- 두번째 방법대로 리사이클러뷰의 리스트 데이터를 바꾸는 것은 `Adapter`코드를 많이 수정하지 않으면서 헤더를 넣을 수 있다.
- 반면에 첫번째 방법대로 인덱스를 사용해서 다른 뷰홀더를 사용하는 것은 헤더 레이아웃을 구성하는데 좀더 자유롭다.  
왜냐하면 리사이클러뷰 리스트 설계를 고려하지 않아도 되기 때문이다.  
    - 또한 리스트 데이터를 수정 없이, `Adapter`가 데이터가 뷰에 보여지는 방식을 처리할 수 있다.


 
