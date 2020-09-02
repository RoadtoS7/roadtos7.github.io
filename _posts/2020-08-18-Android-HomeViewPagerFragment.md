---
layout: post
title:  "Android - Sunflower 분석하기: HomeViewPagerFragment"
date:   2020-08-18 14:12:17 -400
lastmod : 2020-08-18 14:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
---

## TabLayoutMediator
```
// Set the icon and text for each tab
 TabLayoutMediator(tabLayout, viewPager) { tab, position ->
     tab.setIcon(getTabIcon(position))
     tab.text = getTabTitle(position)
 }.attach()
```

`HomeViewPagerFragment`에서는 다음과 같이 `TabLayoutMediator`를 생성하고 `attach()`메서드를 호출하고 있습니다.

`ViewPager2`를 `TabLayout`과 연결할 때 `TabLayoutMeditator`를 사용합니다. `TabLayoutMeditator`는 탭을 선택했을 때, ViewPager2의 위치를 선택한 탭과 동기화하고, 사용자가 `ViewPager2`를 스크롤 할 때 TabLayout의 스크롤 위치를 동기화합니다.

`TabLayoutMediator`는 탭의 움직임에 따라 `ViewPager2`를 조정하기 위해 `OnTabSelectedListener`를 리슨하고 있습니다. 그리고 탭에 표시할 데이터가 변했을 때, 탭을 재생성하기 위해 `RecyclerView`의 `AdapterDataObserver`를 리슨하고 있습니다.  

`TabLayoutMediator`를 이용하여 `ViewPager2`와 `TabLayout`을 연결하기 이위해서는 다음과 같은 절차를 거칩니다.  
1. `ViewPager2`가 어댑터를 가지고 있어야합니다.  

2. `TabLayoutMediator`의 인스턴스를 생성합니다.  

3. `TabLayoutMeditator`의 `attach()`를 호출합니다.

`TabLayoutMediator`의 인스턴스를 생성하는 것은 그저 객체를 생성하는 것일 뿐이고, `attach()` 함수를 호출해야만, `TabLayout`과 `ViewPager2`가 연결됩니다. `HomeViewPagerFragment`에서는 `ViewPager2`와 `TabLayout`을 연결하기 위해 `TabLayoutMediator`를 익명객체로 생성한 후, `attach()`함수를 호출하는 것을 확인할 수 있습니다.  


`TabLayoutMeditator`의 인스턴스를 생성할 때, 연결할 `TabLayout`과 `ViewPager`를 인자로 가질 뿐만 아니라, `TabLayoutMediator.TabConfigure`인터페이스 구현체를 받습니다. 위 코드에서도  `TabLayoutMediator(tabLayout, viewPager)` 이렇게 인자로 받으며, 람다표현식을 이용하여 `TabLayoutMediator.TabConfigure`인터페이스를 구현하고 있습니다.

`TabLayoutMediator.TabConfigure`는 `onConfigureTab` 콜백 메서드를 이용하여, tab의 텍스트를 설정하거나 탭의 스타일링을 합니다. 위 코드에서도 다음과 같이 탭 아이콘을 설정하고, 탭의 택스트를 설정하는 동작을 수행하고 있습니다.
```
tab.setIcon(getTabIcon(position))
tab.text = getTabTitle(position)
```

`ViewPager2`의 어댑터를 수정하기 위해서는 `TabLayoutMeditator`의 `detach()`메서드를 호출한 후 `ViewPager2`어댑터를 수정한 후에 다시 `attach()`메서드를 호출합니다.  

`ViewPager2`나 `TabLayout`을 수정한다면, 새로운 `TabLayoutMediator`를 생성하여 연결작업을 해줘야 합니다.  

## 앱 바 설정하기
```
(activity as AppCompatActivity).setSupportActionBar(binding.toolbar)
```
`setSupportActionBar()`메서드를 이용하여 `toolbar`를 앱바로 설정합니다.
하지만 이 메서드를 호출하는 것 만으로 `toolbar`가 앱바로 설정되는 것은 아닙니다.

### Toolbar를 앱바로 설정하기
`Toolbar`를 앱바로 설정하는 절차  다음과 같습니다.  

1. 액티비티가 다음 예시 코드와 같이 `AppCompatActivity`를 상속받는지 확인합니다.
```
class MyActivity : AppCompatActivity() {
  // ...
}
```
 `Toolbar`를 앱바로 사용하는 경우 모든 액티비티가 `AppCompatActivity`를 상속받아야 합니다.  

 `Sunflower`프로젝트에서는 `HomeViewPagerFragment`의 베이스 액티비티인 `GardenActvitiy`가 다음과 같이 `AppCompatActivity`를 상속받고 있음을 확인할 수 있습니다.  
 ```
class GardenActivity : AppCompatActivity() {
   // ...
}
 ```
