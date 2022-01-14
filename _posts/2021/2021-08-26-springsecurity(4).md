---
layout: post
title: "Spring Security OAuth2.0에서 JWT를 사용하는 이유"
description: "Spring OAuth2.0 Kakao Login"
date: 2021-08-26
tags: [spring, security, oauth2.0, JWT]
categories: [Security, Spring]
comments: true
share: true
---


<br />



Spring Security에 대한 포스팅은 다음 링크를 참고

* Spring Security란?: <https://hyerin6.github.io/2021-08-22/springsecurity(1)/>

* Spring Security 로그인 절차: <https://hyerin6.github.io/2021-08-22/springsecurity(2)/>

* Spring Security OAuth2 (+ Kakao Login): <https://hyerin6.github.io/2021-08-24/springsecurity(3)/>



<br />
<br />




## 구현

<img width="661" alt="스크린샷 2021-08-24 오후 11 21 13" src="https://user-images.githubusercontent.com/33855307/130633816-e2e3e1a7-0f07-4c92-9657-895a63622688.png">

<br />



Spring Security를 학습해보면서 Spring Security와 OAuth2.0을 사용하여 위와 같이 Kakao Login 기능을 구현해봤다.

Spring Security 세션에 별다른 설정 없이 진행한다면 사용자 정보를 출력해보는 것까지는 문제없이 구현할 수 있었다.

하지만 그 다음 인증 과정에서 문제가 많았다.

<br />

이렇게 직접 구현해보기 전에 RefreshToken을 DB에 저장해두고 AccessToken이 만료되면 RefreshToken으로 AccessToken을 재발급 받는

로그인 / 로그인 유지 과정을 Spring Security 프레임워크로 구현하려고 했으나 다음과 같은 문제점들이 생겼다.


<br />

ID/Password가 아닌 OAuth2로 로그인하는 과정은

AbstractAuthenticationProcessingFilter에서 OAuth2 로그인 과정을 호출한다.

상황에 맞게 AuthenticationProcessingFilter가 작동하는데 OAuth2 로그인으로 설정했기 때문에

OAuth2LoginAuthenticationFilter의 `attemptAuthentication()`에서 다음과 같은 인증 과정을 수행한다.


<br />


```
(1) DefaultOAuth2UserService (구현: kakaoOAuth2UserService)
loadUser()에서 request로 받은 값들을 OAuth2User로 반환한다.

(2) OAuth2AuthorizedClientService (구현: KakaoOAuth2AuthorizedClientService)
위 loadUser()에서는 AccessToken과 User 정보만 받을 수 있고
RefreshToken은 OAuth2AuthorizedClientService 클래스에서 받을 수 있다.

(3) AuthenticationSuccessHandler (구현: KakaoAuthenticationSuccessHandler)
로그인 성공 후 처리를 진행한다.
```


<br />


## 문제점(1)
AccessToken은 `ResourceServer`를 구현해 `Introspection`으로 **토큰에 대한 인증 과정을 수행해야 한다.**

참고: <https://www.oauth.com/oauth2-servers/token-introspection-endpoint/>


<br />


![스크린샷 2022-01-14 오후 3 04 57](https://user-images.githubusercontent.com/33855307/149459750-3bfae638-d5b2-4eff-b221-795865293926.png)



access_token은 Bearer 토큰 형식으로 단순히 암호화된 문자열이다.

Bearer 토큰으로 리소스 서버에 리소스를 요청하면 해당 토큰이 유효한지, 토큰 인증한 회원이 누구인지 인증서버에 추가 확인하는 과정이 필요하다.

이러한 토큰을 `opaque token` 이라고 한다.




<br />



access_token 인증 과정의 번거로움을 보완하기 위해 JWT 토큰 사용을 고려해볼 수 있다.

JWT는 JSON String이 암호화된 문자열로 토큰 자체에 특정한 정보를 세팅할 수 있다는 것이 특징이다.

회원의 id나 **인증에 필요한 정보를 포함시킬 수 있기 때문에** Bearer 토큰처럼 회원 확인을 위해

**인증 서버를 한번 더 거칠 필요가 없다.**

JWT를 사용하면 다음과 같은 인증 과정을 갖는다.


![스크린샷 2022-01-14 오후 3 01 22](https://user-images.githubusercontent.com/33855307/149459420-9b86d907-0b2d-4221-85f4-9e7940b3c152.png)



<br />

## 문제점(2)
Spring Security 프레임워크를 사용해서 브라우저를 통해 발급받은 Kakao AccessToken을 브라우저를 통해서가 아닌

RestAPI로(ex.postman을 사용해서 헤더에 access token을 담아 요청하는 경우) 사용하는 경우 인증이 안된다.

<br />

## 문제점(3)
Spring Security에서 RefreshToken을 저장하는 방법은 inMemory, JDBC 두 가지이다.

JDBC에 저장하는 토큰 관리 방법은 다음과 같은 테이블을 만들고 Spring Security에서 정의한대로 구현해야 한다.


<br />

```
create table IF NOT EXISTS oauth_client_details (
  client_id VARCHAR(256) PRIMARY KEY,
  resource_ids VARCHAR(256),
  client_secret VARCHAR(256),
  scope VARCHAR(256),
  authorized_grant_types VARCHAR(256),
  web_server_redirect_uri VARCHAR(256),
  authorities VARCHAR(256),
  access_token_validity INTEGER,
  refresh_token_validity INTEGER,
  additional_information VARCHAR(4096),
  autoapprove VARCHAR(256)
);
```

<br />


처음 계획한대로 Spring Security를 사용해서 RefreshToken을 DB에 저장하고

로그인이 필요한 API마다 AuthorizationServer(Kakao)에 토큰의 유효성을 검사하는 방식까지

구현하려고 했으나 너무 많은 클래스를 구현해야 하고 Spring Security가 어떻게 동작하는지 알아보기 위해

시작한 구현이기 때문에 이정도에서 만족하고 OAuth2와 JWT를 사용한 로그인 방식으로 변경했다.

<br />


이번주부터는 Spring Security를 사용하지 않고 JWT를 사용한 Kakao 로그인을 구현할 예정이다.

<br />
<br />



## 참고
* <https://daddyprogrammer.org/post/1287/spring-oauth2-authorizationserver-database/>

* <https://daddyprogrammer.org/post/1754/spring-boot-oauth2-resourceserver/>

* <https://www.oauth.com/oauth2-servers/token-introspection-endpoint/>

<br />

