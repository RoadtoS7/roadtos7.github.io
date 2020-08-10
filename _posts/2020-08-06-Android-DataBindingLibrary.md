---
layout: post
title:  "Android - 데이터 바인딩 라이브러리(Data Binding Library) 소개"
date:   2020-08-06 22:12:17 -400
lastmod : 2020-08-10 11:59:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
---

## Data Binding Library (데이터 바인딩 라이브러리) 란?
한국말로 "데이터 결합 라이브러리" 라고도 불리는 데이터 바인딩 라이브러리(Data Binding Library)는 프로그래밍적인 방식이 아니라 선언적인 방식으로 UI와 데이터를 연결시킬 수 있도록 하는 라이브러리 입니다.  
데이터 바인딩 라이브러리는 Android Architecture Components의 일환으로 등장하여, 유지보수가 쉽고, 테스트가 용이한 앱을 만드는데 도움을 줍니다.  

액티비티 코드에서 UI를 구성요소들은 UI 프레임워크 메서드 내에 다음과 같이 정의됩니다.
```kotlin
findViewById<TextView>(R.id.sample_text).apply {
    text = viewModel.userName
}
```

다음 예시 코드는 위의 코드를 데이터 바인딩 라이브러리를 사용한 것으로 바꾼 것입니다.
xml 레이아웃 파일에서 뷰에 데이터를 직접 할당할 수 있습니다. 이 방법을 사용하면 액티비티 코드에서처럼 findViewById() 호출할 필요가 없습니다.  

```xml
<TextView
    android:text="@{viewmodel.userName}" />
```

데이터 바인딩 라이브러리를 사용하면, 액티비티 코드 상에서 UI프레임워크 호출을 없앨 수 있어, 파일이 단순해지고 유지보수하기 쉬워집니다. 또한, 앱의 성능이 향상되며, 메모리 누수나 null point exception을 예방할 수 있습니다.  


### 데이터 바인딩 라이브러리를 사용하면 다음과 같은 기능을 사용할 수 있습니다.
1. 레이아웃과 바인딩 표현식  
- 표현식 언어를 이용하여, 레이아웃에 뷰와 변수를 연결하는 표현식을 작성할 수 있습니다.  
- 데이터 바인딩 라이브러리는 레이아웃의 뷰와 데이터 객체들을 연결하는 데 필요한 클래스를 자동으로 생성합니다. 또한, 레이아웃에서 임포트, 인클루드, 변수과 같은 기능들을 레이아웃 내에서 사용할 수 있도록 합니다.
- 이러한 기능들은 기존의 레이아웃 내에서 사용할 수 있습니다.  
예를 들어서, 레이아웃 내의 표현식에서 사용할 수 있는 결합 변수는, `data`요소 내에서 정의됩니다.
`data`요소는 레이아웃에서 루트 레벨에 존재하는 UI 요소와 같은 레벨에 존재해야 합니다.
- 게다가, 두 요소는 모두 `layout`태그로 감싸져야 합니다.  
- 예시 코드
```xml
<layout xmlns:android="http://schemas.android.com/apk/res/android"
          xmlns:app="http://schemas.android.com/apk/res-auto">
      <data>
          <variable
              name="viewmodel"
              type="com.myapp.data.ViewModel" />
      </data>
      <ConstraintLayout... /> <!-- UI layout's root element -->
  </layout>
```

2. 옵저버블 객체를 이용한 작업
- 데이터 결합 라이브러리는 데이터 변화를 쉽게 탐지할 수 있도록 도와주는 클래스와 메서드를 제공합니다.
- 이 클래스와 메서드를 이용하면, 데이터 변화에 따라서 UI를 변경하는 것에 신경쓰지 않아도 됩니다.  
- 또한, 변수, 속성, 객체, 컬렉션 등 다양한 타입의 데이터를 관찰가능하게(Observable)하게 만들어줍니다.

3. 바인딩 어댑터(Binding Adapter)
- 모든 레이아웃 표현식에는 해당 속성이나 수신기를 설정하는 데 필요한 프레임워크 호출을 만드는 바인딩 어댑터가 있다.
- 예를 들어서 바인딩 어댑터는 `setText()`를 호출하여 텍스트 뷰에 텍스트 데이터를 설정하거나, `setOnClickListener()`를 호출하여 리스너를 클릭 이벤트에 추가할 수 있습니다.  
- `android:text` 속성에 대한 바인딩 어댑터와 같이, 자주 쓰이는 어댑터들은 `android.databinding.adapters` 패키지에서 꺼내서 사용할 수 있습니다.  
- `android.databinding.adapters`패키지에 존재하는 어댑터들은 [구글의 어댑터 Git 페이지](https://android.googlesource.com/platform/frameworks/data-binding/+/refs/heads/studio-master-dev/extensions/baseAdapters/src/main/java/androidx/databinding/adapters)를 참조하세요.
- 이와 같이 제공되는 바인딩 어댑터를 사용할 수도 있고, 아래 예시와 같이 커스텀 어댑터를 생성하여 사용할 수도 있습니다.
```kotlin
@BindingAdapter("app:goneUnless")
fun goneUnless(view: View, visible: Boolean) {
    view.visibility = if (visible) View.VISIBLE else View.GONE
}
```

3. 레이아웃 뷰와 아키텍처 구성요소들 연결
- Android Support Library에는 성능이 뛰어나고, 테스트와 유지관리가 쉬운 앱을 만드는데 사용할 수 있는 (Android Architecture Components)[https://developer.android.com/topic/libraries/architecture]가 존재합니다.  
- 이 아키텍처 구성요소를 데이터 바인딩 라이브러리와 함께 사용한다면, UI 개발을 한층 더 단순화 시킬 수 있습니다.  

4. 양방향 데이터 바인딩
- 데이터 바인딩 라이브러리는 양방향 데이터 바인딩을 지원합니다. 양방향 데이터 바인딩은  데이터 변경을 수신받음과 동시에, 사용자가 하는 업데이트를 리스닝하는 것을 할 수 있도록 합니다.  


다음 포스팅에서는 본격적으로 데이터 바인딩 라이브러리를 사용하기 위한 프로젝트 환경설정에 대해서 다뤄보도록 하겠습니다.  

<br/>
<br/>

참고: https://developer.android.com/topic/libraries/data-binding
