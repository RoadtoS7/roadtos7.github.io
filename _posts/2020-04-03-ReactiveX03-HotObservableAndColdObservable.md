---
title: "ReactiveX 03- 뜨거운 Observable & 차가운 Observable"
time: "2020-04-03 15:36:17 -400"
categories: ReactiveX
---

# 3. 뜨거운 Observable과 차가운 Observable
Observable에는 뜨거운 Observable과 차가운 Observable이 존재합니다.  
뜨거운 Observable은 생성과 동시에 데이터 발행을 시작합니다.  
따라서 뜨거운 Observable이 데이터를 발행한 이후에 구독한 Observer는 구독시점 이후에 발행된 데이터부터 관찰할 수 있습니다.  
즉, 구독시점 이전에 발행된 데이터는 놓치게 됩니다.  

반면에 차가운 Observable은 자신을 구독하는 Observer가 생길 때까지 아이템을 발행하지 않습니다.  
따라서 Observable이 발행하는 데이터 시퀀스를 처음부터 관찰할 수 있습니다.  

뜨거운 Observable에서는 배압에 대해서 주의해야합니다.
배압은 Observable이 데이터를 발행하는 속도와 구독자가 데이터를 받아서 처리하는 처리하는 속도 차이가 클 때 발생합니다.  

배압을 처리하는 클래스는 Flowable 클래스로 추후 포스팅에서 더 자세히 다루도록 하겠습니다.  
그때까지는 뜨거운 Observable을 사용할 때는 배압에 대해서 주의해야하며, Flowable 클래스가 배압 처리를 한다고 기억하시면 됩니다.  

그런데 차가운 Observable을 뜨거운 Observable로 변환하는 방법이 있습니다.  
바로 Subject 클래스와 Connectable Observable을 활용하시면 됩니다.  

우선 Subject 클래스 부터 알아보도록 하겠습니다.  

<br/>
<br/>
<br/>
<br/>

## 3.1 Subject
- Subject 는 Rx를 구현할 때 필요한 일종의 다리와 같은 역할을 한다고 보면됩니다.  
**즉, Observable과 Observer 둘다로서 동작합니다. **

<br/>
- Observer 가 될 수 있기 때문에 여러개의 Observable을 구독할 수 있습니다.
- Observable이 될 수 있기 때문에 Subjct가 관찰하던 아이템을 재발행함으로써 전달해줄 수 있습니다. 전달 뿐만아니라 새로운 아이템을 생성하여 발행할 수도 있습니다.
- Subject가 Observable을 구독하기 때문에, Subject는 Observable이 아이템을 발행하는 것을 시작시킬 수 있습니다. ( 만약 Subject가 구독하는 Observable이 차가운 Observerable인 경우를 말합니다. 차가운 Observable의 경우에는 아이템을 발행하기 전까지 자신을 구독해줄 Observer를 기다리고 있습니다.)
- **결과적으로 Subject의 이러한  성질로 인해서 Subejct는 뜨거운 Observable을 차가운 Observable로 바꿀 수 있습니다.**

subeject에는 총 4가지의 종류가 있습니다.  
참고로 모든 언어에서 이 4가지 subject를 이용할 수 있는 것은 아닙니다.  
RxJava에서는 모두 이용할 수 있습니다.  

<br/>
<br/>

