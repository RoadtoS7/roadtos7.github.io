---
layout: post
title:  "Android - Sunflower 분석하기: Room (2)"
date:   2020-08-24 16:12:17 -400
lastmod : 2020-08-24 16:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

Room은 데이터베이스, 엔티티, DAO로 구성되어 있습니다.  
이 중에서도 데이터베이스는  다음과 같은 조건을 만족해야 합니다.  

1. 데이터베이스는 `RoomDatabase`를 상속받는 추상 클래스여야 합니다.
2. `@Database` 어노테이션내에 엔티티 목록을 포함해야 합니다.
3. 인수를 갖지 않고 `@Dao` 어노테이션으로 지정된 클래스를 반환하는 추상 메서드를 포함해야 합니다.

오늘은 이러한 조건을 가지는 데이터베이스에 대해서 더 자세히 살펴보도록 하겠습니다.  

## 데이터베이스 생성
위의 조건을 만족하는 데이터베이스의 인스턴스는 `Room.databaseBuilder()` 또는 `Room.inMemoryDatabaseBuilder()`를 호출하여 얻을 수 있습니다.  

코드로 나타내면 다음과 같습니다.  

{% highlight Kotlin %}
val db = Room.databaseBuilder(
            applicationContext,
            AppDatabase::class.java, "database-name"
        ).build()
{% endhighlight %}

```Kotlin
val db = Room.databaseBuilder(
            applicationContext,
            AppDatabase::class.java, "database-name"
        ).build()

```

## `데이터베이스`와 `엔티티`, `Dao`
데이터베이스 하나가 가질 수 있는 엔티티와 Dao의 개수에는 제한이 없습니다.  
하지만 엔티티와 DAO는 한 데이터베이스 안에서 유일해야 합니다.

아래 예시 코드에서는 총 2개의 엔티티와 3개의 Dao를 가지는 Database를 나타냅니다.

```Kotlin
// Song and Album are classes annotated with @Entity.
@Database(version = 1, entities = {Song.class, Album.class})
abstract class MusicDatabase extends RoomDatabase {
  // SongDao is a class annotated with @Dao.
  abstract public SongDao getSongDao();
  // AlbumDao is a class annotated with @Dao.
  abstract public ArtistDao getArtistDao();
  // SongAlbumDao is a class annotated with @Dao.
  abstract public SongAlbumDao getSongAlbumDao();
}
```


## `Dao`를 사용한 데이터베이스 접근을 권장합니다.
데이터베이스에 직접적으로 접근하여 쿼리를 실행할 수도 있지만, 안드로이드에서는 `Dao`클래스를 사용하는 것을 권장합니다.  
`Dao`클래스를 사용한다면, 네트워크와의 커뮤니케이션을 더 논리적인 계층에서 추상화하여 사용할 수 있습니다.  
또한, SQL쿼리를 직접 실행할 때보다 테스트하기가 더 쉬워집니다. (mock객체를 만들기 쉽다.)
게다가 자동으로 `Cursor`에서 앱의 데이터 클래스로 자동으로 해주기 때문에, 데이터 접근을 위해서 낮은 수준의 데이터베이스 API들을 다룰 필요성을 없애줍니다.

Room은 앱이 컴파일 될 동안에 `Dao`클래스 내의 쿼리에 오류가 있는지 확인합니다.  
따라서 만약 쿼리에 문제가 있다면, 프로그래머에게 바로 알려줍니다.
(런타임이 아닌, 컴파일 타임에 쿼리 상의 에러를 찾을 수 있도록 해줍니다.)


## Database의 속성
`@Database` 어노테이션의 속성들은 다음과 같습니다.
1. entities
  - 데이터베이스 내에 포함할 엔티티들을 나열합니다.
  - 나열된 각 엔티티는 데이터베이스 내에서 테이블로 생성됩니다.

2. exportSchema
  - Room에서 해당 데이터베이스 스키마를 지정된 폴더로 내보낼지 여부를 나타냅니다.
  - 어노테이션 프로세서 인수인 `room.schemaLocation`에 스키마를 내보낼 폴더를 지정합니다.
  - `exportSchema`값이 true일 경우 지정된 폴더로 데이터베이스 스키마가 내보내집니다.
  - 의무사항은 아니지만, 코드 베이스에 데이터베이스 스키마 버전 기록을 가지고 있는 것이 좋습니다. 즉, version control에 스키마 파일을 커밋하는 것이 좋습니다. (하지만 앱과 함께 커밋하면 안됩니다.)
  - 디폴트 값으로 true를 갖습니다.

