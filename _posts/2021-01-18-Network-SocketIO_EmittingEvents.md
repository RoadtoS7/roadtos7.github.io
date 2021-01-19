---
layout: post
title:  "Socket.io - 이벤트 전송"
date:   2021-01-18 02:41:17 -400
lastmod : 2021-01-18 02:41:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Network
background: "/img/classic_blue.jpeg"
---

## 이벤트 전송
- Socket.IO에서 클라이언트와 서버간에 이벤트를 주고받을 수 있는 여러 방법이 존재한다.

### 1. 기본적인 전송 방법
- 한쪽에서 이벤트를 발행하고, 다른쪽에서는 해당 이벤트 리스너를 등록하는 방법

- 예시1. 서버에서 이벤트를 전송하고, 클라이언트에서 해당 이벤트 리스너를 등록

```javascript
// server-side
io.on("connection", (socket) => {
  socket.emit("hello", "world");
});

// client-side
socket.on("hello", (arg) => {
  console.log(arg); // world
});
```

- 예시2. 예시1과 반대(서버에서 이벤트 리스너를 등록하고, 클라이언트에서 이벤트를 전송)
```javascript
// server-side
io.on("connection", (socket) => {
  socket.on("hello", (arg) => {
    console.log(arg); // world
  });
});

// client-side
socket.emit("hello", "world");
``` 

- **예시3. 이벤트와 함께 데이터를 전송하는 방법**
```javascript
// server-side
io.on("connection", (socket) => {
  socket.emit("hello", 1, "2", { 3: '4', 5: Buffer.from([6]) });
});

// client-side
socket.on("hello", (arg1, arg2, arg3) => {
  console.log(arg1); // 1
  console.log(arg2); // "2"
  console.log(arg3); // { 3: '4', 5: ArrayBuffer (1) [ 6 ] }
});
``` 

- 주의 사항: Map과 Set은 직렬화가 가능하지 않기 때문에, 수동으로 직렬화 시킨 후 전송해야 한다.

```javascript
const serializedMap = [...myMap.entries()];
const serializedSet = [...mySet.keys()];
```

### 2. Acknowledgements
- 기존의 요청, 응답 API 방식으로 이벤트를 주고 받는 것 
- emit의 마지막 인자로 콜백함수를 넣을 수 있다.  
이 콜백함수는 상대방이 해당 emit으로 전송하는 이벤트를 수신하면, 콜백함수가 호출된다.

```javascript
// server-side
io.on("connection", (socket) => {
  socket.on("update item", (arg1, arg2, callback) => {
    console.log(arg1); // 1
    console.log(arg2); // { name: "updated" }
    callback({
      status: "ok"
    });
  });
});

// client-side
socket.emit("update item", "1", { name: "updated" }, (response) => {
  console.log(response.status); // ok
});
```

- 타임 아웃 이벤트에 대한 콜백은 기본적으로는 지원되지 않지만, 아래와 같이 구현할 수 있다.

```javascript
const withTimeout = (onSuccess, onTimeout, timeout) => {
  let called = false;

  const timer = setTimeout(() => {
    if (called) return;
    called = true;
    onTimeout();
  }, timeout);

  return (...args) => {
    if (called) return;
    called = true;
    clearTimeout(timer);
    onSuccess.apply(this, args);
  }
}

socket.emit("hello", 1, 2, withTimeout(() => {
  console.log("success!");
}, () => {
  console.log("timeout!");
}, 1000));
```

### 3. volatile event
- volatile event란 근본적인 연결이 준비되지 않은 경우에는 발송되지 않는 이벤트이다.(신뢰성 측면에서 UDP와 유사하다.)
- 예를 들어, 온라인 게임에서 캐릭터의 위치를 전송해야 하는 경우(최신 값만 유용하기 때문에) 흥미로울 수 있다.
- 또는 클라이언트가 연결되지 않았을 때, 전송된 모든 이벤트가 사라지도록 할 때 유용합니다.  
(기본적으로 클라이언트가 연결되지 않았을 때 모든 이벤트는 버퍼에 저장된다.)  
- 예시

```javascript
// server-side
io.on("connection", (socket) => {
  console.log("connect");

  socket.on("ping", (count) => {
    console.log(count);
  });
});

// client-side
let count = 0;
setInterval(() => {
  socket.volatile.emit("ping", ++count);
}, 1000);
```

- 예시의 콘솔 출력

```javascript
connect
1
2
3
4
# the server is restarted, the client automatically reconnects
connect
9
10
11
```

- volatile이 없는 경우
```javascript
1
2
3
4
# the server is restarted, the client automatically reconnects and sends its buffered events
connect
5
6
7
8
9
10
11
```