---
title: "ReactiveX 04 - Creator(생성 연산자)"
date: "2020-04-14 14:03:17 -400"
categories: ReactiveX
---

Observable과 Observer는 Rx의 시작에 해당됩니다.  
물론 Observable과 Observer가 시작인만큼 이것을 이해하는 것은 매우 중요합니다.  
하지만 이것들 만으로는 Observer 패턴을 단순하게 확장만 할 수 있습니다.
Observer 패턴을 더 강력하게 이용하기 위해서는 ReactiveExtension 곧 Reactive X를 활용해야합니다.  

Rx연산자들은 Observer들이 발행한 데이터를 가지고 변형하고, 결합하고, 조작하고 작업할 수 있도록 합니다.  
이러한 Rx 연산자들은 콜백의 장점인 효율성을 가지면서도, 선언하는 방식으로 비동기적인 작업들을 구성할 수 있지만, 
반면에 콜백의 단점인 콜백이 중첩되는 현상은 나타나지 않습니다.  

변수, 클래스, if, while, for 등등 프로그래밍 언어에서 제공하는 키어드 대신 Rx에서 제공하는 연산자를 이용하여 
비동기 프로그래밍에 필요한 주요 로직을 만들 수 있습니다.  

리액티브 연산자는 곧 함수이며, 정확히는 부수효과가 없는 순수함수(pure function)입니다.  

## 4.1 리액티브 연산자 종류
1. 생성(creating)연산자
- Observable, single 클래스 등 데이터 흐름을 만드는 함수
- ex. create(), just(), fromArray(), interval(), range(), timer(), defer() 등등
- 모든 Rx프로그래밍은 생성 연산자에서 시작합니다.  

2. 변환(Transforming) 연산자
- 입력을 받아서 특정한 결과물을 반환하는 전통적인 의미의 함수
- ex. map(), flapMap()

3.  필터(Filter) 연산자
- 입력 데이터 중에서 원하는 데이터만 거르는 함수
- ex. filter(), take(), first()

4. 합성(Combining) 연산자
- 여러 Observable을 조합하는 역할
- 생성, 변환, 필터 연산자: 주로 하나의 Observable을 다룸

5. 오류 처리(Error Handling)  연산자
- onErrorReturn(), onErrorResumeNext()

6. 유틸리티(Utility) 연산자
- 비동기 프로그래밍 지원
- ex. subscribeOn(), observeOn()

7. 조건(Conditional) 연산자
- Observable의흐름을 제어하는 역할

8. 수학과 집합형(Mathematical and Aggregate) 연산자
- 수학 함수와 연관 있는 연산자.

이번 포스팅에서는 RX의 연산자중 팩토리 연산자(생성 연산자)에 대해서 알아보도록하겠습니다.  




## 4.2 just()
just는 Observable을 생성하는 가장 쉬운 방법입니다.  
즉, 기존의 자료구조를 인자로 받아서 이를 차례대로 발행하는 Observable을 생성합니다.  
실제 데이터 발행은 구독자가 생겼을 때부터 시작하기 때문에 차가운 Observable을 만들어 줍니다.  

인자로 데이터를 1개에서 10개까지의 데이터를 넣을 수 있습니다.  
단, 데이터 타입이 모두 같아야합니다. 

- 예시 코드
```
Observable.just(1, 2, 3, 4, 5, 6)
  .subscribe(System.out::println);
```

## 4.3 create() 
- just() 함수는 구독자가 생기면 자동으로 아이템을 발행하지만, create()의 경우에는 개발자가 
직접 함수를 호출하여 아이템을 발행해야합니다.  
- 마찬가지로 차가운 Observable을 생성합니다.  
- 모든 데이터를 발행한 후에는 반드시 onCompleted() 함수를 호출하여 완료 알림을 전달해야 합니다.  
- create() 원형

```
Observable<T> create(ObservableOnSubscribe<T> source)

// ObservbleOnSubscribe<T> 원형
public interface ObservableOnSubscrbie<T>{
   void subscribe(ObservableEttier<T> e) throws Exception;
}
```

- 예시 코드
```
Observable<Integer> source = Observable.create(
  (ObservableEmiiter<Integer> emitter) -> {
    emitter.onNext(100);
    emitter.onNext(200);
    emitter.onNext(300);
    emitter.onComplete();
})

source.subscribe(System.out::println);
```
- 실행결과
```
100
200
300
```

