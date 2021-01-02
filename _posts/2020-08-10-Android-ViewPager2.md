---
layout: post
title:  "Android - ViewPager2의 어댑터"
date:   2020-08-10 14:12:17 -400
lastmod : 2020-12-29 14:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

> 본 포스팅은 Android에서 Android Jetpack 사용 예시로 제공하는 Sunfolwer 프로젝트를 클론코딩하며 분석하고 있습니다.

`HomeViewPagerFragment`에서 `MY GARDEN`탭과 `PLANT LIST`탭을 구성하는데 `ViewPager2`를 사용하고 있었으며, `ViewPager2`의 어댑터로 `SunflowerPagerAdapter`를 사용하고 있었습니다.  
<div>
<img src='https://user-images.githubusercontent.com/57262833/89758714-6733ae80-db23-11ea-979d-34af631d0c72.png' width=300px>
</div>

<br/>
<br/>
<br/>
<br/>

- SunflowerPagerAdapter.kt 코드

{% highlight Kotlin %}
const val MY_GARDEN_PAGE_INDEX = 0
const val PLANT_LIST_PAGE_INDEX = 1

class SunflowerPagerAdapter(fragment: Fragment) : FragmentStateAdapter(fragment) {

    /**
     * Mapping of the ViewPager page indexes to their respective Fragments
     */
    private val tabFragmentsCreators: Map<Int, () -> Fragment> = mapOf(
        MY_GARDEN_PAGE_INDEX to { GardenFragment() },
        PLANT_LIST_PAGE_INDEX to { PlantListFragment() }
    )

    override fun getItemCount() = tabFragmentsCreators.size

    override fun createFragment(position: Int): Fragment {
        return tabFragmentsCreators[position]?.invoke() ?: throw IndexOutOfBoundsException()
    }
}
{% endhighlight %}



위 코드를 볼 때, `SunflowerPagerAdapter`는 `FragmentStateAdapter`를 상속받고 있음을 알 수 있습니다.
`FragmentStateAdapter`는 `ViewPager2`의 어댑터를 생성할 때 사용되는 클래스 중 하나로,  `ViewPager2`와 `Fragment`를 함께 사용할 때 주로 사용되며 특히, 이 때 `Fragment`로 하나의 화면을 표시할 때 주로 사용됩니다.
`Sunflower`프로젝트에서는 `MY GARDEN`탭과 연결되는 `GardenFragment`와 `PLANT LIST`탭과 연결되는 `PlantListFragment`가 화면의 일부분을 차지하면서, 화면을 표시할 때 사용되므로 `FragmentStateAdapter`를 사용하고 있음을 추측할 수 있습니다.  

![탭 사진](https://user-images.githubusercontent.com/57262833/91941703-97422c00-ed34-11ea-8655-947e4dea7243.png)


<br/>
<br/>

`ViewPager2`도 `ViewPager`와 마찬가지로 페이지와 자신을 연결시킬 `Adapter`가 필요합니다.  
`Adpater`로 여러가지를 사용할 수 있지만, Activity Lifecycle에 걸쳐서 뷰 내에서 보관하고 있어야할 데이터 값이 없는 경우나, `View`를 이용하여 ViewPager내의 화면을 구현하는 경우와 같이 간단하게 사용하는 경우에는 `RecyclerView.Adpater`로도 충분히 `ViewPager2`를 구현할 수 있습니다.  


`ViewPager2`내에서 `Fragment`를  전환할 화면으로 사용하는 경우, `FragmentStateAdapter`를 상속받아서 Adapter를 만들어야 합니다.`FragmentStateAdapter`는 `getItem` 역할을 하는 메서드에서 새로운 Fragment 객체를 반환합니다.  
`FragmentStateAdapter`는 `StatefulAdapter`를 구현하고 있습니다. `StatefulAdapter`는  프레그 먼트들은 자동적으로 lifecycle의 일환이 됩니다. 따라서 평소 `Fragment`를 구현할 때 처럼 `Fragment`에서 `onSaveInstanceState()`메서드를 하면 됩니다.   

예시를 통해서 좀 더 자세히 보도록 하겠습니다.


- FragmentStateAdapterActivity.kt 코드

{% highlight Kotlin %}
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import androidx.fragment.app.Fragment
import androidx.viewpager2.adapter.FragmentStateAdapter
import kotlinx.android.synthetic.main.activity_main.*
import java.lang.IndexOutOfBoundsException

class FragmentStateAdapterActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        viewpager2.adapter = object: FragmentStateAdapter(this){

            override fun getItemCount(): Int {
                return 2
            }

            override fun createFragment(position: Int): Fragment {
                return when(position){
                    0 -> HomeFragment()
                    1 -> MenuFragment()
                    else -> throw IndexOutOfBoundsException()
                }
            }
        }
    }
}
{% endhighlight %}

