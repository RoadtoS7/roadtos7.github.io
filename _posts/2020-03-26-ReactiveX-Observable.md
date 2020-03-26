---
title: "ReactiveX_Observable"
date: 2020-03-23 14:58:17 -0400
categories: ReactiveX
--- 

저번 포스팅에서는 리액티브 프로그램에 대한 개념과 자바 언어와 리액티브 프로그래밍의 관계 마지막으로 RxJava 라이브러리의 역할에 대해서 알아보았습니다.  

오늘은 리액티브 프로그래밍에서 중요한 Observable 와 Observer의 개념에 대해서 알아보겠습니다.  

우선적으로 리액티브 프로그래밍과 리액티브 프로그래밍에서 Observable과 Observer의 역할을 설명하도록 하겠습니다.  
- 내용이 잘 이해되지 않더라도 이후에 차례대로 설명할 것이기 때문에 이 표현에 대해서 익숙해지는 것이 좋습니다.  

Rx에서는 Observer가 Observerable 을 구독합니다.(subscribe 합니다.)  
그 후에 Observer는 Observable이 발행하는 아이템, 혹은 아이템들이 무엇이든 간에 그에 대해 반응합니다.  
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
- 박스 위쪽 화살표 위치한 세로 막대기 = 옵저버블이 데이터 발행을 성공적으로 완료했음을 의미한다. 이 말은 "옵저버블이 성공적으로 완료되었다." 라는 말과 같다.  
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
** 하지만 ReactiveX에서는 많은 명령어들이 병렬적으로 실행된다.**  
** 그리고 실행 결과는 Obsever에 의해서 임의이 순서대로 수집된다.(captured 된다.)**  

<br/>

2. 데이터를 수집하고 변경하기 위한 체계를 Observerable이라는 형태로 정의한다. 그리고 Observer는 Observable이 아이템을 발행할 때마다 아이템을 포착하고, 
발행에 대해 응답한다.

데이터를 수집 변경하기 위해서, 기존의 프로그래밍에서는 함수를 호출했다.  
하지만 ReacriveX에서 프로그래머는 데이터를 수집하고 변형하기 위한 매커니즘을 Observable이라는 형태로 만들고, 이 Observable을 Observer로 하여금 구독하게 한다.  

Observer가 Observable을 구독하는 시점부터, 
Observable(데이터를 수집하고 변형하기 위해 미리 정의해놓은 매커니즘)은 자신이 발행하는 아이템을 포착하고,
그에 대해 응답하기 위해 대기중인 Observer와 같이 동작하게 된다.  
즉, Observer가 Observable을 구독하기 시작하면, Observer는 Observable이 아이템을 발행할 때 이 아이템을 포착하고, 
발행에 대해 응답하게 된다.  

3. 프로그램에서 해야할 작업이 많을 때, 각 작업이 의존적이지 않다면 병렬적으로 실행시킬 수 있다.  
- 장점에 해당한다.  

여기서 각 작업이 의존적이지 않다는 것은 서로에게 영향을 주지 않는다는 것이다.  
이렇게 작업을 병렬적으로 실행시킨다면, 전체 작업이 완료되는데 걸리는 시간은 작업들 중 가장 긴 소요시간을 가지는 작업이 완료되는데 걸리는 시간과 같다.  

4. Observable은 Observer의 함수를 호출함으로써 Observer에게 아이템을 발행하거나 알림을 전달한다.  
즉, Observable이 Observer의 함수를 호출한다는 것은 Observer에게 아티엠을 발행하거나 알림을 전달한다는 것과 같은 의미이다.  

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

2. 위에서 언급한 비동기적인 함수를 정의한다. 즉, Observer가 반환 값을 얻을 함수를 정의한다. 이 함수를 OBservable이라고 한다.  

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
// defines, but does not invoke, the Observable
def myObservable = someObservable(itsParameters);
// subscribes the Subscriber to the Observable, and invokes the Observable
myObservable.subscribe(myOnNext);
// go on about my business
```

여기서 myOnNext의 함수가 Observer의 일부입니다.



