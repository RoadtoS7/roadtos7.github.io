---
layout: post
title:  "Socket.io의 개요"
date:   2021-01-17 23:41:17 -400
lastmod : 2021-01-17 23:41:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Network
background: "/img/classic_blue.jpeg"
---

## :clap: Introduction
Socket.io는 브라우저와 서버간에 **실시간으로 양방향 통신**이 가능하게 하는 라이브러리이다.    
Socket.io는 **이벤트기반 통신**을 지원한다.  

Socket.io는 HTTP Node.js 서버와 통합되는 부분인 socket.io과  
자바스크립트로 작성되었으며 브라우저에서 사용되는 라이브러리인  socket.io-client로 구성되어 있다.  

## :one: Socket.io의 동작방식
클라이언트에서 가능하다면 **웹소켓 커넥션**을 시도하며 가능하지 않다면 **HTTP 롱 폴링**으로 다시 들어간다. 
(웹소켓: 브라우저와 서버사이에 양방향으로 인터렉션을 가능하게 하는 커뮤니케이션 세션을 열어주는 API이다.  
이 API를 가지고 답에 대해 서버로 폴링할 필요 없이 서버로 메시지를 보내고 응답 이벤트를 받을 수 있다.)

웹 소켓은 서버와 브라우저간에 완전한 이중화 및 낮은 지연시간 채널을 제공하는 커뮤니케이션 프로토콜이다.
  
다음 웹 소켓 예시에서는 아래의 조건들을 가정합니다. 
- 모든 브라우저에서 웹소켓 api를 제공한다 (2020년 현재 97%의 브라우저에서 웹소켓 api를 제공한다.)
- 클라이언트와 웹 소켓간의 통신을 방해하는 프록시, 방화벽과 같은 요소가 없다.

Socket.io를 사용할 때, 클라이언트를 **웹 소켓 API를 둘러싼 아주 얇은 막**으로 생각할 수 있습니다.

따라서 다음과 같은 클라이언트 사이드 코드를 작성하는 것이 아니라,

```javascript
const socket = new WebSocket('ws://localhost:3000');

socket.onopen(() => {
  socket.send('Hello!');
});

socket.onmessage(data => {
  console.log(data);
});
``` 

클라이언트에서는 아래와 같은 코드를 작성하게 됩니다.
```javascript
const socket = io('ws://localhost:3000');

socket.on('connect', () => {
  // either with send()
  socket.send('Hello!');

  // or with emit() and custom event names
  socket.emit('salutations', 'Hello!', { 'mr': 'john' }, Uint8Array.from([1, 2, 3, 4]));
});

// handle the event sent with socket.send()
socket.on('message', data => {
  console.log(data);
});

// handle the event sent with socket.emit()
socket.on('greetings', (elem1, elem2, elem3) => {
  console.log(elem1, elem2, elem3);
});
```

Socket.io를 사용하여 서버에서 작성하는 코드도 이와 비슷하다. 
Node.js의 `EventEmitter`클래스를 상속받는 `socket` 객체를 얻을 수도 있다.   
```javascript
const io = require('socket.io')(3000);

io.on('connection', socket => {
  // either with send()
  socket.send('Hello!');

  // or with emit() and custom event names
  socket.emit('greetings', 'Hey!', { 'ms': 'jane' }, Buffer.from([4, 3, 3, 1]));

  // handle the event sent with socket.send()
  socket.on('message', (data) => {
    console.log(data);
  });

  // handle the event sent with socket.emit()
  socket.on('salutations', (elem1, elem2, elem3) => {
    console.log(elem1, elem2, elem3);
  });
});
```

