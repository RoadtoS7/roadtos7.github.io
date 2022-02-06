---
layout: post
title: "[Android] Compose"
date: 2022-01-07 00:05:17 -400
lastmod: 2022-01-07 00:49:17 -400
sitemap:
  changefreq : daily
  priority : 1.0
categories: 
  - Android
background: "/img/classic_blue.jpeg"
---

## Compose Tutorial

- [Android Compose Tutorial](https://developer.android.com/jetpack/compose/tutorial?hl=ko) 을 공부하며, Compose에 대해 정리해보았다.  
추가적으로 현재 Compose를 사용하는 프로젝트에서 애니메이션을 적용해야 해서, 이 부분에 필요한 내용을 추가했다. 

### Basic

- onCreate()에서 `setContent{}` 블럭이 Activity의 레이아웃을 만드는 코드에 해당한다.
- `setContent{}`에서 Composable Function을 호출한다.

{% highlight Kotlin %}

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            Column {
                var isButtonClicked by remember { mutableStateOf(false) }

                ComposeStudyTheme {
                    Conversation(
                        messages = SampleData.conversationSample,
                        isClicked = isButtonClicked,
                        onMessageClick = { if (!isButtonClicked) isButtonClicked = true }
                    )
                }
            }
        }
    }

{% endhighlight %}

### Composable Function이란?
- 코드 상에서 `@Composable` 어노테이션이 붙은 함
- UI를 코드로 만들 수 있게 해주는 함수
- Composable Function은 Composable Function에서만 호출할 수 있다.

{% highlight Kotlin %}
수
fun Conversation(messages: List<Message>, isClicked: Boolean, onMessageClick: () -> Unit) {
val spaceSize: Int by animateIntAsState(if (isClicked) 20 else -50)

        LazyColumn(verticalArrangement = Arrangement.spacedBy(spaceSize.dp)) {
            items(messages) { message ->
                MessageCard(message = message, onMessageClick = onMessageClick)
            }
        }
    }

{% endhighlight %}

### Layout

