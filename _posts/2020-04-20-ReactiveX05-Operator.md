---
title: "ReactiveX05 - Operator(연산자)"
date: "2020-04-20 14:49:17 -400"
background: "/img/classic_blue.jpeg"
categories: ReactiveX
---

## 5. Operator(연산자)
ReactiveX에서 기본이 되는 것은 Observable과 Observer입니다.  
하지만 연산자를 사용할 때 ReactiveX에서 제공하는 더 다양한 기능을 사용할 수 있습니다.  

<br/>
<br/>
<br/>

### 5.1 생성연산자
1. interval()
2. timer()
3. range(n, m) 
- interval(), timer()와 달리 반환 값이 Integer이다.
- interval(), timer()와 달리 현재 스레드에서 실행된다.
- n부터 1씩 증가하여 총 m개의 Integer객체를 반환한다.
4. intervalRange()
- interval(), range(), take() 를 조합하여 사용하는 것을 권장한다.
5. defer()
- 구독자가 생길 때마다(subscribe()가 호출될 때 마다) 새로운 Observable을 생성한다. 그 결과, 구독자들은 각자 다른 Observable을 구독한다.  
6. repeat()
- 입력파라미터가 없으면, Observable이 발행하는 데이터를 무한히 반복하여 발행한다.
- 입력파라미터가 있으면, 입력파라미터가 반복횟수가 된다.

<br/>
<br/>
<br/>

### 5.2 변환연산자
1. concatMap()
- flatMap()과 기능이 비슷하다. 즉, source Observable의 데이터를 변형하는 함수의 반환값이 Observable이며 최종적으로 flatMap()이 반환하는 아이템은 함수가 반환하는 Observable들을 합친 Observable이다. 
- flatMap()과의 차이점: flatMap()은 입력순서대로 작업이 처리되지 않을 수 있다. 즉 interleaving이 가능하다. 하지만 concatMap()은 입력이 들어온 순서대로 작업이 반환되는 것이 보장된다.
- 대신, 순서를 보장하기 위한 작업에 시간이 소요되기 때문에, flatMap()보다 속도가 느리다.

2. switchMap()
- concatMap()이 입력이 들어온 순서대로 작업이 처리되는 것을 보장하는 반면에 switchMap()은 작업 진행중일 때 새로운 데이터가 들어오면, 기존에 진행중이던 작업을 중단하고 새로운 데이터에 대하여 작업한다. 
- 여러개의 값이 발행되었을 때 마지막에 발행된 값만 사용하고 싶을 때 switchMap()을 사용한다.
- 중간에 끊기더라도 마지막에 발행된 데이터에 대한 작업은 보장한다.

3. groupBy()
- 어떤 기준(keySelector인자, groupBy의 첫번째 인자) 으로 단일 Observable을 Observable 그룹(GroupedObservable)으로 만든다. 그리고 이 Observable 그룹을 발행하는 Observable을 만든다.
- GroupedObservable은 Obser 자신을 구성하는 Observable들이 가지고 있는 데이터들이다. 

3. scan()
- reduce()와 비슷하게 Observable을 합쳐서 결과물을 반환한다.
- reduce()와의 차이점: 
  1. reduce()는 Observable을 합친 마지막 결과를 발행하는 반면에, scan()은 실행할 때마다 결과를 반환한다. 
  2. reduce()는 source Observable이 마지막 아이템을 발행하지 않는 경우에 결과값을 발행하지 않을 수 있다. 따라서 반환 타입이 Maybe 이다. 하지만 scan()은 작업의 중간중간마다 결과물을 발행하기 때문에 반환 타입이 Observable()이다.(즉, 데이터가 반환된다는 것이 reduce() 보다는 보장되기 때문이다.)

### 5.3 결합연산자
1. zip()
- 두 Observable을 합쳐서 하나의 Observable을 만든다.
- 두 Observable을 합칠 때 인자로 주어진 함수를 이용하며, 두 Observable 중 한 Observable이 아이템을 발행하지 않는다면, 그 Observable이 아이템을 발행할 때까지 기다린다.
- zipInterval() 기법: 데이터 뿐만 아니라 시간과도 결합할 수 있다. 결과적으로 Observable의 데이터 발행을 지연시킬 수 있다.

2. zipWith()
- zip()과 역할은 똑같다. 하지만 합치는 과정 중간에 호출할 수 있다.