**Socket.IO는 플레인 WebSocket 보다 [추가적인 기능](https://socket.io/docs/v3#Features) 을 제공한다.**


## :two: Socket.IO에서 착각하기 쉬운 것들
1. Socket.IO는 Web Socket의 구현이 아니다.
Socket.IO는 가능한 경우에만 Web Socket을 전송계층에서 사용하지만, Socket.IO는 전송하는 패킷에 메타 데이터를 추가한다.  
이로 인해 Web Socket 클라이언트는 Socket.IO 서버와 연결할 수 없다.  
그리고 Socket.IO 클라이언트는 일반 Web Socket 서버와 연결할 수 없다.  

## :three: 이벤트 기반의 의사소통
클라이언트 사이드와 서버 사이드의 `socket` 객체는 EventEmitter 클래스를 상속받는다.  
따라서 `socket.emit()`을 호출하면 이벤트가 전송된다.  
그리고 이벤트를 수신하는 것은 이벤트 리스너를 등록하는 방식으로 이루어진다. `socket.on(<event name>, <listener>)`

## :four: Socket.IO의 메인 기능
1. 신뢰성을 가지고 있다.
다음과 같은 경우에도 서버, 클라이언트 연결이 이루어지도록 하는 신뢰성을 가지고 있다.
    - 프록시 및 로드 밸런서가 있는 경우 
    - 개인 방화벽 및 바이러스 백신 소프트웨어가 있는 경우

통신에 신뢰성을 보장하기 위해서 [`Engine.IO`](https://github.com/socketio/engine.io) 를 사용한다.  
이 부분에 대해서는 [`Goals`](https://github.com/socketio/engine.io#goals) 을 읽어보면 된다.
통신을 할 때 `Engine.IO`는 가장 먼저 롱폴링 연결을 한다 그 다음 웹소켓과 같 `테스트`된 더 나은 전송(transport)로 업그레이드 한다. 
 
2. 자동 재연결을 지원한다.
기본적으로 연결이 끊긴 서버가 다시 사용할 때까지, 계속 연결을 시도한다.  
재연결과 가능한 옵션에 대해서는 [여기](https://socket.io/docs/v3/client-api/#new-Manager-url-options) 를 읽어보면 된다.

3. Disconnection 감지
heartbeat 메커니즘은 `Engion.IO`레벨에서 구현되어있다. 이 덕분에 클라이언트와 서버는 언제부터 상대방이 더이상 응답하지 않는지를 알 수 있다.  
이 기능은 연결 핸드쉐이크 동안에 서버와 클라이언트에게  공유되는 시간 초과 밸류들 (pingInterval 및 pingTimeout 매개 변수)를 가지고, 서버와 클라이언트 각각에 설정된 타이머를 통해서 구현된다.   
이 타이머들은 연결이 설정된 이후에 발생하는 클라이언트 호출이 동일한 서버로 전송되도록 요구하기 때문에, 여러 노드를 사용할 때 고정 세션을 요구한다.  

4. 바이너리를 지원한다.
다음을 포함한 직렬화 가능한 데이터 구조를 전송할 수 있다.
- 브라우저에서 ArrayBuffer와 Blob
- Node.jsdptj ArrayBuffer와 Buffer

5. 멀티 플렉싱을 지원한다.
- 우리앱에서 (모듈 단위로 혹은 사용권한 기준으로) 관심사 분리를 구현하기 위해서, Socket.IO는 여러 네임스페이스를 만들 수 있도록 해준다.
각각의 네임스페이스는 분리된 커뮤니케이션 채널로 동작하지만 그 기반에서는 동일한 연결을 공유한다.

## 참고! heartbeat 메커니즘이란? 
대기중인 메시지가 없는 경우 주기적으로 메시지를 전송하는 것을 말한다.
여기서 전송되는 메시지를 heartbeat라고 한다.
hearbeat을 보냄으로써 송신 프로그램이 정상적으로 실행중인 상태를 수신자에게 알린다.
요약: heartbeat를 사용하는 목적은 송신 프로그램과 수신 프로그램간에 네트워크 요청 및 응답이 없을 때,  
 송신 프로그램이 정산적으로 실행중이라는 상태를 수신 프로그램에게 알리기 위해서 사용한다.  
 
 
  

     

   