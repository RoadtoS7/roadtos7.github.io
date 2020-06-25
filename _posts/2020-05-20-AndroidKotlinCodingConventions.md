
## 코틀린 코딩 컨벤션
### 1. Class 구성 순서
- Class 구성은 다음과 같은 순서로 배치됩니다.
1. 속성(변수) 선언, init 블럭 
2. Secondary 생성자
3. 함수 선언
4. Companion object

- 메소드 선언을 알파벳 순서로 하지 않습니다. 또한 일반 메소드와 확장 메소드를 구분하지 않습니다.
- 대신 관련된 것들을 모아서 클래스를 읽는 사람이 위에서 아래로 소스 코드를 읽었을 때 무슨 일이 일어나고 있는지 이해할 수 있도록 배치합니다. 
- 중첩 클래스는 해당 클래스를 사용하는 코드 다음에 위치 십