---
layout: post
title:  "Socket.io의 개요"
date:   2021-01-17 22:37:17 -400
background: "/img/classic_blue.jpeg"
lastmod : 2021-01-17 22:37:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Network
---

## 개요
LAMP(PHP)와 같은 인기있는 웹 애플리케이션 스택을 이용해서 채팅 프로그램을 작성하는 것은 매우 어렵다.  
이 방법은 변화를 위해서 서버에 폴링을 구현해야할 뿐만 아니라, 타임스탬프를 계속해서 기록해야 하며 이는 시간이 소요되는 작업이다.

이러한 이유로 `Socket`은 전통적으로 실시간 채팅 시스템에 대한 해결책이 되어 왔다. 
`Socket`은 클라이언트와 서버 간에 양방향 통신 채널을 제공한다.  
다시 말해서, `Socket`을 사용하면 서버가 클라이언트로 메시지를 보낼 수 있다는 것을 의미한다.  
이를 채팅시스템에서 활용하면 클라이언트에서 채팅 메시지를 작성하면, 서버가 이를 받아서 연결된 다른 모든 클라이언트에 전송하는 방식으로 이루어진다.


## Socket.IO 의 구성
`Socket.IO`는 2 부분으로 구성되어 있다.
1. Node.js HTTP 서버와 통합되는 부분인 socket.io
2. 클라이언트에 로드되는 라이브러리인 socket.io-client

다음은 socket.io-client를 이용한 클라이언트의 예시 코드이다.
```javascript

const app = require('express')();
const http = require('http').Server(app);
const io = require('socket.io')(http);

io.on('connection', (socket) => {
  console.log('a user connected');
});

``` 
HTTP 서버 객체를 가지고 socket 객체를 생성한다.  
소켓에 대한 연결 이벤트를 등록해두고, 이벤트가 들어오면 콘솔에 기록한다.

각 소켓 객체는 연결 이벤트를 발생시킬 뿐만 아니라, 연결이 해지되었을 때의 이벤트도 발생시킨다.  
연결 해지 이벤트 핸들러도 등록할 수 있다.
```javascript
io.on('connection', (socket) => {
  console.log('a user connected');
  socket.on('disconnect', () => {
    console.log('user disconnected');
  });
});
```  

## 이벤트 발행(Emitting Event)
Socket.io의 기본 아이디어는 우리가 원하는 이벤트를 데이터와 함께 보내고 받을 수 있다는 것이다.  
JSON 표현식으로 인코딩될 수 있는 모든 것이 전달될 수 있으며  바이너리 데이터도 지원된다.  

사용자가 메시지를 입력하면 서버가 `chat message`이벤트를 받도록 만들어보자.  
html의 script 섹션은 다음과 같이 구성된다.

```html
<script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io();

  var form = document.getElementById('form');
  var input = document.getElementById('input');

  form.addEventListener('submit', function(e) {
    e.preventDefault();
    if (input.value) {
      socket.emit('chat message', input.value);
      input.value = '';
    }
  });
</script> 
```

위의 코드에서 `socket.emit('chat message', input.value)` 부분이 `input.value` 데이터를 가지고 `chat message` 이벤트를 발행하는 부분이다.
서버의 index.js 에서는 `chat message` 이벤트 리스너를 등록해놓는다.
```javascript
io.on('connection', (socket) => {
  socket.on('chat message', (msg) => {
    console.log('message: ' + msg);
  });
});
```

## 브로드캐스팅
다음으로는 서버에서 연결된 다른 모든 사용자들에게 이벤트를 발행하는 것이다.  
Socket.io에서는 다른 '모든' 사용자들에게 이벤트를 보내는 방법으로 `io.emit()` 메서드를 지원한다.
  
```javascript
io.emit('some event', { someProperty: 'some value', otherProperty: 'other value' }); // This will emit the event to all connected sockets
```

만약 특정한 송신 소켓을 제외한, 모든 사용자에게 메시지를 보내고 싶으면, 해당 송신 소켓에서 emit()함수를 호출할 때 `broadcast`플래그를 사용하면 된다.

```javascript
io.on('connection', (socket) => {
  socket.broadcast.emit('hi');
});
```






