---
layout: post
title: "[iOS] Optional"
date: 2022-01-20 15:13:17 -400
lastmod: 2022-01-20 15:13:17 -400
sitemap:
  changefreq : daily
  priority : 1.0
categories:
  - iOS
background: "/img/classic_blue.jpeg"
---

# Optional & Unwrapping

## ＠ 옵셔널 Optional과 언래핑Unwrapping

---

```swift
let possibleNumber = "123"
	let convertedNumber = Int(possibleNumber)

var serverResponseCode: Int? = 404
serverResponseCode = nil

if convertedNumber != nil {
	print("convertedNumber has an integer value of \(convertedNumber!).")
	// <- 강제 언래핑 Forced unwrapping 
}
```

- nil 을 가질 수 있는 타입
  - **타입이 아님**, 다른 타입의 상태 관리를 도와주는 타입
  - **비어있는 상태를 표현**하기 위해서 `?`를 붙여서 optional 타입으로 사용한다.
  - ex) `Int?` → Int 타입이 옵셔널로 동작하게 된다. Int 타입의 **값이 있을 수도 있고, 없을 수도 있다.**
- API에서 주로 사용
  - 스위프트는 타입 캐스팅을 안하고 타입을 변환해주는 문법이 생성자로 제공되는 경우가 많다.
    **다른 값으로 변환할 때는 안전하게 변환하기 위해서 옵셔널로 처리된다.**
    - ex) convertedNumber의 타입은 문자를 숫자로 바꾸었을 때 넘어올 수 있는 타입이 된다.
    - 123을 숫자로 바꾸면 123이라는 숫자가 된다.
    - 값의 타입을 바꾸는 것은 **값을 바꿔서 새로운 타입의 값으로 만든다.**

      ↔ 타입 케스팅 종류: 다운 캐스팅, 업캐스팅

    - `possibleNumber` 가 숫자가 아니라면, Int로 새로운 값을 만들어도 값이 바뀌지 못한다.
      **→ nil이 들어간다.**
    - 따라서 이런 생성자는 기본적으로 optional로 생성된다.

- **optional 의 상태 2가지 존재:**
  **값이 존재하는 some 상태  ↔ 값이 비어있는 empty 상태**

### unwrapping operator !

---

- optional의 상태가 nil이 아닌 경우에 쓸 수 있는 unwrapping operator: `!`
- nil인 경우에 사용하면 error 발생
  → 반드시 nil이 아닌지 판단하고 사용해야 한다‼️

💡`!`는 에러가 나기 때문에 쓰지 말자는 쪽으로 나오기도 한다.

### nil인지 상태 체크 방법 2가지

---

1️⃣ `if` 문 사용하여 `nil`인지 **비교** 하는 방법

2️⃣ Optional Binding: `if let`

3️⃣ `guard`

### 옵셔널 바인딩 Optional Binding

---

```swift
let possibleNumber = "123.a" // 변환할 수 없는 문자열 
let convertedNubmer = Int(possibleNumber) // 변환되지 않고 nil 반환

if let actualNumber = Int(possibleNumber) {
	print("\"\(possibleNumber)\" has an integer value of \(actualNumber)")	
} else {
	print("\"\(possibleNumber)\" could not be converted to an integer")
}

let possibleNumber = "123.a"
let convertedNumber = Int(possibleNumber)
```

- optional을 **안전하게 언래핑** 하는 방법
- `키워드: if let - else`
- 사용 방법
  1. let 다음에 상수 변수 하나를 할당
  2. 옵셔널에 값이 있는 경우에는 let으로 인해 바인딩이 되어서
     actual number에 옵셔널이 언래핑되서 옵셔널 안의 값이 들어간다.
- 값이 있는 경우에는 if 문 실행 nil인 경우에는 else문이 실행된다.

### 절대적 언래핑 옵셔널 IUO 타입
Implicitly Unwrapping Optionals

---

```swift
let possibleString: String? = "An optinal String."
let forcedString: String = possibleString! // 명시적인 언래핑 표시 필요

let assumedString: String! = "An implicitly unwrapped optional string"
let implicitString: String = assumedString // 명시적인 언래핑 필요 ❌
```

- 타입 뒤에 `!` 를 붙여서 **옵셔널이지만 항상 값이 있을 것이라는 의미**
- **사용하는 경우**
1. 프로그램 **흐름상 초기값을 바로 할당할 수 없을 때** IUO를 사용
2. 옵셔널이지만 **항상 값이 있다는 것을 코드 상에서 표현**하고 싶을 때 사용
   → 항상 언래핑해서 사용 가능
- 명시적인 unwrapping 을 하지 않아도 된다!!

## 🛡 Guard

---

```swift
guard `Bool 또는 Optional Binding ` else {
	`statements`
}
```

✅ 조건이 만족되지 않았을 때, 프로그램의 실행을 `guard` 의 scope 바깥으로 내보낼때 사용

- 주의사항:
  statements 에  `else` 구문을 작성하거나, `guard` 의 scope 바깥으로 내보내는 코드 작성해야 한다.
- `guard` 의 scope 바깥으로 내보내는 키워드
  - `return`
  - `break`
  - `continue`
  - `throw`

<aside>
👨‍💻 from **Terry**

---

특정 값이 너무 중요해서 다음 코드에서 nil이 되면 안되는 경우 사용

</aside>

### 다양한 Guard 사용 방법

---

1. nil 여부 체크

```swift
func printMessage(_ message: String?) {
	guard message != nil else { return }
	print(message!)
}
```

1. guard 옵셔널 바인딩

💡guard로 옵셔널 바인딩 하면 if let으로 옵셔널 바인딩 했을 때와 달리 괄호 밖에서도 사용할 수 있다.

```swift
func printMessage(_ message: String?) {
	guard let letMessage = message else { return }
	print(letMessage)
}
```

- 출처

  [[iOS][Swift] guard 문](https://velog.io/@youngking0914/iOSSwift-guard-%EB%AC%B8)
