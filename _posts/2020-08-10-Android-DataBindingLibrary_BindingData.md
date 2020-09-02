---
layout: post
title:  "Android - 데이터 바인딩 라이브러리(Data Binding Library): 데이터 바인딩 하기"
date:   2020-08-10 14:12:17 -400
lastmod : 2020-08-10 14:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
---

> 본 포스팅은 Android에서 Android Jetpack 사용 예시로 제공하는 Sunflower 프로젝트를 클론코딩하며 분석하고 있습니다.



`HomeViewPagerFragment`에서 `FragmentViewPagerBinding`을 이용하여 레이아웃 파일을 `inflate` 하고 있었습니다.  `FragmentViewPagerBinding`은 `Data Binding Library`에서 자동으로 생성하는 클래스로, 바인딩 클래스라고 불립니다.  

- HomeViewPagerFragment.kt 코드 중 일부

```
class HomeViewPagerFragment : Fragment() {

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        val binding = FragmentViewPagerBinding.inflate(inflater, container, false)
        val tabLayout = binding.tabs
        val viewPager = binding.viewPager

        return binding.root
    }
}
```

<br/>
데이터 바인딩 라이브러리는 레이아웃의 변수 및 뷰에 액세스하는 데 사용되는 바인딩 클래스를 자동으로 생성합니다.  

바인딩 클래스는 데이터 바인딩을 사용하는 프로젝트 내에서, 각 레이아웃 파일마다 생성됩니다. 바인딩 클래스의 이름은 기본적으로 레이아웃 파일의 이름을 파스칼 표기법으로 변환한 후, Binding이라는 단어를 붙여서 만듭니다. 예를 들어서, 레이아웃 파일 이름이 `activity_main.xml`이라면, 생성되는 클래스이름은 `ActivityMainBinding`입니다.

바인딩 클래스에는 레이아웃 속성(예: 레이아웃 파일에서 사용하는 변수)부터 시작하여, 레이아웃에 존재하는 뷰까지 모든 결합을 포함하고 있으며, 이 결합을 이용하여 표현식의 값을 할당할 수 있습니다.  
데이터 바인딩 라이브러리를 사용하기 위해서는, 바인딩 클래스의 객체인 바인딩 객체를 만들어야 합니다.

바인딩 클래스는 데이터 바인딩 라이브러리에서 만들어 주기 때문에, 우리는 바인딩 객체만 만들면 됩니다.  
권장되는, 바인딩 객체 생성 방법은 다음 예시 코드와 같이 레이아웃을 `inflate`하는 동안 생성하는 것 입니다.

```
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    val binding: ActivityMainBinding = DataBindingUtil.setContentView(
            this, R.layout.activity_main)

    binding.user = User("Test", "User")
}

```

<br/>
<br/>

### 바인딩 객체 만들기
데이터 바인딩 객체가 레이아웃내의 표현식과 결합하기 전에 뷰 계층구조가 수정되지 않도록 하기 위해서, 레이아웃이 inflate된 후에 바로 데이터 바인딩 객체가 생성됩니다.  
(데이터 바인딩 객체와 바인딩 객체는 같은 의미이며, 데이터 바인딩 클래스와 바인딩 클래스는 같은 의미입니다. 편의를 위해 번갈아 가며 쓰도록 하겠습니다.)  

위에서 바인딩 객체 생성방법으로 제시된 예제 코드와 Sunflower 프로젝트의 HomeViewPagerFragment에서 바인딩 객체를 생성하는 방법이 서로 다른 것을 확인할 수 있습니다.  
바인딩 객체를 만드는 방법은 여러가지가 존재합니다.  

가장 일반 적인 방법은 데이터 바인딩 클래스의 정적 메서드를 이용하는 것입니다.   
아래의 예제 코드처럼 바인딩 클래스의 `inflate()`메서드를 이용하여 바인딩 객체를 생성할 수 있습니다.  
```
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    val binding: MyLayoutBinding = MyLayoutBinding.inflate(layoutInflater)

    setContentView(binding.root)
}
```

`layoutInflater`대신에 `ViewGroup`객체를 인자로 받는 `inflate()`함수도 존재합니다.  
```
val binding: MyLayoutBinding = MyLayoutBinding.inflate(getLayoutInflater(), viewGroup, false)
```

레이아웃이 다른 매커니즘을 이용하여 inflate되었다면, 다음과 같이 별도로 bound될 수 있습니다.
```
val binding: MyLayoutBinding = MyLayoutBinding.bind(viewRoot)
```

데이터 바인딩 타입을 사전에 알 수 없는 상황도 존재합니다. 그런 경우에는 `DataBindingUtil` 클래스를 사용하여 데이터 바인딩 객체를 생성할 수 있습니다.
```
val viewRoot = LayoutInflater.from(this).inflate(layoutId, parent, attachToParent)
val binding: ViewDataBinding? = DataBindingUtil.bind(viewRoot)
```

`Fragment`, `ListView`, `RecyclerView`의 아이템에서 데이터 바인딩을 사용한다면, 다음 예시와 같이 바인딩 클래스의 `inflate()`메서드 또는 `DataBindingUtil`클래스의 `inflate()`함수를 사용할 수 있습니다.  
```
val listItemBinding = ListItemBinding.inflate(layoutInflater, viewGroup, false)
// or
val listItemBinding = DataBindingUtil.inflate(layoutInflater, R.layout.list_item, viewGroup, false)
```


### 변경되지 않는 뷰 필드
데이터 바인딩 라이브러리는 레이아웃 내에 ID를 가지고 있는 모든 뷰에 대해서 각각 뷰에 대응되는 변하지 않는 필드를 바인딩 클래스 내에 생성합니다.  

예를 들어서 레이아웃 파일에서 다음과 같이 `id` 값을 가지는 두개의 `TextView`가 존재한다고 할 때,   
데이터 바인딩 라이브러리는 `TextView`타입의 `firstName`과 `secondName`(id 값)라는 불변 필드를 생성합니다.  
```xml
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
           android:text="@{user.firstName}"
   android:id="@+id/firstName"/>
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.lastName}"
  android:id="@+id/lastName"/>
   </LinearLayout>
</layout>
```
