---
layout: post
title: "[Swift] Initializer"
date: 2022-03-29 11:59:17 -400
lastmod: 2022-03-30 00:02:17 -400
sitemap:
  changefreq : daily
  priority : 1.0
categories: 
  - Swift
background: "/img/classic_blue.jpeg"
---

# intializer

## intializtion 란❓

---

1. 인스턴스를 사용하기 위한 준비 작업을 하는 것
2. swift에서는 initialization 단계에서 모든 stored property가 값을 가져야 한다.

## initializer  란❓

---

1. 역할: 
    1. initialization 작업을 수행하는 메서드 
    2. 인스턴스를 생성한다.
2. 표기법

```swift
init() {
	// intializer 내용
}
```

## Default Property Value

---

- stored property의 선언문에 초기값을 같이 지정하는 것을 
”Default Property Value를 할당한다.”라고 표현
- 만일 특정 프로퍼티가 항상 같은 초기 값을 갖는다면 initialzer를 활용하지 않고, default property value로 할당하는 것이 좋다.
    
    b) property 선언문과 값을 할당하는 부분이 같이 존재해서 코드를 읽기 더 쉽다.
    

## Customizing Initialization

---

1. parameter intializer를 만들 수 있다.
2. optional property types
    - nil 값이 default 값으로 할당된다.
3. constant property
    - init 메서드 안에서 값을 할당할 수 있다.
    - init 메서드 내에서는 언제든 값을 바꿀 수 있다❓

## Default Initializer

---

- Default Initializer가 주어지는 조건
1. 모든 stored property가 default 값을 가짐
2. parameter initalizer를 만들지 않고

### member wise intialize

- structure 타입에는 paramter instializer를 만들지 않으면 member wise intializer가 주어진다.
- initalizer에 파라미터 이름을 꼭 서주는데, 이때 default 값을 갖는 프로퍼티의 이름을 빼고 호출할 수 있다.

<aside>
💡 default initializer와 memberwiser initializer를 유지하면서 custom intialzier를 추가하고 싶으면,
extension을 활용하라‼️

</aside>

## Initialization Delegation

---

- initailization 작업을 같은 클래스, 구조체 내의 다른 initializer에게 위임하는 것

### value types (enum, structure)

- self.init을 호출

## class initialization delegation

---

- 상속의 특성상 특정한 규칙 가짐
- 규칙을 알아보기에 앞서 designated initializer, convenience initializer 용어를 알아야 함

### designated initializer

- primary intializer
- class의 모든 property 값을 초기화해야 한다.
- super class의 designated initializer를 호출해야 한다.
- 모든 class는 적어도 하나의 designated initializer를 가져야 함
    - Automatice intializer inheritance 원칙에 따라서 일부러 정의하지 않아도 된다.
- default initalizer도 이에 해당

### convenience initalizer

- 가져도 되고, 가지지 않아도 된다.
- 사용하는 경우: initalization의 간단한 수행 코드 제공

### class initailization delegate 규칙

1. designated 이니셜라이저는 super 클래스의 designated 이니셜라이저를 반드시 호출해야 한다.
2. convenient 이니셜라이저는 같은 class에 위치한 이니셜라이저를 호출해야 한다.
3. convenient 이니셜라이저는 같은 class에 위치한 designated 이니셜라이저를 최종적으로 호출해야 한다.

### Two-Phase Intialization (2단계 초기화)

- class initialization은 2단계로 이루어져야 한다.
1. 클래스의 designated intializer는 선언된 프로퍼티를 모두 초기화해야 한다.
    
    1단계 후, 부모 클래스와 자식 클래스의 모든 프로퍼티가 초기화된다.
    
2. 부모 클래스의 프로퍼티와 자식 클래스의 프로퍼티를 변경한다.

### **Swift Compiler는 2단계 초기화가 잘 지켜졌는지를 체크한다. (safety check)**

- swift 컴파일러는 intialization delegate시 **class initailization delegate 규칙**과 **initalization delegate와 self 참조와 관련된 규칙**을 지키는제 체크하여, 지키지 않으면 compile error를 발생시킨다.
- Swift Compiler는 2단계 초기화 준수 여부를 확인하기 위해 다음 4가지 조건을 지켰는지 확인한다.
1. designated property는 클래스의 property를 모두 초기화한다.
2. super 클래스의 designated initializer를 호출하여, super 클래스의 property를 초기화한다.
3. convenience intializer는 다른 intializer를 호출한 후에, property 값을 할당할 수 있다.
    
    b) 만일 convenience intializer에서 프로퍼티에 값을 할당한 후, 다른 intializer를 호출하면, convenience initalizer가 할당한 값은 사라질 수 있다.
    
