---
layout: post
title: "Coroutine suspend 키워드"
date: 2021-09-02 22:08:17 -400
lastmod: 2021-09-02 22:08:17 -400
sitemap:
  changefreq : daily
  priority : 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

Kotlin Coroutine은 suspend 키워드를 사용해서 suspending function을 만들 수 있다.  
코루틴은 suspending function을 만나면, 실행을 일시 중지 했다가 suspending function의 실행이 완료되면 실행을 재개한다.  

이 포스팅에서는 suspend 키워드를 사용해서 suspending function을 만들었을 때, 이것이 컴파일결과 어떻게 나타나는지 볼 것이다.  
그리고 내부에서 어떤 동작을 통해서 일시 정지와 재개 작업이 이루어지는 볼 것이다.  

참고:bulb: suspending function과 suspend function은 모두 일시 중단 함수를 지칭한다.  

Kotlin 컴파일러는 각 suspend function마다 state machine을 만든다. state machine이 코루틴의 실행을 관리한다.  

<br/>
<br/>

## 📝 suspend function의 내부에서 일어나는 작업

```Kotlin
suspend fun loginUser(userId: String, password: String): User {
  val user = userRemoteDataSource.logUserIn(userId, password)
  val userDb = userLocalDataSource.logUserIn(user)
  return userDb
}
// UserRemoteDataSource.kt
suspend fun logUserIn(userId: String, password: String): User
// UserLocalDataSource.kt
suspend fun logUserIn(userId: String): UserDb
```
설명에 사용할 예시코드이다.  
3개의 함수 모두 suspend function이라는 것에 주목하자.  

suspend function 이 컴파일 되었을 때 일어나는 작업을 간단히 말하자면, 컴파일러는 유한 상태 머신(finite state machine)을 이용하여 suspend function을 최적화된 callback으로 만든다.  

즉, ***우리가 콜백을 이용해서 처리하던 비동기 작업을 컴파일러가 대신 콜백을 이용해서 처리해주는 것이다.***

<br/>

### Continuation Interface
suspend function이 일시 중단 및 재개하는 데는 `Continuation` 인터페이스 객체가 사용된다.  
`Continuation`은 제네릭 함수 인터페이스이다.   
**`Continuation`이 suspend function에 대한 state machine에 해당한다.**

`Continuation`의 정의를 살펴보자.  

```Kotlin
interface Continuation<in T> {
  public val context: CoroutineContext
  public fun resumeWith(value: Result<T>)
  public fun resumeWithException(exception: Throwable)
}
```
- `context`는 이 continuation 내부에서 사용되는 코루틴 콘텍스트이다.
- resume(value: T)
    : 일시 중단 함수의 실행 결과를 가지고 코루틴을 실행 재개하는 함수

- resumeWithException()
    : 일시 중단 작업 중에서 발생한 예외를 전파시킨다.  

suspend 키워드가 붙은 함수는 컴파일되면, 파라미터로 `Continuation` 객체를 추가로 갖는다.  
새롭게 파라미터로 추가된 `Continuation` 객체를 이용하여 suspend function의 실행 결과와 코루틴 간에 상호작용하는데 사용된다.  

위의 `loginUser()`의 컴파일 결과는 다음과 같다.  

```Kotlin
fun loginUser(userId: String, password: String, completion: Continuation<Any?>) {
  val user = userRemoteDataSource.logUserIn(userId, password)
  val userDb = userLocalDataSource.logUserIn(user)
  completion.resume(userDb)
}
```

원래 `loginUser`는 `User`객체를 반환했지만, 컴파일 결과 `Unit`을 반환하는 함수로 바뀐 것을 확인할 수 있다.  
`completion.resume(userDB)`부분을 볼 때 `User` 객체는 `Continuation`을 통해서 반환되는 것을 확인할 수 있다.  

주의사항❗️  
suspend 키워드를 지정한 function 내부에서 어떤 suspend function도 호출하지 않더라도, 컴파일러는 해당 함수에 `Continuation`객체를 파라미터로 추가한다.  
하지만 함수 내부에서는 `Continuation`객체를 이용하여 아무런 작업도 하지 않는다.   

