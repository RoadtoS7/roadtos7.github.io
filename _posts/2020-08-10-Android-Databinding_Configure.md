---
layout: post
title:  "Android - 데이터 바인딩 라이브러리(Data Binding Library): 환경 설정 및 예시"
date:   2020-08-10 14:12:17 -400
lastmod : 2020-08-10 14:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
---

이 포스팅은 Android Architecture Components 중 하나인 데이터 바인딩 라이브러리(Data Binding Library)에 대한 시리즈 포스팅 중 두번째 포스팅에 해당합니다.  
데이터 바인딩 라이브러리에 대해서 처음 접하시는 분들은 첫 번째 포스팅인 [데이터 바인딩 라이브러리(Data Binding Libarary) 소개](https://roadtos7.github.io/android/2020/08/07/Android-DataBindingLibrary.html)를 참고 부탁드립니다. :smile:


데이터 바인딩 라이브러리는 UI와 데이터를 연결하는데 기존의 프로그래밍적인 방식이 아니라, 선언적인 방식으로 이를 가능하게 합니다.
게다가, 성능이 뛰어나고, 유지보수가 쉬우며, 테스트가 용이한 앱을 만드는데 도움을 주는 라이브러리 입니다.  

이번 포스팅에서는 데이터 바인딩 라이브러리를 사용하기 위한 환경설정 방법에 대해 다뤄보도록 하겠습니다.

## 데이터 바인딩 라이브러리 환경설정
데이터 바인딩 라이브러리를 사용하기 위해서는 앱 모듈의 `build.gradle`파일에 다음과 같이 `dataBinding` 요소를 추가합니다.
```
android {
        ...
        dataBinding {
            enabled = true
        }
    }

```

## 데이터 바인딩을 사용하기 위한 레이아웃 파일 설정 및 바인딩 표현식
바인딩 표현식을 사용하면, 뷰에 의해 전달된 이벤트를 처리하는 표현식을 작성할 수 있습니다.  

데이터 바인딩을 사용하기 위해서는 레이아웃의 루트 요소가 `<layout>`태그여야 합니다.  
그리고 `<data>`태그와 뷰들이 이어서 나타납니다.   
코드로 나타내면 다음과 같습니다.  

```xml
<?xml version="1.0" encoding="utf-8"?>
    <layout xmlns:android="http://schemas.android.com/apk/res/android">
       <data>
           <variable name="user" type="com.example.User"/>
       </data>
       <LinearLayout
           android:orientation="vertical"
           android:layout_width="match_parent"
           android:layout_height="match_parent">
           <TextView android:layout_width="wrap_content"
               android:layout_height="wrap_content"
               android:text="@{user.firstName}"/>
           <TextView android:layout_width="wrap_content"
               android:layout_height="wrap_content"
               android:text="@{user.lastName}"/>
       </LinearLayout>
     </layout>
```

루트 태그로 `<layout>`이 존재하고, `<layout>`태그가 레아이웃 내의 모든 요소들을 감싸고 있습니다. `<layout>`태그 내에는 `<data>`태그가 나타나고 이어서 뷰를 나타내는 태그들을 나타나는 것을 볼 수 있습니다.   

<br/>
<br/>

### data 태그 용도
`<data>`태그 내에는 이 레이아웃 파일 내에서 사용할 변수를 지정합니다.
위 예제 코드에서는 `User`타입의 `user`라는 이름의 변수를 지정했습니다.

<br/>

### 표현식 사용 방법
레이아웃 파일내에서 표현식은 `'@{}'`를 사용하여 작성됩니다.
```xml
<TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:text="@{user.firstName}" />
```
위 코드는 `TextView`의 텍스트 속성을 `user`변수의 `firstName`속성으로 지정하고 있습니다.   


## 정리
- 데이터 바인딩을 사용하기 위해서는 레이아웃 파일(xml)내에 루트 요소로 `<layout>`태그가 존재해야 합니다.

- `<layout>`태그 다음으로는 `<data>`태그와 레이아웃 파일에서 보여주고자 하는 뷰를 배치합니다.  

- `<data>`태그 안에는 해당 레이아웃 파일 내에서 사용하고자 하는 변수를 지정합니다.  

- 데이터 바인딩 표현식은 `@{}`를 사용하여 중괄호 내에 작성합니다.  

- 데이터 바인딩 표현식을 통해 `<data>`태그 내에서 지정한 변수값을 가지고 뷰의 속성에 지정할 수 있습니다.  

다음 포스팅에서는 [sunflower 프로젝트](https://github.com/android/sunflower)에서 데이터 바인딩 라이브러리를 사용하여 레이아웃과 코틀린 파일 간의 바인딩 작업을 살펴보도록 하겠습니다.  