### 3.1.1 AsyncSubejct
![AsyncSubject](http://reactivex.io/documentation/operators/images/S.AsyncSubject.png)
- 완료된 시점에서 (onCompleted 알림이 전달된 시점)에서 가장 마지막에 발행된 데이터를 구독자에게 발행(전달)한다.
- 위의 마블 다이어그램을 볼 때, 구독자의 개수에 상관없이 첫번째 구독자와 똑같은 값을 발행한다.
- AsyncSubject 객체는 정적 팩토리 함수인 create() 로 생성합니다.
(Observable에서 연산자는 여러가지 종류가 있으며, 그중에서 팩토리 함수들이 Observable을 생성합니다.
연산자에 대해서는 추후 포스팅에서 다룰 예정입니다)
- 만약 AyncSubject의 source Observable이 아무런 값도 발행하지 안흔다면 AsyncSubject는 아무런 값도 발행하지 않고 종료한다.

![AyncSubjectError](http://reactivex.io/documentation/operators/images/S.AsyncSubject.e.png)
- 만약 source Observable이 error를 가지고 종료된다면, AsyncSubject는 어떤 아이템도 발행하지 않을 것이다. 
- 대신에 source Observable로 부터 받은 error알림을 똑같이 발행한다.

<br/>
<br/>

- AsyncSubject에 대한 예
```
 AsyncSubject<String> subject = AsyncSubject.create();
 subject.subscribe(data -> System.out.println("Subscriber #1 => " + data));
 subject.onNext("1");
 subject.onNext("3");
 subject.subscribe(data -> System.out.prinlnt("Subscriber #3 => " + data));
 subject.onNext("5");
 subject.onComplete();
```
- 실행결과
```
Subscriber #1 => 5
Subscriber #2 => 5
```
- 예시 코드 설명
1. subscribe() 함수를 호출하여 구독을 시작합니다.
Subject클래스의 subscribe() 함수의 원형은 Observable과 같습니다.  
따라서 인자로 람다 표현식을 쓸 수 있습니다.  

2. onNext() 함수를 활용하면, 데이터를 발행합니다.
subject클래스를 생성할 때 AsyncSubject의 제네릭 타입을 String으로 지정했기 때문에, String 타입을 onNext의 인자로 넣어야합니다.  
하지만 onNext() 함수를 통해 데이터가 발행되도록 하더라도, Subject에서는 onNext()를 통해 발행하도록 전달받은 데이터 중 가장 최근의 것만을 저장하고 있다가 onComplete() 알림을 전달받으면 저장하고 있던 것을 발행하게 됩니다.  
즉, 완료된 시점에서 가장 마지막에 발행된 데이터만을 구독자에게 발행합니다.  

3. '3' 데이터를 발행한 후에 두번째 구독자가 subscribe()를 호출하여 구독합니다.  
'5' 데이터가 발행된 후에는  onComplete 함수를 호출하여 subject를 완료시킵니다.  
이때 마지막으로 발행된 데이터가 구독자에게 최종적으로 발행됩니다.

<br/>
<br/>

위의 코드는 AsyncSubject가 Observable로 동작한 것입니다.  
AsyncSubject는 Observer로서도 동작할 수 있습니다.
다음은 AsyncSubject가 Observer로서 동작한 예입니다.
```
Float[] temperature = {10.1f, .13.4f, 12.5f};
Observabler<Float> source = Observable.fromArray(temperature);

AsyncSubject<Float> subject = AsyncSubject.create();
subject.subscribe(data -> System.out.println("Subscriber #1 => " + data);

source.subscribe(subject);
```
1. 우선 10.1f 와 같이 float 타입의 데이터를 담는 Observable을 생성합니다.
여기서 생성한 Observabel이 바로 source에 해당합니다.

2. 다음에 subject라는 이름의 AsyncSubject<Float> 객체를 생성합니다.
subject의 subscribe() 함수를 호출하여 subject를 구독하는 구독자를 만들어줍니다.  

3. source의 subscribe() 함수를 호출하여 source를 구독하는 구독자를 만들어줍니다.
그럼 source Observable이 발행하는 float 타입의 데이터를 AsyncSubject인 subject가 받아서 자신의 구독자에게 발행하게 됩니다.

즉, 여기서 subject는 Observer로서도 동작하지만 Observable로도 동작하고 있습니다.  
이것이 가능한 이유는 AsyncSubject가 Observable을 상속받는 동시에 Observer인터페이스를 구현하고 있기 때문입니다.
```
public abstract class Subject<T> extends Observable implements Observer<T>
```

최종적인 실행결과는 다음과 같이 source Observable이 발행하는 마지막 데이터인 12.5f만이 subject가 발행했음을 알 수 있습니다.
```
Subscriber #1 => 12.5
```

<br/>
마지막으로 AsyncSubject 클래스에 onComplete() 함수를 호출한 후에 구독할 때를 살펴보겠습니다.  
```
AsyncSubject<Integer> subject = AsyncSubject.create();
subject.onNext(10);
subject.onNext(11);
subject.subscribe(data -> System.out.println("Subscriber #1 => " + data));
subject.onNext(12);
subject.onComplete();
subject.onNext(13);
subject.subscribe(data -> System.out.println("Subscriber #2 => " + data));
subject.subscribe(data -> System.out.println("Subscriber #3 => " + data));
```

- 실행결과
```
Subscriber #1 => 12
Subscriber #2 => 12
Subscriber #3 => 12
```
- onComplete() 가 호출되기 바로 직전에 즉, Subject가 완료되기 전에 발행된 마지막 데이터인 12가 모든 구독자에게 전달됩니다.  
- 즉, Subject도 Observable과 마찬가지로 onComplete 알림 전달 이후에는 어떤 아이템도 발행하지 않습니다.  
- 그리고 Subject가 아이템 발행을 완료한 이후에도 마지막에 발행된 아이템을 가져올 수 있습니다.  

### 3.1.2 BehaviorSubject
두번째로 알아볼 Subject클래스는 BehaviorSubject 클래스입니다.  
- BehaviroSubject는 구독자가 자신을 구독했을 때 가장 최근에 발행한 값 또는 기본값을 발행합니다.
즉, source Observable이 가장 최근에 발행한 값을 발행합니다. 그러나 만약에 source Observable이 아직까지 발행한 값이 없다면 기본 값(default 값)을 발행합니다.
- 구독시점 이후에는 BehaviorSubject는 source Observable이 발행하는 값을 계속해서 발행합니다.
- createDefault() 함수를 이용하여 BehaviorSubject를 생성합니다. 이때 기본값으로 설정할 값을 createDefault의 인자로 넣어줍니다.

<br/>
- 마블 다이어그램
![BahaviorSubject](http://reactivex.io/documentation/operators/images/S.BehaviorSubject.png)


다음 마블다이어그램과 일치되는 코드를 예시로 BehaviroSubject 함수의 사용을 보겠습니다. ![image](https://user-images.githubusercontent.com/57262833/78527020-c9e40900-7816-11ea-915f-eb46222f0b75.png)

```
BehaviorSubject<String> subject = BehaviorSubject.createDefault("6");
subject.subscribe(data -> System.out.println("Subscriber #1 => " + data));
subject.onNext("1");
subject.onNext("3");
subject.subscribe(data -> System.out.println("Subscriber #2 => " + data));
subject.onNext("5");
subject.onComplete();
```

1. BahaviorSubject인 subject 생성할 때 초기값으로 6을 설정했습니다.
2. 첫번째 구독자가 subject를 구독할 때 6을 발행받습니다. 
그결과 6이 출력됩니다.
3. subject는 그 다음 1, 3이라는 데이터를 발행하고, 첫번째 구독자는 1, 3을 출력하게 됩니다.
4. 두번째 구독자가 subject를 구독하고 가장 최근에 발행된 값인 3을 발행받게됩니다.
5. subject는 5라는 데이터를 발행하고, 첫번째와 두번째 구독자는 3, 5를 출력합니다.
6. onComplete()를 호출하여 subject가 완료됩니다.  

출력결과는 다음과 같습니다.  
```
Subscriber #1 => 6
Subscriber #1 => 1
Subscriber #1 => 3
Subscriber #2 => 3
Subscriber #1 => 5
Subscriber #2 => 5
```

![BehaviroSubjectError](http://reactivex.io/documentation/operators/images/S.BehaviorSubject.e.png)
- BehaviorSubject에서도 AsyncSubject와 마찬가지로 error를 만나 종료하게 되면, BehaviorSubject는 이후에 구독한 구독자들에게 어떤 아이템도 발행하지 않습니다.
- 대신에 source Observable이 발행하는 error알림을 그대로 발행합니다.


### 3.1.3 PublishSubject
세번째 Subject로 PublishSubject를 알아보겠습니다.  
- PublishSubject는 가장 기본적인 Subject 클래스 입니다.
- 구독자가 subscribe() 함수를 호출하면, 구독한 시점 이후부터 발행된 데이터만 구독자에게 발행됩니다.  
AsyncSubject에서는 데이터 발행이 모두 완료된 이후에, 가장 마지막으로 발행된 데이터가 발행됬습니다.  반면에 BehaviorSubject에서는 AsyncSubject와 비슷하게 구독시점에서 가장 최근에 발행된 데이터가 발행되지만, 만약 source Observable에서 발행한 데이터가 없다면 기본값으로 설정된 아이템이 발행됩니다.  
- **PublishSubject는 생성되자마자 아이템을 발행하기 시작합니다.** 이는 PublishSubject에서 매우 중요한 사항입니다.  
- PublishSubject도 AsyncSubject와 마찬가지로 create() 함수를 사용하여 생성합니다.

<br/>
<br/>
- 다음 예시 마블 다이어그램에 해당하는 코드를 살펴보면서 PublishSubject에 대해서 더 자세히 알아보도록 하겠습니다.  
![image](https://user-images.githubusercontent.com/57262833/78528648-d5d1ca00-781a-11ea-8213-77c0156f9cf8.png)

```
PublishSubject<String> subject = PublishSubject.create();
subject.subscribe(data -> System.out.println("Subscriber #1 => " + data));
subject.onNext("1");
subject.onNext("3");
subject.subscribe(data -> System.out.println("Subscriber #2 => " + data));
subject.onNext("5");
subject.onComplete();
```
1. 구독자 1은 subject가 데이터를 발행하기 전부터 구독을 하고 있었기 때문에,  
subject가 발행하는 모든 데이터를 발행받습니다.  
발행받는 데이터 : 1, 3, 5

2. 구독자 2는 subject가 1과 3을 발행한 이후에 구독을 시작했기 때문에,  
subject가 발행하는 모든 데이터를 발행받지 못하고, 그 이후의 데이터부터 발행받습니다.  
발행받는 데이터 : 5

<br/>
<br/>
<br/>

- 만약 source Observable이 에러와 함께 종료된다면, 
PublishSubject는 에러 이후에 구독한 구독자들에게 아무런 아이템도 발행하지 않습니다.  
단, source Observable이 발행한 error 메시지만을 구독자에게 전달합니다.  

PublishSubject는 생성되자 마자 데이터를 발행하기 때문에, 구독자가 PublishSubject가 발행하는 모든 데이터를 받지 못할 가능성이 있습니다.  
따라서 source Observabel이 발행하는 모든 데이터가 이것을 구독하는 PublishSubject를 통해 모두 발행되어야한다면, 
즉, PublishSubject가 발행하는 모든데이터를 구독자가 발행받아야 한다면,  
다음과 같은 방법을 사용해야 합니다.  

1) 아이템을 발행할 객체를 PublishSubject가 아니라, create 연산자를 사용하여 Observable을 만든다.  
이는 Cold Observable 처럼 행동하도록 수동으로 만드는 방법입니다.  
그리고 이 경우에는 Observable이 데이터를 발행하기 전에 모든 구독자가 구독을 완료했는지 확인해야합니다.  

2) ReplaySubject를 사용합니다.  

그렇다면 ReplaySubject가 무엇인지 정확히 알아보도록 하겠습니다.  

<br/>
<br/>
<br/>
<br/>

### 3.1.4 ReplaySubject 
- ReplaySubject는 source Observable이 발행한 모든 아이템들을 구독자가 구독한 시점에 관계없이 구독자들에게 모두 발행합니다.  
- Subject 클래스의 원래 목적은 뜨거운 Observable을 사용하기 위함이지만, ReplaySubject는 차가운 Observable 처럼 동작합니다.  
(뜨거운 Observable은 생성과 동시에 아이템 발행을 시작하고, 차가운 Observable은 구독자가 생길 때까지 아이템을 발행하지 않습니다.   
결과적으로 뜨거운 Observable을 구독하는 구독자들은 Observable이 발행하는 모든 데이터를 받을 수 있다는 보장이 없지만, 차가운 Observable을 구독하는 구독자는 Observable이 발행하는 데이터를 모두 받을 수 있습니다.  
구독자들이 Observable이 발행하는 모든 데이터를 받을 수 있다는 점에서, RepalySubject는 차가운 Observable 처럼 동작합니다.)

- 따라서 사용시 매우 주의해야합니다.  
왜냐하면 ReplaySubject는 replay buffer라는 데이터들을 저장해놓는 버퍼를 두고 작동하는데, 이 버퍼가 저장할 수 있는 양을 넘어서 저장하게 되거나 혹은 특정 시간이 지나면 이전에 발행된 오래된 데이터들을 버리는 경우도 있기 때문입니다.

- 또한 ReplaySubject를 Observer로 사용한다면, 여러개의 스레드에서 옵저버로 이미 동작하고 있는 ReplaySubject의 onNext 메소드 혹은 다른 on메소드를 호출하면 안됩니다.  
왜냐하면 이 경우에는 동시호출(비순차호출)이 발생할 수 있기 때문입니다.  
동시 호출(비순차 호출)이 발생하게 되면 ObservabelContract에 위배되고, 어떤 아이템이나 알림이 첫번째가 되어서 재발행되어야할지에 대해 결과 Subject에서 모호성(ambigutiy)를 만들어낼 수 있습니다.  

좀 더, 자세히 설명하자면 ObservabelContract 중 Observable은 Observer들에게 알림을 순차적으로 발행해야 합니다. 즉, 병렬적으로 발행하면 안됩니다.  
즉, 모든 Observable들을 순서를 정해서 차례대로 한 순간에 한 Observer에게만 아이템 혹은 알림을 발행할 수 있습니다.  
Observable들이 각각 다른 스레드에 위치하면서 알림을 발행할 수 있지만, 그 발행된 알림 사이에는 공식적으로 무엇이 먼저 발행되었는지 순서가 정해져있어야 합니다.  

- ReplaySubject를 생성하는 연산자는 create()입니다.  
![image](https://user-images.githubusercontent.com/57262833/78860559-7dded180-7a6d-11ea-9a6a-757558e9e082.png)


다음 마블다이어그램을 바탕으로 예시코드를 살펴보겠습니다.  

```
ReplaySubject<String> subject = ReplaySubject.create();
subject.subscribe(data -> System.out.println("Subscriber #1 => " + data));
subject.onNext("1");
subject.onNext("3");
subject.subscribe(data -> System.out.println("Subscriber #2 => " + data));
subject.onNext(5);
subject.onComplete();
```
- 첫번째 구독자가 구독을 시작한 후 1, 3이라는 데이터를 발행합니다. 
- 그 후, 두번째 구독자가 구독을 시작하는데, 이때 두번째 구독자는 이제까지 subject가 발행한 모든 데이터(1, 3)을 발행받습니다.
- subect는 5를 발행합니다. 첫번째 구독자, 두번째 구독자 모두 1, 3, 5를 발행받습니다.  
- onCreate()를 호출하여 발행을 완료합니다.  

- 실행결과는 다음과 같습니다.  
```
Subscriber #1 => 1
Subscriber #1 => 3
Subscriber #2 => 1
Subscriber #2 => 3
Subscriber #1 => 5
Subscriber #2 => 5
```

## 3.2 '데이터 발행자'와 '수신자'
지금 까지 Rx를 볼 때 '데이터 발행자'를 담당하는 역할인 Observable 과 '데이터 수신자' 역할을 하는 Observer가 있는 것을 확인할 수 있었습니다.  
하지만 '데이터 발행자' 담당을 하는 것에는 Observable외에도 많은 것들이 있었습니다.  
데이터 발행자와 수신자에 어떤 것들이 속하는지 좀 더 명확히 짚어보도록 하겠습니다.  

### 3.2.1 데이터 발행자
- Observable
- Single
- Maybe
- Subject
- Completable

데이터 발행자는 클래스 개념이기 때문에 명확하게 구분됩니다.  
그러나 데이터 수신자는 Rx 문서에서도 여러가지 용어를 혼용해서 쓰기 때문에 명확하게 구분되지 않습니다.

### 3.2.2 데이터 수신자
- 구독자(Subscriber)
- 옵서버(Observer)
- 소비자(Consumer)

위의 용어 모두 데이터 수신자를 의미합니다. 참고로 알아두시면 좋을 것 같습니다.  

<br/>
<br/>
<br/>
<br/>

## 3.3 ConnectableObservable
앞서서 차가운 Observable을 뜨거운 Observable로 바꾸기 위해 Subject와 ConnectableObservable을 사용한다고 말씀드렸습니다.  
Subject에 이어서 이번에는 ConnectableObservable에 대해서 알아보도록 하겠습니다.  

- ConnectableObservable은 일반적인 ObservablerObservable과 유사합니다.  
단, ConnectableObservable은 구독자가 구독한 시점에도 아이템을 발행하지 않는다는 점이 다릅니다.  
- 대신에 connect연산자가 호출되었을 때 아이템 발행을 시작합니다.  
- 따라서 프로그래머가 원하는 시점에 Observable이 아이템을 발행하도록 만들 수 있습니다. 
- ConnectableObservable의 마블다이어그램은 다음과 같습니다.  
![ConnectableObservable](http://reactivex.io/documentation/operators/images/publishConnect.c.png)

- 여러구독자에게 Observable 하나를 공유할 수 있기 때문에, 데이터를 여러 구독자에게 동시에 전달할 때 사용합니다.
- connect()를 호출한 이후부터 아이템 발행을 시작하지만, connect를 호출한 후부터 구독하기 시작한 구독자는 subscribe() 호출 이후 발행된 데이터부터 발행받습니다.  
- ConnectableObservable을 생성하는 방법은 publish() 함수를 호출하여 일반적인 Observable을 ConnectableObservable로 바꾸어주면 됩니다. 
- 여기서 publish() 함수는 connect 함수가 호출될 때까지 데이터 발행을 유예시키는 역할을 합니다.  

다음과 같은 마블다이어그램을 바탕으로 예제코드를 살펴보겠습니다.  
![image](https://user-images.githubusercontent.com/57262833/78863210-fba5db80-7a73-11ea-8298-4906eff90bd1.png)

```
String[] dt = {"1", "3", "5"}; 
Observable<String> balls = Observable.interval(100L, TimeUnit.MILLIESECONDS)
  .map(Long::intValue)
  .map(i -> dt[i])
  .take(dt.length);
  
ConnectableObservable<String> source = balls.publish();
source.subscribe(data -> System.out.println("Subscriber #1 " + data));
source.subscribe(data -> System.out.println("Subscriber #2 " + data));
source.connect();

CommonUtils.sleep(250);
source.subscribe(data -> System.out.println("Subscriber #3 " + data));
CommonUtils.sleep(100);
```

- 발행하려는 데이터는 1, 3, 5입니다.  
Observable.interval의 인자는 두가지로, 첫번째는 시간이며, 두번째는 시간의 단위입니다.  
여기서는 100ms 단위로 0부터 차례대로 1씩 증가시키면서 Long값을 발행시킵니다.

- 다음은 map() 함수를 활용하여 interval 함수가 발행한 데이터를 각각 int타입으로 바꾸어줍니다.  마찬가지로 다시한번 map 함수를 이용하여 앞에서 만든 int값을 이용하여 배열의 값을 반환합니다.  이때 take함수를 이용하여 배열의 요소 개수 만큼만 배열의 값을 가지고 옵니다.  

- ConnectableObservable 클래스의 인자인 source를 생성합니다. 이때 위에서 생성한 Observable인 balls의 publish 함수를 호출하는 방식으로 source를 생성합니다.  
- source의 첫번째 구독자와 두번째 구독자가 구독을 시작합니다.
- source의 connect 연산자를 호출하여 source가 아이템 발행을 시작합니다.
- 그리고 250ms 를  쉰 후 세번째 구독자가 구독을 시작합니다.
connect 함수를 앞서서 호출했기 때문에 세번째 구독자는 아이템을 바로 수신합니다.  
- 다시 100ms 를 쉰 후에는 balls 객체의 데이터가 모두 발행됩니다.  

실행결과는 다음과 같습니다.  
```
Subscriber #1 => 1
Subscriber #2 => 1
Subscriber #1 => 3
Subscriber #2 => 3
Subscriber #1 => 5
Subscriber #2 => 5
Subscriber #3 => 5
```
아직 배우지 않은 interval(), take() 등 여러가지 함수가 있어서 이해가 잘 되지 않을 수 있습니다. 하지만 차차 앞으로의 포스팅에서 배워나갈 것들이기 때문에 지금은 눈으로만 익숙해지셔도 괜찮습니다.  

<br/>
<br/>
<br/>
<br/>

이번 포스팅에서는 차가운 Observable을 뜨거운 Observable로 바꾸는데 사용되는 Subject와 ConnectableObservable을 살펴보았습니다.  












