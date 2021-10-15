---
title: "ReactiveX_Observable & Observer"
date: 2020-03-23 14:58:17 -0400
background: "/img/classic_blue.jpeg"
categories: ReactiveX
--- 

저번 포스팅에서는 리액티브 프로그램에 대한 개념과 자바 언어와 리액티브 프로그래밍의 관계 마지막으로 RxJava 라이브러리의 역할에 대해서 알아보았습니다.  

오늘은 리액티브 프로그래밍에서 중요한 Observable 와 Observer의 개념에 대해서 알아보겠습니다.  

우선적으로 리액티브 프로그래밍과 리액티브 프로그래밍에서 Observable과 Observer의 역할을 설명하도록 하겠습니다.  
- 내용이 잘 이해되지 않더라도 이후에 차례대로 설명할 것이기 때문에 이 표현에 대해서 익숙해지는 것이 좋습니다.  

Rx에서는 Observer가 Observerable 을 구독합니다.(subscribe 합니다.)  
그 후에 Observer는 Observable이 발행하는 아이템, 혹은 아이템들이 무엇이든 간에 Observable이 아이템을 발행하면, 그에 대해 반응합니다.  
이러한 방식은 동시 작업(병렬 작업)을 쉽게 할 수 있게 합니다. 왜냐하면 Observable이 객체를 발행하는 것을 기다리는 동안에 block 상태가 될 필요가 없기 때문입니다.   
대신에 Observer라고 하는 보초병을 만들어서 Observable이 미래에 무엇을 하든지 간에 그 행동에 대해 적절한 반응할 준비가 되어있도록 합니다.  

<br/>
<br/>