## 4.4 fromArray() 
- 배열 데이터를 Observable로 만들 때 사용합니다.
- 예시 코드
```
Integer[] arr = {100, 200, 300}
Observable<Integer> source = Observable.fromArray(arr);
source.subscribe(System.out::println);
```
- 실행 결과
```
100
200
300
```

## 4.5 fromIterable()
- Iterable 인터페이스를 구현한 클래스 인스턴스로 부터 Observable을 만들 때 사용합니다.

Interable에 대해서 간략히 설명하자면, Iterable 인터페이스는 Iterater 패턴을 구현한 것으로,다음에 어떤 데티어가 있는지와 그 값을 얻어오는 것에만 관여할 뿐
데이터 타입에 상관없이 사용할 수 있다는 장점이 있습니다.

Iterator 인터페이스는 다음과 같습니다.
```
public interface Iterator<E>{
  boolean hasNext();
  E next();
}

Iterable<E> 인터페이스를 구현하는 클래스는 다음과 같습니다.  
1. ArrayList(List 인터페이스)
2. ArrayBlockingQueue(BlockingQueue 인터페이스)
3. HashSet(Set 인터페이스)
4. LinkedList
5. Stack
6. TreeSet
7. Vector

### 4.5.1 List 객체에서 Observable 만들기
- 예시코드
```
List<String> names = new ArrayList();
names.add("Jerry");
names.add("William");
names.add("Bob");

Observable<String> source = Observable.fromIterable(names);
source.subscribe(System.out::println);
```

- 실행결과
```
Jerry
William
Bob
```

### 4.5.2 Set 인터페이스 객체로 Observable 만들기
```
Set<String> cities = new HashSet<>();
cities.add("Seoul");
cities.add("London");
cities.add("Paris");

Observable<String> source= Observable.fromIterable(cities);
source.subscribe(System.out::println);
```

- 실행결과
```
Seoul
London
Paris
```

### 4.5.3 BlockingQueue 인터페이스 객체로 Observable 만들기
```
BlockingQueue<Order> orderQueue = new ArrayBlockingQueue();
orderQueue.add(new Order("ORD-1"));
orderQueue.add(new Order("ORD-2"));
orderQueue.add(new Order("ORD-3"));

Observable<String> source = Observable.fromIterable(orderQueue);
source.subscribe( order ->
  System.out.println(order.getId());
);
```

### 4.6 fromCallable()
기존 자바에서 제공하는 비동기 클래스나 인터페이스와 연동하여 사용할 수 있습니다.  

자바 5에서 제공되는 Callable 인터페이스는 비동기 실행후 결과를 반환하는 call() 함수를 정의합니다.  
```
public interface Callable<V>{
  // 결과를 계산하고, 만약 계산할 수 없는 경우에는 예외를 던집니다.
  // @return 결과값
  // @throws Exception 계산을 완료할 수 없을 때
  V call() thrwos Exception
}
```

이러한 Callbale 인터페이스 객체와 fromCallable() 함수를 이용하여 Observable을 생성할 수 있습니다.  
```
Callable<String> callable = () ->{
  Thread.sleep();
  return "Hello Callable";
}

Observable<String> source = Observable.fromCallable(callable);
source.subscibe(System.out::println);
```

- 실행 결과
```
Hello Callable
```

### 4.7 fromFuture()
Future 인터페이스 역시 자바 5에서 추가된 동시성 API로 비동기 계산의 결과를 구할 때 사용합니다.  

Future 인테페이스 객체와 fromFuture() 함수를 이용하여 Observable을 만들 수 있습니다.  
```
Future<String> future = Executors.newSingleThreadExecutor().submit(() -> {
  Thread.sleep(1000);
  return "Hello Future";
});

Observable<String> source = Observable.fromFuture(future);
  source.subscribe(System.out::println);
```

- 실행 결과
```
Hello Future
```

### 4.8 fromPublisher()
Publisher는 자바 9의 표준인 Flow API의 일부입니다.
Publisher와 fromPublisher() 함수를 통해서 Observable 을 만들 수 있습니다.
```
Publisher<String> publisher = (Subscriber<? super String> s) -> {
  s.onNext("Hello Observable.fromPublisher()");
  s.onComplete();
};

Observable<String> source = Observable.fromPublisher(publisher);
source.subscribe(System.out::println);
```

- 실행 결과
```
Hello Observable.fromPublisher()
```

이러한 생성 연산자외에도 굉장히 많은 생성 연산자가 있습니다.  
기존의 데이터 구조를 사용하지 않는 추가적인 생성연산자에 대해서는 다음 포스팅에서 더 자세히 다루도록 하겠습니다.  

<br/>
<br/>
<br/>
<br/>