suspend function이 아니더라도 `Continuation` 인터페이스는 여러 곳에서 볼 수 있다.
1. callback 기반의 API를 `suspendCoroutine` 혹은 `suspendCancellableCoroutine` 으로 전환할 때 코루틴 실행을 재개하기 위해서 `Continuation` 객체를 사용한다.   
   - `suspendCoroutine` 혹은 `suspendCancellableCoroutine` 을 사용할 때 파라미터로 코드 블럭을 넘긴다. 이 코드 블럭이 실행되는 동안에는 코루틴이 일시 정지하게 된다.  
   - 따라서 코드 블럭 실행 후에 코루틴을 다시 실행시키기 위해서 `Continuation` 객체를 사용한다.   

2. suspend function에 대한 확장함수인 `startCoroutine()`를 이용해서 코루틴을 시작할 수 있다.  
   - `startCoroutine()`은 `Continuation` 객체를 파라미터로 받는다.  
   그리고  `Continuation` 객체는 `startCoroutine()`로 시작된 코루틴의 실행 결과를 갖게 되거나, 혹은 exception을 전파하게 된다.  

<br/>

### 다른 Dispatcher를 사용하는 경우
코루틴에서 Dispatcher를 이용하여 작업을 처리할 스레드를 바꾼 상황일 때, 코틀린은 중단된 코루틴을 어디 스레드에서 다시 시작해야하는지 알 수 있을까?

`Continuation`의 subtype으로 `DispatchedContinuation`이라는 것이 존재한다.  
이 `DispatchedContinuation`의 `resume()`은 `DispatchedContinuation`의 `CoroutineContext` 내부에서 이용가능한 Dispatcher에서 Coroutine이 처리되도록 한다.  

 All Dispatchers will call dispatch except Dispatchers.Unconfined whose isDispatchNeeded function override (that is called before dispatch) always returns false.

<br/>
<br/>

## State Machine
이제부터 나오는 예시 코드들은 컴파일된 바이트 코드 결과와 일치하지 않는다.  
이해를 돕기 위해서 Kotlin으로 각색된 코드이다.  

**코틀린 컴파일러는 언제 suspend function이 실행을 중단할 수 있는지를 체크한다.**  
1. suspend function 내부에서 실행 중단할 수 있는 각 지점은 유한 상태 머신(finite State Machine)의 상태로 표현된다. 그리고 각 상태는 컴파일러에 의해서 라벨로 나타내진다.

    ```Kotlin
    fun loginUser(userId: String, password: String, completion: Continuation<Any?>) {
    // Label 0 -> first execution
    val user = userRemoteDataSource.logUserIn(userId, password)
    // Label 1 -> resumes from userRemoteDataSource
    val userDb = userLocalDataSource.logUserIn(user)
    // Label 2 -> resumes from userLocalDataSource
    completion.resume(userDb)
    }
    ```

    - 상태 머신을 좀더 잘 나타내기 위해서 컴파일러는 when 표현식을 사용하여 각각 실행 중단이 일어날 수 있는 상태를 구분한다.  

    ```Kotlin
    fun loginUser(userId: String, password: String, completion: Continuation<Any?>) {
    when(label) {
        0 -> { // Label 0 -> first execution
            userRemoteDataSource.logUserIn(userId, password)
        }
        1 -> { // Label 1 -> resumes from userRemoteDataSource
            userLocalDataSource.logUserIn(user)
        }
        2 -> { // Label 2 -> resumes from userLocalDataSource
            completion.resume(userDb)
        }
        else -> throw IllegalStateException(...)
    }
    }
    ```

2. 이 코드는 상태들이 서로 정보를 공유할 방법이 없기 때문에 아직 불안전하다.  
컴파일러는 `loginUser()`내에서 동일한 `Continuation` 객체를 사용하도록 함으로써 서로 다른 상태들간에 정보를 공유할 수 있도록 한다.  
이를 위해서 `Continuation`의 제네릭 타입이 `User`가 아니라 `Any?`이다.  

3. 게다가 컴파일러는 private class를 만들어서 이 private class로 하여금 (1)필요한 데이터를 갖고 있도록 하고, (2) 코루틴 실행 재개를 위해 `loginUser()`을 반복하여 호출하도록 한다.  

