---
layout: post
title:  "Kotlin - sealed"
date:   2021-01-18 14:41:17 -400
lastmod : 2021-01-18 14:41:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Language
background: "/img/classic_blue.jpeg"
---

## `Sealed`

클래스의 유형중 하나로 `sealed` 클래스가 존재한다.  
sealed는 closed 타입을 정의한다. 즉, sealed 클래스를 상속받는 모든 자식 클래스는 모두 사전에 sealed 클래스 내부에서 정의해야 한다.  
sealed 클래스를 사용하면 컴파일러가 `sealed`클래스의 자식 클래스를 사전에 알 수 있다.  
또한 sealed 클래스 외부에서는 자신을 상속받는 자식 클래스를 둘 수 없기 때문에, 데이터 타입을 제한시켜야 할 경우 사용하기 좋다.   
예를 들어서, Android에서 RecyclerView의 어댑터의 경우 RecyclerView에서 뷰로 나타낼 데이터를 사전에 지정해두어야 한다.  
이때 RecyclerView에서 사용될 데이터의 형태가 다양할 경우, 모두 sealed class로 묶어서 하나의 데이터 묶음으로 나타낼 수 있다.

- 사용 예시
```Kotlin
sealed clas DataItem{
    abstract val id: Int
    data class Message(
        override val id: Int, 
        val message: String
    ): DataItem()
    object Header: DataItem(){
        override val id: Int
    }   
}
``` 