`HomeFragment`와 `MenuFragment`는 `TextView`하나씩을 가지고 있습니다.  
위 코드에서 `viewpager2`의 어댑터는 `FragmentStateAdapter`의 익명객체입니다.
`FragmentStateAdapter`에서 우리는 두가지의 함수를 반드시 구현해야 합니다.  


첫번째로, `getItemCount()` 함수를 오버라이딩 해야합니다. `getItemCount()`에서는 뷰페이저를 통해서 보여주고자 하는 `Fragment`의 개수를 `Int`타입으로 반환합니다.

두번째로, `createFragment()`함수를 오버라이딩 해야합니다. `createFragment()`에서는 `Int`타입의 `position`이라는 파라미터를 입력받습니다.  이 `position`은 `Fragment`의 표시 순서라고 이해하시면 됩니다.  
`position` 값에 대응되는 Fragment를 생성해서 반환하면 됩니다.
따라서 위의 예제 코드에서는 `when`을 이용하여 `position`에 따라서 그에 대응되는 Fragment가 생성되어 반환되도록 했습니다.  
0번에는 `HomeFragment`를 맵핑시켰고, 1번에는 `MenuFragment`를 맵핑시켰습니다.  
현재 두개의 Fragment만 고려하고 있기 때문에, 1번보다 큰 position 값이 올 경우에는 `IndexOutOfBoundsException` 예외를 일으켰습니다.  

구현결과는 다음과 같습니다.  
![구현결과](https://media.giphy.com/media/U1guoIXFbYrKRyn7FX/giphy.gif)

<br/>
<br/>

예제 코드에서는 `FragmentStateAdapter`에서 `this`인 `FragmentStateAdapterActivity`를 인자로 받고 있습니다.  
하지만 이외에도 `FragmentStateAdapter`생성자는 여러가지가 존재합니다. 차례대로 살펴보도록 하겠습니다.  

<br/>
<br/>

가장 먼저 위 예시처럼 `FragmentStateAdapter`에 `FragmentActivity`를 인자로 받는 것이 존재합니다.
  
{% highlight Kotlin %}
public FragmentStateAdapter(fragmentStateAdapter FragmentStateAdapter)
{% endhighlight %}

이 생성자는 `ViewPager2`가 액티비티 내에서 동작할 때 주로 쓰입니다.  

<br/>
<br/>

두번째는 인자로 `Fragment`를 받는 것입니다.

{% highlight Kotlin %}
public FragmentStateAdapter(Fragment fragment)
{% endhighlight %}

이 생성자는 프레그먼트 내에서 동작하는 `ViewPager2`를 만들 때 주로 사용합니다.  

<br/>
<br/>

세번째 `FragmentStateAdapter`생성자는 첫번째 인자로 `FragmentManager`를 받고 두번째 인자로는 `Lifecycle`을 받습니다.
  
{% highlight Kotlin %}
public FragmentStateAdapter(FragmentManager fragmentManager,
                            Lifecycle lifecycle)
{% endhighlight %}

첫번째 인자인 `fragmentManager`에는 `ViewPager2`호스트의 `FragmentManager`를 넣습니다.  
두번째 인자인 `lifecycle`에는 `ViewPager2`호스트의 `LifeCycle`을 넣습니다.  

이와 같은 방식으로 `FragmentStateAdapter`를 이용해서 `ViewPager2`와 함께 `Fragment`를 사용할 수 있습니다.  

<br/>
<br/>

`Fragment`를 `ViewPager`를 이용해서 스와이핑하는 기능을 구현할 때, `TabLayout`을 같이 사용하는 경우가 많습니다.  
기존의 `ViewPager`에서는 `TabLayout`과 연결하기 위해 `setUpWithViewPager`를 사용했습니다.  
하지만 `ViewPager2`에서는 `TabLayoutMediator`를 이용하여 `ViewPager2`와 `TabLayout`을 연결합니다.  
먼저, `ViewPager2`에 어댑터를 설정합니다.  
`onConfigureTabCallback`을 구현하여 `TabLayoutMediator`의 생성자의 인자로 넣으면서, 호출하는 방식으로 `TabLayoutMediator`를 생성합니다.   
이렇게 생성된 `TabLayoutMediator`의 `attach()`메서드를 호출하면, `ViewPager2`와 `TabLayout`이 연결됩니다.

`Sunflower`프로젝트의 `HomeViewPagerFragment`에서도 다음과 같은 `TabLayout`을 구현하기 위해 `TabLayoutMediator`를 사용했습니다.
- `HomeViewPagerFragment`의 `TabLayout` 구현 모습  

  ![탭 사진](https://user-images.githubusercontent.com/57262833/91941703-97422c00-ed34-11ea-8655-947e4dea7243.png)

- `HomeViewPagerFragment.kt`에서 `ViewPager2` 어댑터를 설정하고, `TabLayout`과 `ViewPager2`를 연결하는 코드

{% highlight Kotlin %}
  viewPager.adapter = SunflowerPagerAdapter(this)

      // Set the icon and text for each tab
      TabLayoutMediator(tabLayout, viewPager) { tab, position ->
          tab.setIcon(getTabIcon(position))
          tab.text = getTabTitle(position)
      }.attach()
{% endhighlight %}

<br/>
<br/>

다음으로는 `ViewPager2`와 `TabLayout`을 사용하는 예시를 살펴보겠습니다.  

<br/>
<br/>

- TabActivity 코드

{% highlight Kotlin %}
class TabActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_tab)

        val binding = DataBindingUtil.setContentView<ActivityTabBinding>(this, R.layout.activity_tab)
        val tabs = binding.tabs
        val viewpager= binding.viewpager

        viewpager.adapter = MenuViewPagerAdapter(this)

        TabLayoutMediator(tabs, viewpager){ tab, position->
            tab.text = getTabTitle(position)
        }.attach()


    }

    private fun getTabTitle(position: Int): String?{
        return when(position){
            0 -> getString(R.string.home_title)
            1 -> getString(R.string.menu_title)
            else -> null
        }
    }
}
{% endhighlight %}