이렇게 3가지 과정을 거친 결과를 추상화 시켜서 나타내면 다음 코드와 같다.  

```Kotlin
fun loginUser(userId: String?, password: String?, completion: Continuation<Any?>) {
  class LoginUserStateMachine(
    // completion parameter is the callback to the function 
    // that called loginUser
    completion: Continuation<Any?>
  ): CoroutineImpl(completion) {
    // Local variables of the suspend function
    var user: User? = null
    var userDb: UserDb? = null
    // Common objects for all CoroutineImpls
    var result: Any? = null
    var label: Int = 0
    // this function calls the loginUser again to trigger the
    // state machine (label will be already in the next state) and
    // result will be the result of the previous state's computation
    override fun invokeSuspend(result: Any?) {
      this.result = result
      loginUser(null, null, this)
    }
  }
  ...
}
```

`invokeSuspend()` 함수가 `Continuation`객체에 대한 정보를 가지고 `loginUser()`를 호출하기 때문에, `loginUser()`의 나머지 파라미터는 모두 nullable이 된다. 

4. 이 시점에서 컴파일러는 각 상태간에 전환하는 방법에 대한 정보만 위코드에 추가해주면된다.  
    
    1. 가장 첫번째로 `loginUser()`가 처음으로 호출 된 것인지, 아니면 일시 정지 되었다가 실행 재개하기 위해서 호출된 것인지를 알 수 있어야 한다.(이전 상태로부터 다음 상태로 넘어오기 위해 호출된 것인지를 알 수 있어야 한다.)
    
        ```Kotlin
        fun loginUser(userId: String?, password: String?, completion: Continuation<Any?>) {
        ...
        val continuation = completion as? LoginUserStateMachine ?: LoginUserStateMachine(completion)
        ...
        }
        ```
       
    - 이는 `loginUser()`의 `Continuation` 파라미터 값의 타입이 `LoginUserStateMachine` 인지 체크해보면 알 수 있다.  

    - 만약, 가장 처음으로 호출된 것이라면, 새로운 `LoginUserStateMachine` 인스턴스를 만들 것이고, `completion` 인스턴스를 생성한 `LoginUserStateMachine` 인스턴스에 저장할 것이다.

    - 그렇지 않다면, 상태 머신(suspend function의 컴파일 결과) 코드를 계속해서 실행할 것이다.  
  

- 이제 컴파일러가 각 상태를 전환하고, 상태 간에 정보를 공유할 수 있도록 만든 코드를 살펴보자.

```Kotlin
fun loginUser(userId: String?, password: String?, completion: Continuation<Any?>) {
    ...

    val continuation = completion as? LoginUserStateMachine ?: LoginUserStateMachine(completion)

    when(continuation.label) {
        0 -> {
            // Checks for failures
            throwOnFailure(continuation.result)
            // Next time this continuation is called, it should go to state 1
            continuation.label = 1
            // The continuation object is passed to logUserIn to resume 
            // this state machine's execution when it finishes
            userRemoteDataSource.logUserIn(userId!!, password!!, continuation)
        }
        1 -> {
            // Checks for failures
            throwOnFailure(continuation.result)
            // Gets the result of the previous state
            continuation.user = continuation.result as User
            // Next time this continuation is called, it should go to state 2
            continuation.label = 2
            // The continuation object is passed to logUserIn to resume 
            // this state machine's execution when it finishes
            userLocalDataSource.logUserIn(continuation.user, continuation)
        }
        ... // leaving out the last state on purpose
    }
}
```

- `when` 표현식의 인자는 `LoginUserStateMachine`인스턴스의 `label` 이다.

- 매번 새로운 상태가 처리될 때마다 이 함수가 일시 정지했을 때 실행 실패가 발생하지는 않았는지 체크한다.  

- 다음 함수를 호출하기 전에(ex. `logUserIn()`), `LoginUserStateMachine`인스턴스의 `label` 값을 다음 상태를 나타내는 값으로 바꾸어 준다.  

- state machine 내부에 다른 suspend function 호출 코드가 존재한다면, `continuation` 인스턴스 (여기서는 `LoginUserStateMachine` 타입이다.)를 해당 suspend function의 파라미터 값으로 넘긴다.  
호출되는 suspend function도 컴파일러에 의해서 `Continuation` 객체를 파라미터로 갖는 state machine으로 변환된다. 이 state machine이 실행 완료되면, 위 코드의 state machine(`loginUser()`)가 실행 재개 된다.  

