---
title: "Spring 04 - 보안"
date: "2020-05-29 14:05:17 -400"
categories: Spring
---

## 4. 스프링 시큐리티
- 스프링 기반의 어플리케이션의 보안(인증, 인가, 권한 등)을 담당하는 프레임워크

- 스프링 시큐리티를 사용하지 않으면, 자체적으로 보안을 위한 로직을 구현해야한다.  
하지만 스프링 시큐리티를 사용하면 보안과 관련한 옵션을 제공하기 때문에, 우리는 이 옵션만 사용하면 쉽게 보안을 구현할 수 있다.
- 스프링 시큐리티는 filter 기반으로 동작하기 때문에 spring MVC 와 분리되어 동작한다.  

### 4.1 보안관련 용어
- 접근 주체(Principal): 보호된 리소스에 접근하려는 유저

- 인증(Authenticate): 보호된 리소스에 접근하려는 유저에 대해서 이 유저가 누구인지 확인하는 작업, 해당 작업을 수행해도 되는 유저인지를 확인하는 작업(ex. Form 기반 로그인)
- 인가(Authorize): 해당 리소스에 대해 접근가능한 권한을 가지고 있는지 검사하는 것, 인증 이후에 이루어집니다.  
- 권한: 인증된 주체가 애플리케이션의 동작을 수행할 수 있도록 허락되었는지를 결정
  - 권한 승인이 필요한 부분으로 접근하려면, 먼저 인증 과정을 통해서 주체가 인증되어야 한다.
  - 즉, 인가 과정에서 해당 리소스에 대한 권한을 가지고 있는지를 확인한다.

### 4.5 Spring 시큐리티 구조(아키텍처)
![image](https://user-images.githubusercontent.com/57262833/83223687-87a3cc00-a1b6-11ea-8dbe-6a2e29fda79c.png)
- 위 그림 = Form 기반 로그인에 대한 흐름을 보여줍니다. 
1. 사용자가 Form 을 이용하여 로그인하기 위한 정보를 입력하고 인증 요청을 보냅니다.(Http Request)

2. AuthenticationFilter가 HttpServletRequest에서 사용자가 보낸 아이디와 패스워드를 꺼냅니다.   
- 보안을 위해서 아이디와 패스워드의 유효성 검사를 해줍니다.(아이디 혹은 패스워드가 null 인지 등).
- 인증을 담당하는 AuthentiacationManager 인터페이스 구현체에게, HttpServeletRequest 에서 꺼낸 사용자 아이디와 패스워드를 인증용 객체(UsernamePasswordAuthenticationToken) 로만들어서 전달합니다.

3. AuthenticationFilter에게 Authentiaction 객체(인증용 객체, UsernamePasswordAuthenticationToken)을 전달받습니다.  

4. 실제 인증을 할 AuthenticationProvider에게 Autication 객체를 다시 전달합니다.

5. DB에서 사용자 인증정보를 가져올 UserDetailsSercice 객체에게 사용자 아이디를 넘겨주고, DB에서 인증에 사용할 정보(아이디, 암호화된 패스워드, 권한 등)를 UserDetails라는 객체로 전달받습니다.

6. AuthenticationProvider는 UserDetails객체를 전달 받은 후, 실제 사용자의 입력정보와 UserDetails 객체를 가지고 인증합니다.
7, 8, 9, 10. 인증이 ㅅ완료되면 사용자 정보를 가진 Authentication 객체를 SecurityContextHolder 에 담은 후 AuthenticationSuccessHandle를 실행합니다.(실패시 AuthenticationFailHandle을 실행합니다.)

### 4.6 Security filter
![image](https://user-images.githubusercontent.com/57262833/83224304-064d3900-a1b8-11ea-93d9-340cac8c505b.png)

1. SecurityContextPersistenceFilter : SEcurityContextRespository에서 SecurityContext를 가져오가너 저장하는 역할을 합니다.

2. LogoutFilter : 설정된 로그아웃 URL로 오는 요청을 감시하며, 해당 유저를 로그아웃 처리
(UsernamePassword)

3. AuthenticationFilter : (아이디와 비밀번호를 사용하는 form 기반 인증) 설정된 로그인 URL로 오는 요청을 감시하며, 유저 인증 처리
- AuthenticationManager를 통한 인증 실행
- 인증 성공 시, 얻은 Authentication 객체를 SecurityContext에 저장 후 AuthenticationSuccessHandler 실행
- 인증 실패 시, AuthenticationFailureHandler 실행

4. DefaultLoginPageGeneratingFilter : 인증을 위한 로그인폼 URL을 감시합니다.
5. BasicAuthenticationFilter : HTTP 기본 인증 헤더를 감시하여 처리합니다.
6. RequestCacheAwareFilter : 로그인 성공 후, 원래 요청 정보를 재구성하기 위해 사용됩니다.
7. SecurityContextHolderAwareRequestFilter : HttpServletRequestWrapper를 상속한  SecurityContextHolderAwareRequestWapper 클래스로 HttpServletRequest 정보를 감쌉니다. - SecurityContextHolderAwareRequestWrapper 클래스는 필터 체인상의 다음 필터들에게 부가정보를 제공합니다.
8. AnonymousAuthenticationFilter : 이 필터가 호출되는 시점까지 사용자 정보가 인증되지 않았다면 인증토큰에 사용자가 익명 사용자로 나타납니다.
9. SessionManagementFilter : 이 필터는 인증된 사용자와 관련된 모든 세션을 추적합니다.
10. ExceptionTranslationFilter : 이 필터는 보호된 요청을 처리하는 중에 발생할 수 있는 예외를 위임하거나 전달하는 역할을 합니다.
11. FilterSecurityInterceptor : 이 필터는 AccessDecisionManager 로 권한부여 처리를 위임함으로써 접근 제어 결정을 쉽게해줍니다.


> https://sjh836.tistory.com/165 [빨간색코딩]