위 코드에서는 `viewpager`의 어댑터로 `FragmentStateAdapter`를 상속받은 `MenuViewPagerAdapter`를 사용하고 있습니다.
(자세한 코드가 궁금하다면 github 프로젝트를 확인해주세요.)  

이어서 `TabLayoutMediator`를 이용하여 `TabLayout`인 `tabs`와 `ViewPager2`인 `viewpager`를 연결하고 있습니다.

<br/>
<br/>

{% highlight Kotlin %}
TabLayoutMediator(tabs, viewpager){ tab, position->
    tab.text = getTabTitle(position)
}.attach()
{% endhighlight %}

`TabLayoutMediator` 구현 코드를 더 자세히 살펴보도록 하겠습니다.  
`TabLayoutMediator`는 3개의 인자를 받습니다.
  
{% highlight Kotlin %}
public TabLayoutMediator (TabLayout tabLayout,
                ViewPager2 viewPager,
                TabLayoutMediator.TabConfigurationStrategy tabConfigurationStrategy)

{% endhighlight %}

첫번째 파라미터인 `tabLayout`와 두번째 파라미터인 `viewPager`에는 연결하려는 `TabLayout`과 `ViewPager2`를 순서대로 넣습니다.  
세번째 파라미터로는 `TabLayoutMediator.TabConfigurationStrategy`를 구현하여 넣습니다.  
`TabLayoutMediator.TabConfigurationStrategy`는 탭 레이아웃의 탭 아이템들의 스타일과 텍스트를 설정하는 작업을 하기 위해 구현해야하는 콜백 인터페이스입니다.  
`onConfigureTab()`이라는 추상메서드를 하나 가지며, 이 메서드에 탭 아이템들의 스타일과 텍스트를 설정하는 작업을 코드로 구현해서 넣어야 합니다.   
`onConfigureTab`은 `tab`과 `position`이라는 파라미터를 가지며, 각각 하나의 탭 아이템과 해당 탭아이템의 탭 레이아웃 상에서의 위치(position)를 의미합니다.  

예제코드에서는 `tab.text = getTabTitle(position)` 을 수행하여, 탭 아이템에 텍스트를 부여하고 있습니다.  
그후 최종적으로 `TabLayoutMediator.attach()`를 호출하면, `TabLayout`과 `TabLayoutMediator`가 연결됩니다.  

최종적으로 코드를 실행하면 다음과 같이 `TabLayout`과 `TabLayoutMediator`가 연결되어서 스와이핑 되는 기능이 정상적으로 수행되는 것을 볼 수 있습니다.


![완성작품](https://media.giphy.com/media/SVTfG4J61OzifSCCNn/giphy.gif)
![완성작품](https://media.giphy.com/media/SUiKDvQCER3omy1bHT/giphy.gif)
