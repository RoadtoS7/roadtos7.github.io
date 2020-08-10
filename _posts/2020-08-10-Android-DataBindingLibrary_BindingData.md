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

> 본 포스팅은 Android에서 Android Jetpack 사용 예시로 제공하는 Sunfolwer 프로젝트를 클론코딩하며 분석하고 있습니다.



`HomeViewPagerFragment`에서 `FragmentViewPagerBinding`을 이용하여 레이아웃 파일을 `inflate` 하고 있었습니다.  `FragmentViewPagerBinding`은 `Data Binding Library`에서 자동으로 생성하는 클래스로, 바인딩 클래스라고 불립니다.  

- HomeViewPagerFragment.kt 코드

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
데이터 바인딩 라이브러리는 레이아웃의 변수 및 뷰에 액세스하는 데 사용되는 바인딩 클래스를 생성합니다.  

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

### 바인딩 객체 만들기
위에서 바인딩 객체 생성방법으로 제시된 예제 코드와 