3. version
  - 데이터베이스 버전
  - 프로그래머가 데이터베이스를 업데이트 할 때마다 숫자를 1씩 증가시켜서 지정해줍니다.  

4. views
  - 데이터베이스 내에 포함될 데이터베이스 뷰들을 나열합니다.
  - 나열된 각 뷰는 데이터베이스 내에서 뷰로 생성됩니다.

## Sunflower Database 분석 - AppDatabase.kt

### AppDatabase.kt
```Kotlin
@Database(entities =[GardenPlanting::class, Plant::class], version = 1, exportSchema = false)
@TypeConverters(Converters::class)
abstract class AppDatabase : RoomDatabase() {
  abstract fun getPlaningDao(): GardenPlantingDao
  abstract fun plantDao(): PlantDao
}
```

Sunflower 프로젝트에서 데이터베이스에 해당하는 AppDatabase의 경우 데이터베이스 조건에 따라 `RoomDatabase`를 상속받는 추상클래스입니다.  
그리고 `@Database` 어노테이션 내에 엔티티 목록을 포함하고 있으며,

```Kotlin
@Database(entities =[GardenPlanting::class, Plant::class], version = 1)
```


인자가 0개이고, `@Dao`어노테이션이 지정된 클래스를 반환하는 추상 메서드를 포함하고 있습니다.

```Kotlin
abstract fun getPlaningDao(): GardenPlantingDao
abstract fun plantDao(): PlantDao
```

이제 조건 외에 `AppDatabase`에 기술된 코드 의미를 분석해보도록 하겠습니다.  
`AppDatabase`의 속성을 볼 때, `GardenPlanting`엔티티와 `Plant`엔티티를 가지며, `AppDatabase`의 버전은 1입니다. 그리고 `exportSchema`가 false로 되어있기 때문에 따로 데이터베이스 스키마가 저장되고 있지 않음을 알 수 있습니다.

```Kotlin
@Database(entities = [GardenPlanting::class, Plant::class], version = 1, exportSchema = false)
```
Room은 primitive 데이터 타입과 primitive 데이터에 대한 wrapper 타입만 지원합니다.  
따라서 그 외의 타입(우리가 만든 클래스) 등을 사용하기 위해서는 `TypeConverter`를 지정해야 합니다.
`TypeConverter`는 Room이 저장할 수 있는 데이터 타입과 커스텀 타입간의 상호변환을 해줍니다.  
`AppDatabase`에서도 Calendar 타입을 사용하기 위해 `Converter`를 만들고 데이터베이스에 `TypeConverter`로서 지정해주었습니다.

AppDatabase.kt 코드

```Kotlin
@Database(entities = [GardenPlanting::class, Plant::class], version = 1, exportSchema = false)
@TypeConverters(Converters::class)
abstract class AppDatabase : RoomDatabase() {
  abstract fun gardenPlantingDao(): GardenPlantingDao
  abstract fun plantDao(): PlantDao
  // ...
}  
```

Converters.kt 코드

```Kotlin
class Converters{
    @TypeConverter fun calendarToDatestamp(calender: Calendar): Long = calender.timeInMills
    @TypeConverter fun datestampToCalendar(value: Long): Calendar = Calendar.getInstance().apply{ timeInMills = value}
}
```

<br/>
<br/>

데이터베이스 인스턴스는 리소스 소모를 많이 하며, 단일 프로세스 내에서 여러 인스턴스에 접근할 필요가 거의 없기 때문에 **싱글톤** 으로 생성합니다. 따라서 `AppDatabase`에서도 데이터베이스 인스턴스를 얻기 위해 사용하는 `getInstance()`함수를 살펴볼 때, 데이터베이스 인스턴스 Singleton 디자인 패턴으로 생성되었음을 알 수 있습니다.

```Kotlin
companion object{
  @Volatile private val instance: AppDatabase? = null

  fun getInstance(context: Context): AppDatabase{
    return instance ?: synchronized(this){
      instance ?: buildDatabase(context).also{ instance = it}
    }
  }
}
```

다음 포스팅에서는 `Room`의 요소중 `Entity`에 대해서 자세히 알아보도록 하겠습니다.
