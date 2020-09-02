---
layout: post
title:  "Node.js - 모듈(module)"
date:   2020-08-04 21:36:17 -400
lastmod : 2020-08-04 20:36:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Nodejs
---

## 모듈(Module) 이란?
모듈이란 프로그램에서 부품과 같습니다. 우리가 프로그래밍을 할 때, 모든 부분을 다 구현할 필요 없이 이미 내장되어있는 부품들이 존재하며, 우리는 이 부품들을 잘 조합하여 프로그램을 만들 수 있습니다. 이 부품들이 바로 모듈(Module) 입니다.  

Node.js에도 여러가지 모듈이 존재하며, 이 중 웹서버를 만드는 모듈이 존재합니다. 이번 포스팅에서는 Node.js의 웹서버 모듈을 이용하여 간단한 웹서버 애플리케이션을 만들어보고자 합니다.


## 코드
Node.js 공식홈페이지에 존재하는 [예시 코드](https://nodejs.org/ko/about/)를 중심으로 살펴보겠습니다.  
```javascript
const http = require('http');

const hostname = '127.0.0.1';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

웹 서버를 만들기 위해서는 `http`모듈을 사용합니다.   
```javascript
const http = require('http');
```
따라서 위 코드에서는 `http`모듈을 가져와서, `http`인스턴스를 `http`변수(const 오른쪽에 존재하는 변수)에 담은 것 입니다.  

<br/>
<br/>

```javascript
const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
});
```

`http.createServer()`를 호출하면 서버가 만들어지고, 이 서버를 우리가 제어할 수 있도록 서버 객체가 리턴됩니다.  
현재 이 서버 객체는 `server`변수에 담았습니다.  

<br/>
<br/>

```javascript
server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

`server`객체로 하여금 `listen()`의 인자인 `hostname`을 ip주소로 가지고, `port` 인자가 가지는 값에 해당하는 포트번호를 리스닝하도록 합니다.

포트번호는 서버 컴퓨터 내의 많은 애플리케이션 중 어떤 것을 사용할 것인지에 대한 식별자로 쓰입니다.  
웹서버를 만든 후, 해당 서버가 어떤 포트를 리슨하도록 할 것인지 지정합니다. 그러면 해당 포트번호로 들어온 요청에 대해서만 서버가 응답하게 됩니다.  
예제 코드에서는 `listen()` 메서드를 사용하여 애플리케이션이 사용할 ip와 포트번호를 지정하고 있습니다.  

그런데 이 `listen()`작업은 시간이 오래 걸릴 수 있기 때문에 비동기로 작동합니다. 따라서 세번째 인자로 콜백함수를 받으며, listen 작업이 성공하면 이 콜백함수가 실행됩니다.   
우리 코드에서는 listen이 성공하면 로그가 찍힙니다.  

<br/>
<br/>
`listen()` 메서드를 통해서 애플리케이션이 특정 포트를 리스닝하고 있다가 그 포트로 요청이 들어오면, 작업을 처리합니다.  
어떤 작업을 할 것인지는 함수로 정의하여, `createServer()`의 인자로 넣어줍니다.  

```javascript
const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
});
```

예제 코드에서 요청에 대한 작업을 처리하는 함수는 다음과 같습니다.
```javascript
(req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
}
```

첫번째 파라미터인 req를 통해서 요청과 관련된 정보를 얻습니다. req는 요청으로 전달되는 데이터를 담고있는 객체입니다.    
두번째 파라미터인 res를 통해서 응답과 관련된 정보를 담습니다.   

`res.statusCode`에 https response의 응답 상태 코드를 지정합니다.  
`res.setHeader()`메서드를 이용하여 http response의 header를 설정합니다.
`res.end()` 메서드를 클라이언트에게 전송할 데이터를 인자로 전달하면서 호출하면 응답이 전송됩니다.  

<br/>
모듈은 이와 같이 프로그램에서 도구처럼 사용됩니다.  
또한 외부로부터 가져오는 모듈만 존재하는 것이 아닌 우리가 직접 모듈을 생성할 수도 있습니다.
Node.js는 모든 파일을 각각 모듈화합니다. 1개의 파일이 곧 1개의 모듈이 됩니다.  
이를 통해 클라이언트 사이드 자바스크립트에서 발생하는 전역변수 중복 문제를 해결하고 있으며, 우리는 이 방식을 이용하여 코드를 모듈로 만들어 프로젝트를 더 가독성 있고, 이해하기 쉽게 유지할 수 있습니다.  

<br/>
<br/>
