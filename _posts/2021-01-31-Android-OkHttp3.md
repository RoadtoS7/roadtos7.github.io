---
layout: post
title:  "OkHttp3"
date:   2021-01-31 23:12:17 -400
lastmod : 2021-01-31 19:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

우선 OkHttp3를 알아보기 전에 OkHttp가 무엇인지부터 알아보자.
공식홈페이지에 나와있는 설명에 따르면, 
> OkHttp란 효율적인 HTTH 클라이언트이다.

왜 효율적인 클라이언트일까?
1. HTTP/2는 동일한 호스트에 보내는 요청이 동일한 소켓을 사용할 수 있도록 한다.
2. 연결 풀링을 사용하면 요청 대기시간이 줄어든다. 
3. GZIP이 다운로드 크기을 줄인다.
4. 응답 캐싱은 반복 요청에 대해서 네트워크를 보호한다.

OkHttp는 네트워크 문제가 발생할 경우, 그 원인이 일반적인 연결 문제라면 자동으로 연결이 복구된다.  
만약 우리가 요청을 보낸 서비스가 다양한 IP 주소를 가지고 있다면, OkHttp는 첫번째 IP 주소로 연결이 실패할 경우, 두번째 IP 주소와 자동으로 연결을 시도한다.  
IPv4+IPv5 및 중복된 데이터 센터에서 호스팅되는 서비스대상으로는 이 기능이 필요하다.  
OkHttp는 모던 TLS(TLS 1.3, ALPN, certificate pinning) 기능들을 지원한다. 
이 기능은 광범위한 연결을 위해서 사용되지 않도록 설정될 수 있다.  

OkHttp를 사용하는 것은 쉽다.  
OkHttp의 요청, 응답 API는 유연한 여러가지 빌더와 불변성을 가지고 설계되었다.  
OkHttp는 동기식 블락킹 호출과 콜백을 가지는 비동기식 호출을 모두 지원한다.  

~~조사를 하면서 알게 된건데 OkHttp 4.x 가 나온 것 같다...~~

   
 
