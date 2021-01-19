---
layout: post
title:  "Socket.io - Socket 인스턴스에 대한 기본 사항"
date:   2020-01-18 02:41:17 -400
lastmod : 2020-01-18 02:41:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Network
background: "/img/classic_blue.jpeg"
---

## id
- 연결된 소켓은 아이디를 갖는다.
- 이 아이디는 서로 연결된 소켓끼리 갖은 값을 갖는다.
- `socket.id`

## connected
- 해당 소켓이 연결되었는지 여부 boolean 값으로 반환한다.
- `socket.connected`


## Socket.io Lifecycle
1. Manager 인스턴스가 서버와 연결(재연결)한다. =  Manager의 open() 함수가 호출된다.
2. 소켓이 핸드 셰이크 패킷을 보낸다.
3. 연결이 설정되면 connect 이벤트가 발생한다.  -> 서버와 연결이 설정된다.(연결된다.) -> 4번  
그렇지 않으면 connect_error 이벤트가 발생한다. -> 서버와 disconnect 된다. (수동으로 재연결을 시도해야 한다.)
4. 서버와 연결이 된 후에 disconnect되면, socket의 disconnect()가 호출되고, manager가 close()된다.  
-> 1번으로 이동해서 자동으로 재연결을 시도한다.

## Event
### 1. connect
- 서버 소켓과 클라이언트 소켓간 연결이 수립되었을 때 발생하는 이벤트
- connect 이벤트 핸들러러 내에 다른 이벤트 핸들러를 등록해 놓으면 안된다.   
왜냐하면 새로운 소켓 연결이 수립될 때마다 새로운 핸들러가 등록될 것 이기 때문이다.

### 2. connect_error
- 서버가 연결 요청을 받아드리지 않는 경우 발생한다.
- 이때는 수동으로 재연결 요청을 해야 한다.

```javascript
// either by directly modifying the `auth` attribute
socket.on("connect_error", () => {
  socket.auth.token = "abcd";
  socket.connect();
});

// or if the `auth` attribute is a function
const socket = io({
  auth: (cb) => {
    cb(localStorage.getItem("token"));
  }
});

socket.on("connect_error", () => {
  setTimeout(() => {
    socket.connect();
  }, 1000);
});
```

### 3. disconnect
- 연결이 끊겼을 때 발생하는 이벤트이다.

### disconnect 이벤트가 발생하는 이유 목록
1. `io server disconnect` : 서버에서 강제적으로 서버 소켓의 연결을 끊은 경우 = `socket.disconnect()`을 호출한 경우
2. `io client disconnect` : 클라이언트에서 수동으로 서버 소켓의 연결을 끊은 경우 = `socket.disconnect()`을 호출한 경우
3. `ping timeout` :  서버가 `ping timeout` 이내에 응답하지 않은 경우
4. `transport close` : 연결이 닫힌 경우 (ex. 사용자가 연결을 유실하거나, wifi에서 4g로의 네트워크 변경이 일어난 경우 )
5. `transport error` : 연결 중 에러가 발생한 경우 (ex. HTTP long polling 사이클에서 서버가 죽은 경우 )

- 주의사항!  
`disconnecting`, `newListener`, `removeListener` 와 같은 이벤트는 특별한 이벤트 이기 때문에 우리가 애플리케이션에서 사용하면 안된다. 