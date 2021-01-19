---
layout: post
title:  "Socket.io - 이벤트 수신"
date:   2021-01-18 02:41:17 -400
lastmod : 2021-01-18 02:41:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Network
background: "/img/classic_blue.jpeg"
---

## 이벤트 수신
- 이벤트를 수신하는 방법에는 여러가지가 있다.  

### 1. EventEmitter 메서드 사용하기
- Server 사이드에서는 socket 인스턴스가 EventEmitter 클래스를 상속하고 있다.
- Client 사이드에서는 socket 인스턴스는 component-emitter 라이브러리에서 제공하는 이벤트 전송자를 사용하며, 이 이벤트 전송자는 EventEmitter 메서드의 일부를 노출시킨다.
  
#### 1. socket.on(이벤트 이름, 리스너)
- 이벤트에 대한 리스너 함수를 리스너 배열 끝에 추가한다.

```javascript
socket.on("details", (...args) => {
  // ...
});
```

#### 2. socket.once(이벤트 이름, 리스너) 
- 지정된 이벤트에 대한 일회성 리스너를 추가한다.

```javascript
socket.once("details", (...args) => {
  // ...
});
```

#### 3. socket.off(이벤트 이름, 리스너)

- 해당 이벤트에 대한 리스너 배열에서, 특정 리스너를 제거한다.

```javascript
const listener = (...args) => {
  console.log(args);
}

socket.on("details", listener);

// and then later...
socket.off("details", listener);
```

#### 4. socket.removeAllListeners([이벤트 이름])
- 이벤트 이름이 인자로 주어지지 않은 경우에는 모든 리스너를 제거한다.
- 이벤트 이름이 인자로 주어진 경우에는 해당 이벤트에 등록된 모든 리스너를 제거한다.

```javascript
// for a specific event
socket.removeAllListeners("details");
// for all events
socket.removeAllListeners();
```

### 2. catch-all listener
#### 1.  socket.onAny(listener)
- 모든 이벤트에 대해서 실행되는 리스너를 등록한다.

```javascript
socket.onAny((eventName, ...args) => {
  // ...
});
```

#### 2. socket.prependAny(listener)
- 모든 이벤트에 대해서 실행되는 리스너를 추가한다. 
- 이 리스너는 리스너 배열의 맨 앞에 추가된다.

```javascript
socket.prependAny((eventName, ...args) => {
  // ...
});
```

#### 3. socket.offAny([listener])
- 인자로 리스너가 주어지지 않은 경우, 모든 catch-all 리스너를 제거한다.
- 인자로 리스너가 주어진 경우, 해당 catch-all 리스너만 제거한다.

```javascript
const listener = (eventName, ...args) => {
  console.log(eventName, args);
}

socket.onAny(listener);

// and then later...
socket.offAny(listener);

// or all listeners
socket.offAny();
```

### 3. validation
- 이벤트 인수의 유효성 검사는 Socket.IO 라이브러리의 영역이 아니다.
- 자바스크립트 환경에는 이를 다루는 많은 패키지들이 존재한다. 
    - joi
    - ajv
    - validatorjs

### 4. Error Handling
- Socket.IO 에는 에러 핸들링 API가 존재하지 않는다.
- 따라서 리스너에서 발생할 수 있는 모든 예외를 catch 하여 처리해야 한다.

```javascript
io.on("connection", (socket) => {
  socket.on("list items", async (callback) => {
    try {
      const items = await findItems();
      callback({
        status: "OK",
        items
      });
    } catch (e) {
      callback({
        status: "NOK"
      });
    }
  });
});
```
 

