---
title: "ReactiveX 03- 뜨거운 Observable & 차가운 Observable"
time: "2020-04-03 15:36:17 -400"
categories: ReactiveX
---

# 뜨거운 Observable과 차가운 Observable
Observable에는 뜨거운 Observable과 차가운 Observable이 존재합니다.  
뜨거운 Observable은 생성과 동시에 데이터 발행을 시작합니다.  
따라서 뜨거운 Observable이 데이터를 발행한 이후에 구독한 Observer는 구독시점 이후에 발행된 데이터부터 관찰할 수 있습니다.  
즉, 구독시점 이전에 발행된 데이터는 놓치게 됩니다.  

반면에 차가운 Observable은 자신을 구독하는 Observer가 생길 때까지 아이템을 발행하지 않습니다.  
따라서 Observable이 발행하는 데이터 시퀀스를 처음부터 관찰할 수 있습니다.  

뜨거운 Observable에서는 배압에 대해서 주의해야합니다.
배압은 Observable이 데이터를 발행하는 속도와 구독자가 데이터를 받아서 처리하는 처리하는 속도 차이가 클 때 발생합니다.  

배압을 처리하는 클래스는 Flowable 클래스로 추후 포스팅에서 더 자세히 다루도록 하겠습니다.  
그때까지는 뜨거운 Observable을 사용할 때는 배압에 대해서 주의해야하며, Flowable 클래스가 배압 처리를 한다고 기억하시면 됩니다.  