3. combineLatest()
- zip() 함수는 두 Observable의 아이템을 결합할 때, 결합결과 하나를 만들기 위해서 두 Observable에서 모두 아이템을 발행해야 한다.
- 2개 이상의 Observable을 결합하는데, 각 Observable에서 데이터를 발행될 대마다 데이터를 결합한다.
- 따라서 첫 번째 Observable과 두 번째 Observable이 발행하는 데이터 개수가 달라도 정상적으로 combineLatest()가 수행된다. (onCompleted 이벤트가 발생한다.)


4. merge()
- 단순하게 두 Observable을 합치는 것이다. 
- 두 Observable에서 아이템이 발행되는 순서대로 합쳐서 Observable을 구성한다.

5. concat()
- 두 Observable을 단순히 합치지만, 한 Observable의 발행이 끝날 때까지 다른 Observable은 발행을 대기한다. 
- 따라서 한 Observable이 영원히 onComplete를 전달하지 않는다면, 메모리 누수가 발생할 수 있다. 꼭 첫번째로 발행하는 Observable은 데이터 발행이 완료되어야한다.(onComplete를 전달해야 한다.)
- concat() 함수를 사용할 때는 onComplete알림이 전달되는지 여부를 확인하는 것이 중요하기 때문에, 이때 Action 객체를 사용한다.
- Rx는 Observable의 중간 결과를 확인할 수 있는 함수들을 제공한다.
ex. doOnNext(), doOnComplete(), doOnError()
이 함수들을 이용하여 적절한 로그를 찍을 수 있다.

### 5.4 조건 연산자
- Observable의 흐름을 제어하는 역할을 한다.

1. amb()
- 여러개의 Observable 중 가장 먼저 아이템을 발행하는 (혹은 onCompleted 알림이나 onError알림) Observable을 선택하여, 그 Observable의 아이템만을 반환한다.

2. takeUntil()
- 입력파라미터 Observable이 아이템을 발행할 때까지, 현재 Observable의 아이템을 반환한다.
- 입력파라미터 Observable이 아이템 발행을 시작하면, 현재 Observable에서는 onComplete 이벤트를 발생시킨다.

3. skipUntil()
- takeUntile()과 반대이다. 입력파라미터 Observable이 아이템을 발행할 때까지, 현재 Observable의 아이템을 스킵한다. 입력파라비터 Observable이 아이템 발행을 시작하면, 그 시점부터 현재 Observable이 발행하는 아이템을 반환하기 시작한다.

4. all()
- 현재 Observable이 발행하는 모든 데이터가 주어진 조건이 맞을 때에만 true값을 반환하고발행된 데이터 중 하나라도 조건에 맞지 않는 것이 있으면 fasle를 


### 5.5 수학연산자 및 그 외 연산자

1. count()
- Observable에서 발행한 데이터 개수를 반환합니다.
- 결과값이 한개 이기 때문에 반환 타입은 Single<Long> 타입으로 반환합니다.  
  
2. max()
- Observable이 발행하는 데이터 중에서 최대값을 반환한다.

3. min()
- Observable이 발행하는 데이터 중에서 최소값을 발환한다.

4. sum()
- Observable이 발행하는 데이터의 총합을 반환한다.
- 반환하는 데이터 타입에 따라서 sumInt(), sumDouble() 등 메서드 이름이 바뀐다.

5. average()
- Observable이 발행하는 데이터의 평균을 반환한다.
- 반환하는 데이터 타입에 따라서 averageInt(), averageDouble() 등 메서드 이름이 바뀐다.  

6. delay()
- Observable이 데이터 발행하는 것을 진행시킨다.
- 인자로 지연시키는 시간과 시간의 단위를 받는다.  
- 계산 스케쥴러에서 실행된다. 
> 시간과 관련된 Observable 연산자
> interval(), timer(), defer()(Callable 함수를 등록해놓고 실행을 지연시키는 함수)

7. timeInterval()
- Observable을 아이템이 발행되었을 때 이전 아이템이 발행된지 얼만큼 시간이 지났는지, 시간 간격을 발행하는 Observable로 변환한다.
- 마블 다이어그램은 다음과 같다.

![image](https://user-images.githubusercontent.com/57262833/80072905-d8a21e00-8581-11ea-95e2-cb39f7daf81f.png)