- UI는 계층적으로 구성된다. 계층적인 UI는 Composable Function에서 다른 Composable Function을 호출함으로써 만들 수 있다.
- 두개의 `Text()`를 연달아서 호출하면 각 텍스트 뷰에 대한 위치 지정을 하지 않았으므로 겹쳐서 나타나게 된다.
![image](https://user-images.githubusercontent.com/57262833/152686838-ac3dcb88-de45-455d-9b17-cdbebd3307e9.png)

### Column, Row

- Row, Column을 사용하면 view를 같은 row에 두거나, column에 두는 등 쉽게 뷰를 배치할 수 있다.

### Modifier

- 뷰 사이즈, padding, appearance, layout, high-level interaction 설정할 때 사용한다.

### Spacing

- 간격을 설정하는 Composable Function

### Material Design

- Compose는 매터리얼 원칙을 지원한다.
- 매터리얼 디자인을 바로 사용할 수 있다.

### Surface

- `Surface`를 활용하여 `Text`의 모양을 변경할 수 있다.

### 예제 코드: `Column`, `Row`, `Modifier`, `Spacing`, `Material Design`, `Surface`

{% highlight Kotlin %}

@Composable
    fun MessageCard(message: Message, onMessageClick: () -> Unit) {
        Row(modifier = Modifier
            .padding(all = 8.dp)
            .clickable {
                Log.d("MainActivity", "Is Clicked ")
                onMessageClick()
            })
        {
            Image(
                painter = painterResource(id = R.drawable.flowers),
                contentDescription = "this is flower",
                modifier = Modifier
                    .size(40.dp)
                    .border(1.5.dp, MaterialTheme.colors.secondary, CircleShape)
                    .clip(CircleShape)
            )

            Spacer(modifier = Modifier.width(8.dp))
            Column {
                Text(
                    text = message.author,
                    color = MaterialTheme.colors.secondaryVariant,
                    style = MaterialTheme.typography.subtitle2,
                )
                Spacer(modifier = Modifier.height(4.dp))
                Surface(
                    shape = MaterialTheme.shapes.medium,
                    elevation = 1.dp,
                    modifier = Modifier
                        .animateContentSize()
                        .padding(1.dp)
                ) {
                    Text(
                        text = message.content,
                        style = MaterialTheme.typography.body2,
                        modifier = Modifier.padding(all = 4.dp)
                    )
                }
            }
        }
    }

{% endhighlight %}

<br/>
<br/>

## List && Animation

### `LazyColumn` & `LazyRow`

- 화면에 보이는 Column, Row만 렌더링한다.
- 따라서 길이가 긴 list를 구현할 때 사용하기 적합하다.

### `LazyColumn`

- `LazyColumn`을 호출하는 Composable Function `List`을 파라미터로 받아야 한다.
- `items`라는 이름의 자식 Composable Function을 호출한다.
- `items`은 `List`의 각 요소에 대해서 반복문을 돌면서 람다식을 호출한다.

{% highlight Kotlin %}

@Composable
fun Conversation(messages: List<Message>) {
    LazyColumn {
        items(messages) { message ->
            MessageCard(message = message)
        }
    }
}

{% endhighlight %}

### 뷰 사이즈 변화에 적용될 애니메이션 만들기

- 예제 코드에서는 사용자의 메시지를 보여주는 텍스트 뷰를 눌렀을 때 텍스트 뷰가 크기가 커져서 내용이 전부 드러나도록 했다.
- 이를 위해서는 확장 여부를 저장할 변수를 만들어야 한다.  
  그리고 이 변수의 상태 변경을 추적할 수 있어야 한다.
- Compose에서는 `remember` 함수와 `mutableStateOf`함수로 변수의 상태 변화를 추적할 수 있도록 해준다.

- 동작 과정

1. Composable Function은 `remember`를 사용하여 변수의 상태를 메모리에 저장하며 `mutableStateOf`함수에 전달된 값을 추적할 수 있다.
2. `mutableStateOf`로 지정된 값은 변화될 때, Composable Function이 뷰를 업데이트 한다.

- 만일 `remember`만 사용하고 `mutableStateOf`는 사용하지 않을 때 어떻게 되는가❓

1. 결과: `mutableStateOf`값이 초기(initial) 값으로 초기화된다.
2. 이유: `mutableStateOf`는 자신의 값이 바뀔 때 자신을 참조하고 있는 모든 Composable Function에 대한 re-compose를 하게 된다.    
   (여기서 recompose란 Composable Function을 다시 호출하여, 뷰를 다시 그린다는 것을 의미한다.)  
   이때 `remember`가 recompose될 때에도 해당 변수의 값은 유지되도록 만들어준다.

- 아래 예시 코드를 통해서 살펴보자.

{% highlight Kotlin %}

@Composable
fun HelloContent() {
    var name by remember { mutableStateOf("") }
    Text(
        text = "Hello, $name",
        modifer = Modifer.padding(bottom = 8.dp),
        style = MaterialTheme.typography.h5
    )
    OutlinedTextField(
        value = name,
        onValueChaned = { name = it },
        label = { Text("Name") }
    )
}

{% endhighlight %}

- `OutlinedTextField` 가 EditTextView에 해당한다. 이것을 통해 사용자의 입력이 들어오면,  
  `onValueChanged` 함수가 호출되어 `name` 변수의 값이 바뀐다.
- `name`변수는 `mutableStateOf` 로 생성되었기 때문에, 자신의 값이 바뀌면 자신을 참조하고 있는 모든
- `remember`로 만들어진 변수는 다른 Composable Function의 파라미터로 사용할 수 있다.
- 주의사항‼️ configuration change(ex) 화면 회전) 가 발생했을 때는 remeber 변수는 유지되지 않는다.  
  이를 위해서는 `rememberSavable`을 사용해야 한다.  
  `rememberSavable`은 `Bundle`에 담길 수 있는 값이면 무엇이든 저장해놓는다.    
  만약 Bundle에 담길 수 없는 값이라면 저장할 수 있는 객체로 직접 만들어야 한다.

- 배경색 변화 애니메이션  : `animateColorAsState()`

{% highlight Kotlin %}

val surfaceColor: Color by animateColorAsState(
    if (isExpanded) MaterialTheme.colors.primary else MaterialTheme.colors.surface,
)

{% endhighlight %}

- 메시지를 보여주는 텍스트 뷰 사이즈 변화 애니메이션: `animateContentSize()`

{% highlight Kotlin %}

Surface(
  shape = MaterialTheme.shapes.medium,
  elevation = 1.dp,
  color = surfaceColor,
  modifier = Modifier
      .animateContentSize()
      .padding(1.dp)
) {
    Text(
        text = message.content,
        style = MaterialTheme.typography.body2,
        modifier = Modifier.padding(all = 4.dp),
        maxLines = if (isExpanded) Int.MAX_VALUE else 1,
    )
}

{% endhighlight %}

<br/>
<br/>

## Compose 상태(state)

- 여기서 상태(state)란 뷰가 담고 있는 혹은 표현하는 데이터를 의미한다.
- 즉, TextView가 담고 있는 문장 혹은 단어가 TextView의 상태에 해당한다.  
  만약 ToggleButton이 현재 toggle on 되어있다면, toggle on이 Toggle Button의 상태이다.
- **Composable이 자기 자신의 상태를 갖고 있는 것은 좋지 않다.**
  자신의 상태를 직접 관리하는 Composable은 재활용하기 어렵고, 테스트하기 어려운 Composable이다.

- **Composable은 stateless하게 만들어주는 것이 좋다.**
- 이때 hoisting을 활용한다.

