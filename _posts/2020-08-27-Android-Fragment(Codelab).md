---
layout: post
title:  "Android - Create a fragment(Codelabs)"
date:   2020-08-24 17:12:17 -400
lastmod : 2020-08-24 17:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

> 이 포스팅은 구글 Codelabs에서 "Create a Room fragment"을 공부하며 작성한 포스팅입니다.

## Fragment
프레그먼트는 액티비티의 유저 인터페이스의 일부분이나 동작을 나타냅니다.  
우리는 한 액티비티 안에 여러 분할 UI를 만들기 위해서 여러 프레그먼트들을 결합할 수 있습니다.   
그리고 하나의 프레그먼트를 여러 액티비티에서 재사용할 수 있습니다.

프레그먼트를 다른 액티비티에서 재사용할 수 있는 서브 액티비티로서 볼 수 있는 이유는 다음과 같은 특징을 갖기 때문입니다.  

- 프레그먼트는 자신만의 생명주기를 가지고, 자신만의 입력 이벤트를 받습니다.

- 액티비티가 작동하는 동안에 프레그먼트를 추가하거나 제거할 수 있습니다.

- 프레그먼트는 코틀린 클래스로 정의되어있습니다.

- 프레그먼트의 UI는 XML 레이아웃 파일로 정의되어 있습니다.

## Fragment의 onCreate() 함수 의미
1. onCreate()
  - 프레그먼트 파라미터들과 companion object 초기화를 담당합니다.

## Fragment에서 kotlin 파일과 xml파일을 연결하기 위해서 데이터 바인딩 객체를 만들어야 합니다.
- 이것은 Activity에서 setContentView() 함수와 동일한 역할을 합니다.

- 데이터 바인딩 객체는 `DatabindingUtil.inflate`함수를 호출하여 얻습니다.

- 첫번째 인자로 `LayoutInflater`객체를 넘겨줍니다.

- 두번째 인자로 뷰로 인플레이트 할 XML 레이아웃 리소스를 지정합니다.

- 세번째 인자는 네번째 인자로 true값을 줄 경우 레이아웃 계층에서 부모로 설정할 `ViewGroup`객체를 지정합니다.  
네번째 인자로 false 값을 줄 경우, 레이아웃 계층에서 루트 뷰에 대한 LayoutParams값 집합을 제공하는 객체를 지정합니다.   
세번째 인자의 경우 optional 파라미터로 지정하지 않아도 괜찮습니다.

- 네번째 인자는 인플레이트된 뷰들의 계층관계가 세번째 인자에 붙여져야하는지를 boolean 값으로 나타냅니다.  
만약 네번째 인자로 false값이 지정된다면, 세번째 인자 값은 XML 리소스에서 루트 뷰에 대한 LayoutParams값을 나타냅니다.

- 일반적으로 프레그먼트에서 사용되는 데이터 바인딩 객체는 다음과 같이 생성됩니다.
  
```Kotlin
override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                         savedInstanceState: Bundle?): View? {
   val binding = DataBindingUtil.inflate<FragmentTitleBinding>(inflater,
           R.layout.fragment_title,container,false)
   return binding.root
   }
```
- `onCreateView()`메서드에서는 인플레이트된 뷰를 담고 있는 `binding.root`를 반환합니다.

## 액티비티 xml 파일에 새로운 프레그먼트 추가하기

- 액티비티의 xml 파일에 `<fragment>` 뷰를 추가합니다.

- 추가한 `<fragment>` `android:id`를 원하는 것으로 지정합니다.

- 추가한 `<fragment>`의 `android:name`을 추가하고자 하는 프레그먼트의 코틀린 파일 경로를 전부 입력합니다.

- 예시 코드는 다음과 같습니다.

````Kotlin
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">
            <fragment
                android:id="@+id/titleFragment"
                android:name="com.example.android.navigation.TitleFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                />
        </LinearLayout>

</layout>
```

## 정리
- 프레그먼트는 액티비티의 모듈 화면과 같습니다.

- 프레그먼트는 자신만의 라이프사이클과 입력 이벤트를 갖습니다.  

- 레이아웃 파일에서 프레그먼트를 위치시키고 싶은 `<fragment>`를 위치시킵니다.

- `onCreateView()`메서드 안에서 프레그먼트의 레이아웃을 인플레이트 합니다.

- 액티비티가 러닝상태일 동안에(동작할 동안에) 프레그먼트를 추가하거나 제거할 수 있습니다.  

<br/>
<br/>

참고한 코드랩 링크: [https://codelabs.developers.google.com/codelabs/kotlin-android-training-create-and-add-fragment/#0](https://codelabs.developers.google.com/codelabs/kotlin-android-training-create-and-add-fragment/#0)
