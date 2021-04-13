---
layout: post
title: "Android - Context"
date: 2021-02-05 18:42:17 -400
lastmod: 2021-02-05 18:42:17 -400
sitemap:
  changefreq: daily
  priority: 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

Android 앱을 만들다보면 `Context`라는 것을 사용하는 경우가 매우 많다. 
과연 `Context`란 무엇일까?

## :santa_claus: Context
- `Context`는 애플리케이션의 현재 상태에 대한 맥락(Context)이다.
- 액티비티나 애플리케이션에 대한 정보를 얻기 위해서 사용될 수 있다.
- 리소스나 데이터베이스, SharedPreferences 등에 대한 접근을 얻기 위해서 사용될 수 있다.
- Activity와 Applicaiton 클래스 모두 Context 클래스를 상속받는다.

`Context`는 애플리케이션의 거의 모든 곳에서 사용된다. 그리고 안드로이드 개발에서 가장 중요한 것이다. 따라서 `Context`를 정확하게 이해해야 한다.  

`Context`의 잘못된 사용은 애플리케이션의 메모리 누수를 일으킬 수 있다.  

매우 다양한 종류의 `Context`가 존재하기 때문에 안드로이드 개발자들은 이 `Context` 간에 어떤 곳에서 어떤 것을 써야할지 자주 혼동된다.  

## Context의 종류
### 1. Application Context
- 액티비티 내에서 `getApplicationContext()` 메서드를 통해서 얻어올 수 있다.
- 싱글톤 객체이다.
- 이 객체는 애플리케이션의 라이프 사이클과 연결되어 있다.
- 앱에서 현재 컨텍스트와 분리된 라이프사이클을 가진 Context가 필요하거나 혹은 액티비티의 범위를 넘어서 Context를 전달할 때 Application Context를 사용할 수 있다.

- 사용 예시: 애플리케이션을 위한 싱글톤 객체를 생성해야 하며 그 객체가 Context 필요하다면, application Context를 사용하면 된다.  
이때 Activity Context를 사용한다면, 메모리 누수로 이어질 수 있다. 왜냐하면 해당 싱글톤 객체는 액티비티를 계속해서 참조할 것이고, 참조된 액티비티는 가비지 컬렉션이 이루어지지 못하게 되기 때문이다.  

- 액티비티에서 라이브러리를 초기화해야 한다면, 해당 액티비티의 Context이 아닌 Application Context를 이용해야 한다.

- 그 어떤 Context 보다도 오래 유지되는 Context가 필요한 경우에만 `getApplicationContext()`를 사용해야 한다.

### 2. Activity Context
- 이 Context는 액티비티 내에서만 사용 가능하다.
- Activity Context는 액티비티와 연결되어 있다.
- Activity Context는 Activity 범위 내에서 Context를 전달해야 할 때, 혹은 현재 Context와 연결된 라이프 사이클을 가진 Context가 필요할 때 사용되어야 한다.

- 사용 예시: 라이프사이클이 액티비티와 연결된 객체를 만들어야 한다면 activity context를 사용할 수 있다.

## 정리 
정리하면, 앱의 계층은 다음과 같이 생겼다.
  
![앱의 계층](https://s3.ap-south-1.amazonaws.com/mindorks-server-uploads/context-app-activity-hierarchy.jpg)
- MyApplication 클래스에는 Application Context만 존재한다. 따라서 Application 클래스와 가장 가까운 Context는 Application Context이다.
- MainActivity1에는 Application Context와 Activity Context 둘다 존재한다. 가장 가까운 Context는 Activity Context이다.
- MainActivity2에는 Application Context와 Activity Context 둘다 존재한다. 가장 가까운 Context는 Activity Context이다.

## ContentProvider에서 getContext()
- ContentProvider에서 getContext() 메서드 결과로 반환되는 것은 Application Context이다.
- 따라서 ContentProvider내에서 Application Context를 사용할 수 있다.

## 언제 어떤 Context를 사용해야 할까?
예를 들어서 살펴보자.
Application 클래스를 상속하는 MyApplication 클래스를 가지고 있다고 가정하자.  
그리고 MyDB라는 싱글톤인 클래스도 가지고 있을 때, MyDB가 context가 필요하다고 하자. MyDB를 생성하기 위해서는 어떤 Context가 필요할까?  

    답은 Application Context이다. 
    왜냐하면 Activity Context(예를 들어서 MainActivity의 Context라고 하자)를 이용하여 MyDB를 생성한다면, MainActivity가 사용되고 있지 않을 때에도 MyDB에서 계속해서 MainActivity를 참조하기 때문에 메모리 누수가 발생하게 된다.  
    
따라서 SinglneTon의 경우에는 라이프 사이클이 Application 라이프 사이클에 연결이 되기 때문에 항상 Application Context를 사용해야 한다.

반면에 액티비티 내에서 UI작업의 경우, 예를 들어서 토스트나 다이럴로그 등을 띄우는 것은 Activity Context를 사용한다.  

## `getApplicationContext()` 메서드를 쓰지 않아야 할 때는 언제일까?
- Application Context는 액티비티가 하는 모든 것들을 지원하는 Context가 아니다.  
`ApplicationContext`를 가지고 하려는 우리의 많은 작업이 실패할 것이고, 주로 실패하는 것들은 GUI와 관련된 것이다.
- 만약 컨텍스트에서 사용자가 정리하지 않은 호출에 의해 만들어진 내용을 Applicaiton Context가 가지고 있으면, 메모리 누수가 발생할 수 있다.  
액티비티가 무언가를 가지고 있을때, 액티비티가 가비지 컬렉팅되면, 액티비티가 가지고 있는 모든 것들도 메모리에서 사라진다.  
반면에 Application 객체는 우리 프로세스의 생명주기동안 유지된다는 것을 염두해두고 있어야 한다.

## 엄지 손가락의 법칙(The Rule of Thumb)
- 대부분의 경우에는 우리가 작업하고 있는 구성요소에서 바로 사용할 수 있는 Context를 사용하면 된다.
- 그 Context에 대한 참조가 현재 컴포넌트의 라이프 사이클보다 더 오랫동안 유지 되지 않는다면, 안전하게 해당 Context를 참조할 수 있다.
- 액티비티나 서비스의 수명주기보다 오래 유지되는 객체에서 안전하게 Contenxt를 참조하려면, 비록 잠시동안만 Context를 참조하는 것이라도, Applicatoin Context를 참조하는 것이 좋다.

> 이 포스팅은 MindOrk의 [Android Application의 Context에 대한 이해](https://blog.mindorks.com/understanding-context-in-android-application-330913e32514) 포스팅을 참조하여 작성된 포스팅입니다.        

  
