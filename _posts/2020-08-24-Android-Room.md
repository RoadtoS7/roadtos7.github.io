---
layout: post
title:  "Android - Sunflower 분석하기: Room (1)"
date:   2020-08-24 14:12:17 -400
lastmod : 2020-08-24 14:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
---

## Sunflower 와 Room
Sunflower 프로젝트에서는 Room을 사용하여 로컬 데이터 베이스에 앱의 데이터를 저장하고 있습니다.
Room의 기능과 사용방법 등에 대해서 차례차례 알아보도록 하겠습니다.  

## Room을 사용하여 로컬 데이터베이스에 데이터 저장
Room은 SQLite에 대한 추상화 레이어를 제공하여 데이터베이스를 원활하게 사용할 수 있게 하며,  
이와 동시에 SQLite를 완벽히 활용합니다.

상당한 양의 구조화된 데이터를 처리하는 앱은 데이터를 로컬에 저장하여 얻을 수 있는 장점이 많습니다.  
예를 들어서, 데이터를 캐싱하는 것이 있습니다.   
앱에서 데이터를 캐싱하면, 기기가 네트워크에 접속할 수 없어 오프라인 상태일 때도 사용자가 앱의 컨텐츠를 탐색할 수 있습니다.  
그리고 오프라인 동안에 앱에서 사용자가 변경한 컨텐츠는 기기가 온라인 상태가 되었을 때, 서버에 동기화됩니다.

Android에서는 Room이 서버와 로컬의 데이터 동기화 문제를 자동으로 처리해준다는 점에서 Room을 SQLite보다 권장합니다.

앱에서 Room을 사용하려면 앱의 build.gradle 파일에 다음 종속 항목을 추가합니다.
```
dependencies {
 def room_version = "2.2.5"

 implementation "androidx.room:room-runtime:$room_version"
 annotationProcessor "androidx.room:room-compiler:$room_version" // For Kotlin use kapt instead of annotationProcessor

 // optional - Kotlin Extensions and Coroutines support for Room
 implementation "androidx.room:room-ktx:$room_version"

 // optional - RxJava support for Room
 implementation "androidx.room:room-rxjava2:$room_version"

 // optional - Guava support for Room, including Optional and ListenableFuture
 implementation "androidx.room:room-guava:$room_version"

 // Test helpers
 testImplementation "androidx.room:room-testing:$room_version"
}
```

> 참고:: Kotlin 기반 앱에서는 annotationProcessor 대신 kapt를 사용해야 합니다. 또한 kotlin-kapt 플러그인도 추가해야 합니다.

## Room의 구성요소
1. 데이터베이스: 데이터베이스 홀더를 가지고 있으며, 앱의 지속적인 관계형 데이터의 연결을 위한 메인 접속 포인트 역할을 합니다.

  `@Database`어노테이션으로 지정되어있는 클래스는 다음과 같은 조건을 만족해야 합니다.
  - `RoomDatabase`를 상속받는 추상 클래스(abstract class)여야 합니다.
  - 어노테이션 내에 데이터베이스와 연결된 엔티티 목록을 포함해야 합니다.
  - 인수가 0개이며, `@DAO`어노테이션이 지정된 클래스를 반환하는 추상 메서드를 포함해야 합니다.

  런타임 때 `Room.databaseBuilder()` 또는 `Room.inMemoryDatabaseBuilder()`를 호출하여 `Database`인스턴스를 얻을 수 있습니다.

2. 엔티티: 데이터베이스 내에 존재하는 테이블을 나타냅니다.
3. DAO: 데이터베이스에 접근하는데 사용되는 함수들을 포함하고 있습니다.

앱은 Room 데이터베이스를 이용하여 데이터베이스와 연결된, 데이터 접근 객체 혹은 DAO들을 가지고 옵니다.  
그런 다음, 앱은 각 DAO를 사용하여 데이터베이스에서 엔티티를 가져오고 엔티티의 변경사항을 다시 데이터베이스에 저장합니다.  
마지막으로 앱은 엔티티를 사용하여 데이터베이스 내의 테이블 열에 해당하는 값을 가져오거나 열에 값을 설정합니다.   

이러한 Room의 구성요소 간 관계는 다음 그림과 같습니다.
![Room 구성요소간 관계](https://developer.android.com/images/training/data-storage/room_architecture.png?hl=ko)

## Room 예시
다음 예시는 하나의 엔티티와 하나의 DAO가 있는 데이터베이스 구성 샘플입니다.  

- User
```
@Entity
data class User(
  @PrimaryKey val uid: Int,
  @ColumnInfo(name = "first_name") val firstName: String?,
  @ColumnInfo(name = "last_name") val lastName: String?
)
```

- UserDao
```
@DAO
interface UserDao{
    @Query("SELECT * From User")
    fun getALL: List<User>

    @Query("SELECT * From User WHERE uid IN (:userIds)")
    fun loadAllByIds(userIds: IntArray): List<User>

    @Query("SELECT * From User WHERE first_name LIKE :first AND "+
      "lastName LIKE :last LIMIT 1")
    fun findByName(first: String, last: String)

    @Insert
    fun insertAll(vararg users: User)

    @Delete
    fun delete(user: User)
}
```

- AppDatabase
```
@Database(entities = arrayOf(USER::class), version = 1)
abstract class Appdatabase: RoomDatabase(){
  abstract fun userDao(): UserDao
}
```

위 파일을 생성한 후, 다음 코드를 사용하여 생성된 데이터베이스 인스턴스를 가지고 옵니다.
```
val db = Room.databaseBuilder(
    applicationContext,
    AppDatabase::class.java,
    "database-name"
  ).build()
```

앱이 단일 프로세스에서 실행된다면 `AppDatabase`객체를 인스턴스화할 때 싱글톤 디자인 패턴을 따라야 합니다.  
왜냐하면 각 RoomDatabase인스턴스는 리소스를 많이 소모하며,  
단일 프로세스 내에서 여러 인스턴스에 액세스할 필요가 거의 없기 때문입니다.  

앱이 여러 프로세스에서 실행된다면, 데이터베이스 빌더를 호출 `enableMultiInstanceInvalidation()`을 포함합니다.   
이렇게 하면 각 프로세스에 데이터베이스 인스턴스가 있을 때, 한 프로세스에서 공유 데이터베이스 파일을 무효화할 수 있으며, 이 무효화는 자동으로 다른 프로세스 내의 데이터베이스 인스턴스로 전파됩니다.