### hoisting이란?

- 프로그래밍 패턴 중의 하나
- Composable을 호출하는 함수에게 Composable의 상태를 맡기는 것이다.
- 가장 간단한 방법은, Composable은 자신의 상태를 parameter로 전달받고,  
  이벤트가 발생했음을 뷰로 표현하기 위해서 함수를 사용하는 것이다.

- hoisting 을 적용하지 않았을 때의 코드가 다음과 같다고 하자.

{% highlight Kotlin %}

@Composable
fun HelloContent() {
    var name by remember { mutableStateOf("") }
    Text(
        text = "Hello, $name",
        modifer = Modifer.padding(bottom = 8.dp),
        style = MaterialTheme.typography.h5
    )
    OutlinedTextField(
        value = name,
        onValueChaned = { name = it },
        label = { Text("Name") }
    )
}

{% endhighlight %}

- hoisting을 적용한 코드는 다음과 같다.

{% highlight Kotlin %}

@Composable
fun HelloScreen() {
    var name: String by rememberSavable { mutableStateOf("") }
    HelloContent(name = name, onNameChange = { name = it })
}

@Composable
fun HelloContent(name: String, onNameChange: (String) -> Unit) {
    Text(
        text = "Hello, $name",
        modifer = Modifer.padding(bottom = 8.dp),
        style = MaterialTheme.typography.h5
    )
    OutlinedTextField(
        value = name,
        onValueChaned = onNameChange(name),
        label = { Text("Name") }
    )
}

{% endhighlight %}

- `HelloContent`의 파라미터 중 `name`은 현재 OutlinedTextField가 가리키는 값이 된다.  
  `onNameChange` 는 Composable의 상태(여기서는 `name`)을 업데이트할 때 호출되는 람다식이다.
- 이렇게 Composable의 상태와 Composable의 상태를 바꾸는 함수를 상위 함수로 hoisting 함으로써,  
  Composable을 재활용하기 쉽고, 테스트하기에 용이해진다.

### 만약 TextField로 부터 받은 입력값을 다른 곳에서 사용하고자 한다면?

- ex) Database에 저장하고자 한다.
- **view model을 사용해라.**
- view model에서 observable한 holder에 Composable의 상태를 담아서 사용한다.
- 그리고 view model에 존재하는 값이 바뀌면, 자동으로 Composable이 recompose되도록 만든다.
- 코드로 보면 다음과 같다.

{% highlight Kotlin %}

class HelloViewModel : ViewModel() {
    private val _name = MutableLiveData("")
    val name: LiveData<String> = _name
}

@Composable
fun HelloScreen(helloViewModel: HelloViewModel = viewModel()) {
    var name: String by helloViewModel.name.observeAsState("")
    HelloContent(name = name, onNameChange = { name = it })
}

@Composable
fun HelloContent(name: String, onNameChange: (String) -> Unit) {
    Text(
        text = "Hello, $name",
        modifer = Modifer.padding(bottom = 8.dp),
        style = MaterialTheme.typography.h5
    )
    OutlinedTextField(
        value = name,
        onValueChaned = onNameChange(name),
        label = { Text("Name") }
    )
}

{% endhighlight %}

- 이렇게 Composable의 상태를 ViewModel에 보관하면, ViewModel은 View보다 생명주기가 길기 때문에,  
  configuration change가 발생하더라도 view의 상태를 유지할 수 있다.

- Composable의 상태를 바꾸는 함수도 viewModel로 hoisting하자.

{% highlight Kotlin %}
class HelloViewModel : ViewModel() {
    private val _name = MutableLiveData("")
    val name: LiveData<String> = _name

    fun onNameChange(newName: String) {
        _name.value = newName
    }
}

@Composable
fun HelloScreen(helloViewModel: HelloViewModel = viewModel()) {
    var name: String by helloViewModel.name.observeAsState("")
    HelloContent(name = name, onNameChange = { helloViewModel.onNameChange(it) })
}

@Composable
fun HelloContent(name: String, onNameChange: (String) -> Unit) {
    Text(
        text = "Hello, $name",
        modifer = Modifer.padding(bottom = 8.dp),
        style = MaterialTheme.typography.h5
    )
    OutlinedTextField(
        value = name,
        onValueChaned = onNameChange(name),
        label = { Text("Name") }
    )
}
{% endhighlight %}

- 이렇게 하면 ViewModel만이 view의 상태에 해당되는 `name`값을 바꾸는 개체가 된다.
- 네트워크 통신 결과에 따라서 사용자에게 보여주는 화면이 바뀌어야 하듯, UI의 상태는 app의 다른 계층에 의해서도 바뀔 수 있다.    
  따라서 UI의 상태를 ViewModel로 캡슐화하여 ViewModel만 `name` 값을 바꿀 수 있도록 하는 것이 좋다.