2. 매니페스트 파일에서 `NoActionBar`테마 중의 하나를 사용하도록 `<application>` 테마 속성을 설정합니다.  
이렇게 하면, 앱이 네이티브 `ActionBar`클래스를 사용하여 앱바를 제공할 수 없습니다.  
`Sunflower`에서는 `<application>`테마 속성으로 `android:theme="@style/Theme.Sunflower"`를 설정하고 있습니다.
```
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Sunflower"/>
```


 `styles.xml`리소스 파일에서 `Theme.Sunflower`를 확인해보면, `Base.Theme.Sunflower`를 상속받고 있음을 확인할 수 있으며, `Base.Theme.Sunflower`는 `NoActionBar`테마 중의 하나인 `Theme.MaterialComponents.DayNight.NoActionBar`를 상속받고 있습니다.  
 ```
 <style name="Base.Theme.Sunflower" parent="Theme.MaterialComponents.DayNight.NoActionBar">
        <item name="colorPrimary">@color/sunflower_green_500</item>
        <item name="colorOnPrimary">@color/sunflower_yellow_500</item>
        <item name="colorPrimaryDark">@color/sunflower_green_700</item>
        <item name="colorOnSurface">@color/sunflower_black</item>
        <item name="colorAccent">@color/sunflower_green_700</item>
        <item name="colorSurface">@color/sunflower_gray_50</item>
        <item name="colorSecondary">@color/sunflower_yellow_500</item>
        <item name="android:colorBackground">@color/sunflower_green_500</item>
    </style>

    <style name="Theme.Sunflower" parent="Base.Theme.Sunflower"/>
 ```

`Sunflower`에서도 `NoActionBar`테마를 적용하고 있음을 확인할 수 있습니다.  

3. `Toolbar`를 액티비티의 레이아웃에 추가합니다.
```xml
<androidx.appcompat.widget.Toolbar
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    app:layout_collapseMode="parallax"
    >
    <TextView
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:gravity="center"
        android:text="@string/app_name"
        android:textAppearance="?attr/textAppearanceHeadline5"/>
</androidx.appcompat.widget.Toolbar>
```
`HomeViewPagerFragment`에서도 `fragment_view_pager.xml`을 확인해보면, 앱바로 사용하기 위한 `Toolbar`가 위와 같이 존재하는 것을 확인할 수 있습니다.  

4. 액티비티의 `onCreate()`메서드에서 액티비티의 `setSupportActionBar()`메서드를 호출합니다. 이때 `Toolbar`를 인자로 넘기면서 호출합니다.  
`setSupportActionBar()`메서드가 `Toolbar`를 액티비티의 앱바로 설정하는 역할을 합니다.
`HomeViewPagerFragment`는 프래그먼트이기 때문에 액티비티의 `setSupportActionBar()`메서드를 바로 호출할 수 없습니다. 따라서 베이스 액티비티를 얻어와서, 이를 `AppCompatActivity`로 캐스팅 한 후에, `setSupportActionBar()`를 호출하여 `Toolbar`를 액션바로 설정합니다.
```
(activity as AppCompatActivity).setSupportActionBar(binding.toolbar)
```

> 참고: 베이스 액티비티를 AppCompatActivity로 캐스팅하는 이유는 setSupportActionBar() 메서드가 AppCompatActivity에서 제공되는 메서드이기 때문입니다.  




Android3.0(API level 11)부터는 기본 테마를 사용하는 모든 액티비티에 `ActionBar`가 앱 바로 제공되었습니다. 앱 바의 기능은 새로운 Android 버전이 출시될 때마다 네이티브 `ActionBar`에 추가되었기 때문에, 사용자의 기기가 어떤 버전의 Android 를 사용하는지에 따라서 `ActionBar`의 동작이 달라졌습니다.

이와 달리, 최근의 앱바 기능들은 `Toolbar`의 서포트 라이브러리 버전에 추가되었기 때문 서포트 라이브러리를 사용할 수 있는 모든 기기에서 `Toolbar`의 최신 기능을 사용할 수 있습니다. 즉, `Toolbar`를 사용하면, 앱이 다양한 기기에서도 일관되게 작동하도록 할 수 있습니다. 따라서 안드로이드에서는 `Toolbar`를 이용하여 앱바를 구현하는 것을 권장합니다.