## 마블 다이어그램 (Marble 다이어그램)
리액티브 프로그래밍을 알기 위해서는 다음과 같은 마블 다이어그램을 읽을 줄 알아야한다.
![Untitled](https://user-images.githubusercontent.com/57262833/77613286-b19a0180-6f6d-11ea-874f-fbbc9306c4b9.png)

마블다이어그램에 대해 상단 -> 아래, 오른쪽 -> 왼쪽으로 가면서 설명해보겠다.  
- 박스 위쪽 화살표 = 옵저버블의 타임라인으로서, 화살표의 오른쪽에서 왼쪽으로 가면서 시간의 흐름을 나타낸다.  
- 박스 위쪽 화살표 위치한 도형들 = 옵저버블이 발행하는 데이터들을 의미한다.  
- 박스 위쪽 화살표 위치한 세로 막대기 = 옵저버블이 데이터 발행을 성공적으로 완료했음을 의미한다. 이 말은 "옵저버블이 성공적으로 완료되었다." 라는 말과 똑같은 의미를 갖습니다.  
- 가운데 박스와 점선 화살표 = 옵저버블에 변형이 일어남을 의미한다. 가운데 박스 안에 있는 단어는 어떤 변형이 일어나는지를 알려준다.  
- 박스 아래쪽 화살표 = 변형의 결과로 나온 옵저버블이다.  
- 박스 아래쪽 화살표에 위치한 엑스표 = 옵저버블이 데이터를 발행하다가 오류를 만나서 비정상적으로 종료되었음을 의미한다.  

앞으로 ReactiveX에 존재하는 많은 연산자들을 이해하는데 마블다이어그램이 도움이 될 것이다.  

<br/>
<br/>
<br/>

## ReactiveX를 이용하여 리액티브 프로그래밍을 하기 위해 알아야하는 ReactiveX관련 배경지식들

1. 명령어들이 병렬적으로 실행된며, 실행 결과는 Observer에 의해서 임의의 순서대로 수집된다.  

보통 소프트웨어 프로그래밍을 할 때 우리는 작성한 코드가 순서대로 실행될 이라고 예상할 수 있다.  
**하지만 ReactiveX에서는 많은 명령어들이 병렬적으로 실행된다.**  
**그리고 실행 결과는 Obsever에 의해서 임의이 순서대로 수집된다.(captured 된다.)**  

<br/>

2. 데이터를 수집하고 변경하기 위한 체계를 Observerable이라는 형태로 정의한다. 그리고 Observer는 Observable이 아이템을 발행할 때마다 아이템을 포착하고, 
발행에 대해 응답한다.

데이터를 수집 변경하기 위해서, 기존의 프로그래밍에서는 함수를 호출했다.  
하지만 ReacriveX에서 프로그래머는 데이터를 수집하고 변형하기 위한 매커니즘을 Observable이라는 형태로 만들고, 이 Observable을 Observer로 하여금 구독하게 한다.  

Observer가 Observable을 구독하는 시점부터, 
Observable(데이터를 수집하고 변형하기 위해 미리 정의해놓은 매커니즘)은 Observer와 같이 동작한다.  
이 과정에서 Observer는 Observerable이 아이템을 발행하는 행위에 대해 응답하기 위해 대기하고 있으며, Observable이 아이템을 발행하면, 해당 아이템을 포착한다(수집한다).  
즉, Observer가 Observable을 구독하기 시작하면, Observer는 Observable이 아이템을 발행할 때 이 아이템을 포착하고, 
발행에 대해 응답하게 된다.  

3. 프로그램에서 해야할 작업이 많을 때, 각 작업이 의존적이지 않다면 병렬적으로 실행시킬 수 있다.  
- 장점에 해당한다.  

여기서 각 작업이 의존적이지 않다는 것은 서로에게 영향을 주지 않는다는 것이다.  
이렇게 작업을 병렬적으로 실행시킨다면, 전체 작업이 완료되는데 걸리는 시간은 작업들 중 가장 긴 소요시간을 가지는 작업이 완료되는데 걸리는 시간과 같다.  
만약 작업을 병렬적으로 실행하지 못한다면, 모든 작업이 완료되는데 걸리는 시간은 각 작업의 소요시간의 총합이 될 것이다.  

4. Observable은 Observer의 함수를 호출함으로써 Observer에게 아이템을 발행하거나 알림을 전달한다.  
즉, Observable이 Observer의 함수를 호출한다는 것은 Observer에게 아이템을 발행하거나 알림을 전달한다는 것과 같은 의미이다.  

앞으로의 포스팅에서는 비동기적 프로그래밍 프로그래밍을 설명하기 위해서 다음과 같은 용어를 쓸 것이다.  
> Observer는 Observable을 구독한다. (subscribe)

다른 책이나 포스팅에서는 
Observer를 Subscriber, Watcher, reactor 라고 할 수 있다.  

그리고 우리가 현재 다루는 리액티브 프로그래밍(반응형 프로그래밍)은 자주 반응형 패턴(Reactor Pattern)이라고 불린다.  

<br/>
<br/>
<br/>
<br/>

## Observable 생성 방법
1. 비동기적인 함수 호출의 반환값을 가지고 유용한 일을 하는 함수를 정의한다.이 함수를 Observer라고 한다.  

2. 위에서 언급한 비동기적인 함수를 정의한다. 즉, Observer가 반환 값을 얻을 함수를 정의한다. 이 함수를 Observable이라고 한다.  

3. Observer를 Observable에 붙인다. 이를 Observer를 Observable에 구독시킨다고 한다.   
이 작업은 Observable의 행동을 초기화 시킨다.  

4. 나머지 필요한 코드를 작성한다.  

이렇게 Observable을 생성하면, Observable에 해당하는 함수가 반환값을 리턴할 때마다, 
Observer의 함수(Observer에 해당하는 함수)는 그 반환값을 가지고 동작하게 된다.  
그리고 Observable의 반환값이 바로 Observable이 발행하는 아이템이다.  

다시한번 정리해보도록 하겠습니다.  
- 비동기적으로 호출되는 함수 = Observable  
- Observable의 반환값 = Observable이 발행하는 아이템  
- Observable이 발행하는 아이템(비동기적으로 호출되는 함수의 반환값)을 가지고 동작하는 함수 = Observer  
- Observer가 Observable을 구독한다. = Observer는 Observable에 붙인다.  

수도 코드로 나타내면 다음과 같습니다.
```
// defines, but does not invoke, the Subscriber's onNext handler
// (in this example, the observer is very simple and has only an onNext handler)
def myOnNext = { it -> do something useful with it };
// defines, but does not invoke, th- e Observable
def myObservable = someObservable(itsParameters);
// subscribes the Subscriber to the Observable, and invokes the Observable
myObservable.subscribe(myOnNext);
// go on about my business
```

여기서 myOnNext의 함수가 Observer의 일부입니다.  
subscribe 함수를 통해서 우리는 Observer와 Observable을 연결시킵니다. 즉, Observer로 하여금 Observable을 구독하게 합니다.  
위에서와 같이 subscribe 함수에는 인자가 한개만 들어갈 수도 있지만 최대 3개까지 들어갈 수 있습니다.  
그렇다면 각각의 인자의 의미에 대해서 알아보겠습니다.  

<br/>
<br/>
<br/>
<br/>

## onNext, onCompled, onError 함수
- 우리는 Observable과, 이 Observable이 반환하는 값을 가지고 동작하는 Observer를 연결시킬 때 subscribe() 라는 함수를 사용합니다.
- Observer는 총 세가지의 함수를 구현하는데, 전부 구현하지 않고 일부만 구현하기도 합니다.
- 알야할 점: Observer가 구현하는 세가지의 함수들은 Observer의 일부입니다.

- onNext:
  1. Observable이 아이템을 발행할 때마다 Observable은 Observer의 onNext 함수를 호출합니다.
  2. onNext함수는 Observable이 발행하는 아이템을 파라미터로 받습니다.
  
- onError:
  1. Observable이 데이터를 생성하는데 실패하거나 혹은 에러를 만났을 경우를 알리기 위해서 onError 함수를 사용합니다.
  2. 이 함수가 호출되면 이후에 onNext나 onCompleted 함수는 호출되지 않습니다.
  3. onError 함수는 에러에 대한 원인을 파라미터로 받습니다.  (에러에 대한 원인은 Exception, Throwable 클래스의 객체 이거나 문자열 입니다.)
  
- onCompleted:
  1. Observable은 마지막 onNext를 호출한 후에(마지막 아이템을 발행한 후에) onCompleted 함수를 호출합니다.  
  2. 단, 진행과정에서 에러를 만나지 않았을 경우에만 호출합니다.  
  
  
- Observable 제약조건에 따르면 onNext함수는 반드시 호출되는 것이 아니라 한번도 호출되지 않거나 혹은 한번이상 호출될 수 있습니다.
- onNext 함수 호출뒤로 onError나 onCompleted 함수가 호출됩니다.
- 하나의 Observable에서 onError 나 onCompleted 함수는 둘중 하나만 호출되며, 호출된 후에는  Observer 함수는 더이상 호출되지 않습니다.
- 컨벤션에 따르면 Observable이 onNext함수를 호출하는 것을 아이템 발행이라고 부르며,   onCompleted, onError 함수를 호출하는 것을 알림이라고 불립니다.(알림을 전달한다고 불립니다.)  

- 예시 코드
여기서 myOnNextm myError, myComplete 함수가 Observer의 일부입니다.  
```
def myOnNext     = { item -> /* do something useful with item */ };
def myError      = { throwable -> /* react sensibly to a failed call */ };
def myComplete   = { /* clean up after the final response */ };
def myObservable = someMethod(itsParameters);
myObservable.subscribe(myOnNext, myError, myComplete);
// go on about my business
```

이 중에서도 Observable이 아이템을 발행할 때 호출하는 onNext 함수에 대해서 특히 잘 알아두시는 것을 권장합니다.  
> onNext에 대하여 기억하기
> onNext 함수는 Observer의 일부입니다.
> Observable이 Observer의 onNext 함수를 호출하는 것을 Observable이 아이템을 발행한다고 합니다.
> onNext 함수는 발행된 아이템을 파라미터로 받아서 동작합니다
> 즉, Observer는 Observable이 발행된 아이템을 가지고 유용한 작업을 합니다.  


<br/>
<br/>
<br/>

onNext, onError, onCompleted 함수가 Observer를 구현한다는 것을 알아보았습니다.  
앞서서 subscribe 함수를 통해서 Observer와 Observable을 구독시킨다(연결시킨다)고 하였습니다.  
하지만 위의 예시코드에서 subscribe()함수가 호출된 부분을 볼 때, Observer의 일부인 세가지 함수만을 넘겨줄 뿐, Observer 자체를 따로 만들어서 subscribe() 함수에 전달하고 있지 않습니다.  

하지만 이 상태로도 코드는 정상적으로 작동합니다.  
그리고 subscribe() 함수에 위 세가지 함수를 갖춘 Observer 객체를 넘겨주어도 제대로 동작합니다.  
subscribe는 다양하게 오버라이딩 된 형태들을 가지고 있습니다.  
그렇다면 어떤 형태들을 가지고 있는지, 그리고 각 형태별로 차이점에 대해서 알아보겠습니다.

<br/>
<br/>
<br/>
<br/>

## subscribe() 함수
앞서서 우리는 Observer가 Observable을 구독하게 되면, 구독 시점 이후부터 Observer는 Observable이 아이템을 발행하는 것을 기다리기게 됩니다. 그리고 Observable이 아이템을 발행하면 그 아이템을 포착(수집)한 후, 아이템 발행에 대해서 반응하게 됩니다.  

여기서 Observer를 Observable에 구독시키는 것을 또 다른 말로 연결시키는 것이라고도 하는데,  
이렇게 구독(연결)시키는 역할을 하는 것이 subscribe() 함수입니다.  

subscribe() 함수를 통해서 Observer와 Observable을 구독(연결)시킬 수 있기 때문에, Observer는 subscribe() 함수가 호출된 이후부터, Observable이 발행하는 데이터와 OnCompleted 혹은 OnError 알림을 받을 수 있습니다.  

subscribe() 함수를 사용하는 방법으로는 일반적으로 두가지가 있습니다.
첫번째로는 subscribe() 함수에 Observer를 구성하는 함수들(onNext, onCompleted, onError에 해당되는 함수들)을 파라미터로 전달하는 것입니다.  
파라미터로 전달된 세가지 함수들을 이용하여 Observer가 생성되며, 생성된 Observer가 Observerable을 구독하게 됩니다.  

두번째로는 onNext, onCompleted, onError 함수를 가지는 인터페이스를 구현한 객체를 파라미터로 전달하는 것입니다.  
보통 이 세가지 함수를 갖는 인터페이스를 구현한 객체는 Observer클래스와 Subscribe 클래스의 객체에 해당합니다.  
파라미터로 전달된 객체들이 Observable을 구독하게 됩니다.  

게다가 첫번째의 경우에는 꼭 세가지의 함수를 모두 파라미터로 전달하지 않고 일부만을 전달하여도, 전달된 일부만을 가지고 Observer 객체가 생성됩니다.  
단, 구현하지 않은 함수에 해당하는 알림에 대해서는 아무런 동작도 일어나지 않게 됩니다.  

subscribe() 함수의 원형을 본다면 이에 대하여 이해가 될 것입니다.  
<img width="698" alt="subscribe" src="https://user-images.githubusercontent.com/57262833/78215899-a4dc5700-74f3-11ea-8494-885d524efa5a.png">

subscribe() 함수의 원형에 대해서 각각 들여다 보겠습니다.  
1. 아무 파라미터도 전달하지 않으면서 subscribe()를 호출할 수 있습니다.  
파라미터가 없는 경우에도, subscribe() 함수를 호출한 해당 Observable을 구독합니다.  
해당 Observable을 구독하는 Observer가 생성되지만, Observer에 아무런 함수도 구현되지 않았기 때문에 Observable의 아이템 발행이나 알림에 대해서 아무런 동작도 일어나지 않습니다.  

2. 함수 한개를 파라미터로 전달하면서 subscribe() 함수를 호출할 수 있습니다.  
이 경우에 파라미터로 전달된 함수를 onNext 함수로서 가지는 Observer가 생성됩니다.  
그리고 생성된 Observer가 subscribe() 함수를 호출한 Observable를 구독합니다.  
결과적으로 Observable이 아이템을 발행하면 발행된 아이템을 가지고 파라미터로 전달된 함수가 동작하게 됩니다.  

3. 함수 두개를 파라미터로 전달하면서 subscribe() 함수를 호출할 수 있습니다.
이 경우에는 첫번째 함수를 onNext 함수로서, 두번째 함수를 onError 함수로서 가지는 Observer가 생성됩니다.  
그리고 생성된 Observer 가 subscribe() 를 호출한 Observable을 구독합니다.  

4. 함수 세개를 파라미터로 전달하면서 subscribe() 함수를 호출할 수 있습니다.  
이 경우에는 첫번째 함수를 onNext 함수로서, 두번째 함수를 onError 함수로서, 세번째 함수를 onCompleted 함수로서 가지는 Observer가 생성됩니다.  
그리고 생성된 Observer 가 subscrbie()를 호출한 Observable을 구독합니다.  


5. Observer 클래스 객체를 파라미터로 전달하면서 subscribe() 함수를 호출할 수 있습니다.  
이 경우에는 파라미터로 전달된 Observer가 subscribe()를 호출한 Observable을 구독합니다.    
그리고 Observable이 아이템을 발행할 때마다, Observer내부에 구현되어있는 onNext, onError, onCompeted 함수가 발행되는 아이템에 따라서 호출됩니다.  

위의 원형에서 Observer 객체를 전달할 때를 제외한 모든 경우에서 Disposable 인터페이스 객체가 반환되는 것을 확인할 수 있습니다.  

Disposable 객체는 Rx에서 Subscription 객체에 해당하며, 아래의 2가지 함수만 가지고 있습니다.  
1. dispose()
2. isDisposed()

먼저 dispose()는 Observable이 더이상 아이템을 반환하지 않도록 구독을 취소하는 역할을 합니다.  
Observable 계약(Contract) 문서에 따르면, Observable이 onCompleted 알림을 전달하면 자동으로 dispose() 함수가 호출되어 구독자와의 관계가 끊어집니다.  

만약  onComplete알림이 정상적으로 전달이 되었다면, 우리는 따로 dispose() 함수를 호출할 필요가 없습니다.  

isDisposed() 함수는 Observable이 아이템을 발행하는 것을 중단했는지(구독이 취소되었는지)를 확인하는 함수입니다.  
true 가 반환되면 구독이 취소된 것이고, false가 반환되면 아직 구독이 취소되지 않았음을 의미합니다.  


이제까지 subscribe() 함수의 역할에 대해서 알아보았습니다.  
앞서서 subscribe() 함수를 호출하여 Observer가 Observable을 구독하게 된 이후부터, Observable이 발행하는 아이템을 Observer가 반응한다고 했습니다.  

<br/>
<br/>
<br/>
<br/>

## 구독취소(Unsubscribing)
RX에는 특별한 옵저버 인터페이스인 Subscriber 가 존재하며, 이 인터페이스에는 unsubcribe() 메소드가 존재합니다.  
(RX는 여러 언어로 구현되어있는데 구현되어있는 언어에 따라서 Subscriber 인터페이스는 존재하지 않을 수도 있습니다.)  

Subscribe 인터페이스를 구현한 객체가 현재 구독중인 객체에 더이상 관심이 없음을 나타내기 위해서 unsubscirbe() 함수를 이용합니다.  

만약 자신의 모든 구독자가 unsubscribe() 를 호출하여 더이상 구독자가 없는 Observable은 새로운 아이템 발행을 중단할 수 있습니다.  

한 Observer가 unsubscribe() 를 호출하여 구독을 취소하게 되면, 해당 Observer가 구독하고 있던 Observable에게 적용되던 연산자 체인에 존재하는 연산자들이 취소되는데, 이는 연산자 체인의 역방향으로 일어납니다.  
그리고 이것은 연산자 체인의 연산자들이 데이터를 발행하는 것을 중단시킵니다.  

하지만 구독자가 subscribe() 를 호출하자마자 데이터 발행이 중지되지는 않습니다.  
Observable의 구독자가 더이상 없게 되더라도 그 이후에 한동안은 데이터를 생성하고 발행할 수 있습니다.  
<br/>
<br/>
<br/>
<br/>

## 뜨거운 Observable, 차가운 Observable
Observable에는 뜨거운 Observable과 차가운 Observable이 있습니다.  
이 둘을 구분하는 기준은 옵저버블의 데이터를 발행하는 시점입니다.

- 뜨거운 Observable은 생성과 동시에 데이터가 발행될 수 있습니다.  
그리고 뜨거운 Observable이 데이터를 발행하기 시작한 후에 Observer가 이 Observable을 구독하게 되면, 구독한 시점 이후부터 발행되는 아이템를 관찰할 수 있습니다(받을 수 있습니다).  
Observer는 구독 이전에 발행된 아이템은 놓치게 됩니다.  

- 차가운 Observable은 Observer가 자신을 구독할 때까지 데이터를 발행하지 않습니다.  
따라서 차가운 Observable을 구독하는 Observer는 자신이 구독하는 Observable이 발행하는 데이터 시퀀스를 처음부터 관찰할 수 있습니다.  

차가운 Observable과 뜨거운 Observable의 차이를 알아두는 것은 매우 중요합니다.  
그렇기 때문에 차가운 Observable과 뜨거운 Observable에 대해서는 이후의 포스팅에서 더 자세히 다루도록 하겠습니다.  

또한 Rx에는 "Connectable" Observable 이라고 불리는 것이 있습니다.  
Connectable Observable은 자신을 구독하는 Observer의 존재 여부에 관계없이 Connectable Observable의 connect 함수가 호출될 때까지 데이터를 발행하지 않습니다.  
Connectable Observable에 대해서도 이후의 포스팅에서 더 자세히 다루도록 하겠습니다.  
그때까지는 Connectable Observable은 자신의 connect 함수가 호출될 때까지 아이템을 발행하지 않는 Observable이라고 기억해두시면 됩니다.  

<br/>
<br/>
<br/>
<br/>

## 연산자를 통한 Observable의 조합
우리는 Observable 연산자를 이용하여 Observable을 조합할 수 있습니다.  
Observable과 Observer에 대한 이해는 Rx를 시작하는데 매우 중요합니다. 하지만 Rx의 진정한 힘은 연산자를 가지고 Observerable을 조합할 때 나옵니다. 
연산자들은 Observable이 발해한 데이터를 가지고 변형하고, 결합하고, 조작하고, 작업할 수 있도록합니다.  
또한 Rx연산자들은 콜백의 장점인 비동기적인 작업들을 선언하는 방식으로 효율적으로 구성할 수 있도록하지만, 일반적인 비동기 시스템이 가지는 단점인 콜백이 중첩되는 형상은 일어나지 않습니다.  

앞으로의 포스팅에서는 다양한 연산자에 대해서도 다룰 것입니다.  

<br/>
<br/>
<br/>
<br/>

## 연산자 체이닝
Rx에서 연산자들은 Observable을 가지고 연산하여 결과값을 반환합니다.  
우리는 이러한 연산자들을 체인과 같이 엮어서 사용할 수 있습니다.  
체인에서 각각의 연산자들은 자신의 앞에 있는 연산자가 반환한 Observable을 가지고 작업합니다. 

즉, Observable 연산자 체인에서는 가장 오리지널 Observable을 가지고 독립적으로 실행되지 않습니다. 체인에 있는 연산자들은 차례대로 실행되며, 각각의 연산자들은 체인상에서 자신의 앞에 위치한 연산자가 반환한 Observable을 가지고 작업합니다. 

<br/>
<br/>
<br/>
<br/>

## 네이밍 컨벤션(Naming Convengion)
Rx에서는 특정하게 사용하는 네이밍 컨벤션이 존재하지 않습니다.  

<br/>
<br/>
<br/>
<br/>

다음 포스팅에서는 이번 포스팅에서 설명한 뜨거운 Observable과 차가운 Observable에 대해서 자세히 다루도록 하겠습니다.  