4. 1단계 Initialization이 끝난 후 클래스의 프로퍼티와 메서드에 접근할 수 있다. 

<aside>
💡 **class initialization delegate 관련하여 이러한 규칙이 존재하는 이유**

---

위 규칙이 지켜질 경우, class의 모든 propert가 빠짐없이 초기화되는 것이 보장되며, 자식 클래스에서는 부모클래스에서 property 초기화 작업이 이루어진 후에야 property에 접근할 수 있다.

이렇게 되어야, 자식 클래스에서 설정한 property의 값이 부모 클래스의 initialization 작업으로 인해 사라지는 일이 발생하지 않으며, 개발자가 예상치 못하게 intialization이 일어나지 않는다.

</aside>

## Initialization & Overidding

---

- Swift의 클래스는 부모 클래스의 intializer를 자동으로 상속받지 않는다.
- 특정 조건을 만족했을 때에만 상속받는다. (추후에 [Automatic Intializer Inheritance](https://www.notion.so/intializer-3d1cd5ed8faf4c8da8ce0f89d2012025)에서 설명 예정)
- 따라서 부모 클래스의 intializer를 사용하고 싶으면, 자식 클래스에서도 동일한  initializer를 만들어서 사용하는 방법 밖에 없다.
- 이때 만일 부모 클래스의 designated intializer와 동일한 intializer를 만든다면, `override` 키워드를 꼭 붙여 주어야 한다.
    - 이유: 자식 클래스의 initializer를 호출하면, intialization delegate 규칙에 의해서,  부모 클래스의 designated intializer까지 연쇄적으로 호출된다.
        
        반면에 부모 클래스의 convenience intializer와 동일한 것을 자식 클래스에서 만들때에는 부모 클래스의 convenience intializer는 호출될 일이 없으므로, `overrride` 키워드를 붙이지 않아도 된다.
        

### Automatic Initalizer Inheritance

- Swift에서 자식 클래스는 부모 클래스의 intialilzer를 상속받지 않지만, 
특정 조건을 만족할 경우 자동으로 상속받는다.
- 부모 클래스의 initializer 자동 상속 조건
    1. 자식 클래스의 property가 모두 default 값을 갖는다.
    2. 자식 클래스의 designated intializer가 존재하지 않을 경우, 부모 클래스의 designated initalizer를 상속받는다.
    3. 자식 클래스가 designated intializer를 모두 구현한 경우 (2번 경우도 포함된다.), 부모 클래스의 convenience intializer를 상속받는다.

### Foo 클래스

```swift
class Food {
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    convenience init() {
        self.init(name: "[Unamed]")
    }
}
```

![Untitled](intializer%20952f5/Untitled.png)

 

### RecipeIngredient

```swift
/**
Food 클래스의 모든 designated class를 구현함. -> super 클래스의 convenience initializer를 자동으로 상속받는다.
 */
class RecipeIngredient: Food {
    var quantity: Int
    init(name: String, quantity: Int) {
        self.quantity = quantity
        super.init(name: name)
    }
    
    convenience override init(name: String) { // Food 클래스의 designated class를 구현한 intializer
        self.init(name: name, quantity: 1)
    }
    
    // init() : super class의 convenience initializer를 상속받아 생긴 것
}
```

![Untitled](intializer%20952f5/Untitled%201.png)

### FoodCartItem

```swift
/**
 designated initalizer를 정의하지 않는다. (조건1 충족)
 -> super 클래스의 designated intializer를 상속받는다. (조건2 충족)
 -> super 클래스의 convenience intailizer를 상속받는다.
 */
class FoodCartItem: RecipeIngredient {
    var purchased = false
    var description: String {
        var output = "\(quantity) x \(name)"
        output += purchased ? "✔" : "x"
        return output
    }
    
    // init(name: String, quantity: Int) : super class의 designated initializer를 상속받아 생긴 것
    
    // init(name: String) : super class의 convenience initializer를 상속받아 생긴 것
    
    // init() : super class의 convenience initializer를 상속받아 생긴 것
}
```

![Untitled](intializer%20952f5/Untitled%202.png)

## Failable Initializer

---

- optional type을 반환하는 intializer
- 언제 사용?
    
    initializer 파라미터 값이 잘못되어 인스턴스를 초기화할 수 없는 경우, 이를 알리기 위해 nil을 반환하고 싶을 때 사용한다.
    
- 초기화를 실패한 지점을 `return nil` 로 표기한다.
    - initiallizer는 값을 반환하지 않는다. 
    따라서 성공했을 때에는 return 을 사용하면 안된다.
    값의 반환 용도가 아닌, Failable Initializer가 초기화를 실패한 지점을 표기하기 위해서 return nil을 사용하는 것이다.
    
    ```swift
    struct Animal {
    	let species: String 
    	init(sepecies: String) {
    		if s
    	}
    }
    ```
    
- override
    1. failable initializer가 failable initializer override 가능 
    2. non failable initialzer가 failable initializer override 가능
    
- delegate
    1. failable initializer가 failable initializer 에게 delegate 가능
    2. failable initializer가 non failable initializer에게 delegate 가능
        - 초기화 과정에서 fail될 수 있는 로직을 추가할 경우 사용

### Failable Initialzier for Enumerations

```swift
enum TemperatureUnit {
    case kelvin, celsius, fahrenheit
    
    init?(symbol: Character) {
        switch symbol {
        case "K":
            self = .kelvin
        case "C":
            self = .celsius
        case "F":
            self = .fahrenheit
        default:
            return nil
        }
    }
    
    static func test() {
        let f = TemperatureUnit(symbol: "F")
        if f != nil {
            print("initialization succeed")
        }
        
        let unknownUnit = TemperatureUnit(symbol: "X")
        if unknownUnit == nil {
            print("initialization fail")
        }
    }
}
```

- Enumueration하고 같이 사용하여, 파라미터에 맞는 Enumeration case를 반환하는 Intializer를 구현할 수 있다.
- 이 initializer는 파라미터에 맞는 initializer가 없으면 nil을 반환한다.

### Failable Initialzier for Enumerations with Raw Values

```swift
enum TemperatureUnitWithRawValues: Character {
    case kelvin = "K", celsius = "C", fahrenheit = "F"
    
    static func test() {
        let fahrenheitUnit = TemperatureUnitWithRawValues(rawValue: "F")
        if fahrenheitUnit != nil {
            print("initialization succeed")
        }
        
        let unknownUnit = TemperatureUnitWithRawValues(rawValue: "X")
        if unknownUnit == nil {
            print("initialization fail")
        }
    }
}
```

- Raw Value를 갖는 Enumerations은 `rawValue` 파라미터를 받는 failable intializer를 자동으로 갖는다.
- 이 failable initializer는 `rawValue` 파라미터 값과 같은 Raw Value를 갖는 인스턴스를 찾아서 반환한다.
    
    만일 일치하는 인스턴스가 없다면 nil을 반환한다.
    

### Propagation of Initializtion Failure

- Initialization delegate에서 error 발생시 전체 initialization 작업이 취소된다.

### Force Unwrapped(!) failable initializer

- syntax
    
    ```swift
    init! {
    	 // do something
    }
    ```
    
- 언제 사용?
    
    

## Required Initializers

```swift
class RequiredSomeClass {
    required init() {
        print("required init in SomeClass")
    }
    
    init(value: String) {
        print(value)
    }
}

class SomeSubClass: RequiredSomeClass {
    var subValue: String
    required init() {
        self.subValue = "subValue"
        super.init()
    }
}
```

- subsclass에서 initializer를 구현하도록 강제시킬 때 사용한다.
- `override` 표기할 필요 없다.

## Closure 혹은 Function 이용하여 Default Value 설정하기

---

- closure와 function을 사용하여 stored property의 값을 할당할 수 있다.
- 언제 사용? 계산 결과를 stored property의 default value로 할당해야 하는 경우
- 작성 방법
    
    ```swift
    class ChessBoard {
        let boardColors: [Bool] = {
            var tempBoardColors: [Bool] = []
            var isBlack = false
            
            for i in 1...8{
                for j in 1...8 {
                    tempBoardColors.append(isBlack)
                    isBlack != isBlack
                }
                isBlack != isBlack
            }
            
            return tempBoardColors
        }()
        
        func squareIsBlackAt(row: Int, column: Int) -> Bool {
            return boardColors[row * 8 + column]
        }
    }
    ```
    
    1. stored property와 같은 타입의 temporary 값을 생성
    2. 1에서 만든 temporary 값을 수정
    3. temporary 값을 반환