- 가장 마지막 상태는 다른 상태와는 조금 다르다. 마지막 상태에서는 현재 suspend function이 호출된  코루틴을 일시 정지 상태에서 실행 상태로 바꾸는 작업을 해주어야 한다.  
따라서 다음 코드에서 확인할 수 있듯이, `cont` 변수의 resume 함수를 호출한다.  
여기서 `cont` 변수는 `LoginUserStateMachine`이 생성될 때 `LoginUserStateMachine`내부에 같이 생성된다.  

```Kotlin
fun loginUser(userId: String?, password: String?, completion: Continuation<Any?>) {
    ...

    val continuation = completion as? LoginUserStateMachine ?: LoginUserStateMachine(completion)

    when(continuation.label) {
        ...
        2 -> {
            // Checks for failures
            throwOnFailure(continuation.result)
            // Gets the result of the previous state
            continuation.userDb = continuation.result as UserDb
            // Resumes the execution of the function that called this one
            continuation.cont.resume(continuation.userDb)
        }
        else -> throw IllegalStateException(...)
    }
}
```

<br/>
<br/>

## 👩‍🏫 결과

```Kotlin
suspend fun loginUser(userId: String, password: String): User {
  val user = userRemoteDataSource.logUserIn(userId, password)
  val userDb = userLocalDataSource.logUserIn(user)
  return userDb
}
```

결과적으로 위와 같이 우리가 아주 간단하게 작성하는 suspend function을 컴파일러가 다음과 같이 변환해준다.

```Kotlin
fun loginUser(userId: String?, password: String?, completion: Continuation<Any?>) {

    class LoginUserStateMachine(
        // completion parameter is the callback to the function that called loginUser
        completion: Continuation<Any?>
    ): CoroutineImpl(completion) {
        // objects to store across the suspend function
        var user: User? = null
        var userDb: UserDb? = null

        // Common objects for all CoroutineImpl
        var result: Any? = null
        var label: Int = 0

        // this function calls the loginUser again to trigger the 
        // state machine (label will be already in the next state) and 
        // result will be the result of the previous state's computation
        override fun invokeSuspend(result: Any?) {
            this.result = result
            loginUser(null, null, this)
        }
    }

    val continuation = completion as? LoginUserStateMachine ?: LoginUserStateMachine(completion)

    when(continuation.label) {
        0 -> {
            // Checks for failures
            throwOnFailure(continuation.result)
            // Next time this continuation is called, it should go to state 1
            continuation.label = 1
            // The continuation object is passed to logUserIn to resume 
            // this state machine's execution when it finishes
            userRemoteDataSource.logUserIn(userId!!, password!!, continuation)
        }
        1 -> {
            // Checks for failures
            throwOnFailure(continuation.result)
            // Gets the result of the previous state
            continuation.user = continuation.result as User
            // Next time this continuation is called, it should go to state 2
            continuation.label = 2
            // The continuation object is passed to logUserIn to resume 
            // this state machine's execution when it finishes
            userLocalDataSource.logUserIn(continuation.user, continuation)
        }
        2 -> {
            // Checks for failures
            throwOnFailure(continuation.result)
            // Gets the result of the previous state
            continuation.userDb = continuation.result as UserDb
            // Resumes the execution of the function that called this one
            continuation.cont.resume(continuation.userDb)
        }
        else -> throw IllegalStateException(...)
    }
}
```

- 코틀린 컴파일러는 모든 suspend function을 state machine으로 변환한다.  

- 이 state machine은 suspend function이 실행 재개할 때마다 콜백을 이용하는 것을 최적화한 것이다.

- suspend function이 일시 중지 및 재개할 때 필요한 모든 정보는 `Continuation` 객체에 담겨져 있다.

<br/>
<br/>
<br/>

> 이 포스팅은 안드로이드 공식 Medium 블로그의 [The suspend modifier — under the hood 포스팅](https://medium.com/androiddevelopers/the-suspend-modifier-under-the-hood-b7ce46af624f)을 바탕으로 만들어졌습니다.  
