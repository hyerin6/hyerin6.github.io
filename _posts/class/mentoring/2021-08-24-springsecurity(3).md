---
layout: post
title: "Spring Security + OAuth2.0 + Kakao"
description: "Spring OAuth2.0 Kakao Login"
date: 2021-08-24
tags: [spring, security, oauth2.0]
categories: [Security, Spring]
comments: true
share: true
---



<br />


Spring Security에 대한 포스팅은 다음 링크를 참고

* Spring Security란?: <https://hyerin6.github.io/2021-08-22/springsecurity(1)/>   

* Spring Security 로그인 절차: <https://hyerin6.github.io/2021-08-22/springsecurity(2)/>



<br />



OAuth2.0 로그인을 사용한다면 UsernamePasswordAuthenticationFilter 대신 OAuth2LoginAuthenticationFilter가 호출된다.

두 필터의 상위 클래스는 AbstractAuthenticationProcessingFilter이고,
스프링 시큐리티가 상위 클래스를 호출하면 로그인 방식에 따라 구현체인
UsernamePasswordAuthenticationFilter와 OAuth2LoginAuthenticationFilter가 동작한다.



<br />

<br />



## Spring Security Configuration 

* `@EnableWebSecurity`는 스프링 시큐리티 설정들을 활성화 시켜준다. 


* `oauth2Client()`로 OAuth2 클라이언트 구성 요소를 지정할 수 있다.
    - `authorizedClientService(authorizedClientService())`



* `http.oauth2Login()`으로 OAuth2 로그인 관련 처리를 설정할 수 있다.

  

* `userService()`는 OAuth2 인증 과정에서 Authentication을 생성에 필요한

    OAuth2User를 반환하는 클래스를 지정한다.

* `customUserType(KakaoOAuth2User.class, SOCIAL_TYPE)`

    OAuth2User 타입을 지정한다.


* `successHandler()`는 인증을 성공적으로 마친 경우 처리할 클래스를 지정한다.

  

* `failureHandler()`는 인증을 실패한 경우 처리할 클래스를 지정한다. 



<br />

<br />





## OAuth2 인증 과정 

1. 사용자가 소셜 로그인 성공 

2. AbstractAuthenticationProcessingFilter에서 OAuth2 로그인 과정을 호출 

3. Resource Server에서 넘겨주는 정보대로

    OAuth2LoginAuthentiationFilter의 `attemptAuthentication()`에서 인증 과정을 수행

4. attemptAuthentication() 처리 과정에서 OAuth2AuthenticationToken을 생성하기 위해 

   OAuth2LoginAuthenticationProvider의 `authenticate()` 호출 

5. `authenticate()` 처리 과정에서 OAuth2User를 생성하기 위해

    OAuth2UserService의 `loadUser()` 호출

6. `loadUser()` 처리 과정에서 OAuth2User를 반환 

7. (6)번까지 끝내면 AbstractAuthenticationProcessingFilter에서 SuccessHandler의 `onAuthenticationSuccess()`을 호출한다.

   기본적으로 리다이렉션하는 역할인데 커스텀해서 지정할 수 있다. 

8. (6)번까지의 과정이 정상적으로 끝나지 않았다면 AbstractAuthenticationProcessingFilter에서 FailureHandler의 `onAuthenticationFailure()`을 호출한다.

<br />


여기까지 올바른 요청일 경우의 처리 과정이고 이외의 경우는 다음과 같다. 

1. 인증되지 않은 사용자가 인증이 필요한 URL에 접근하려 한다면 authenticationEntityPoint에서 예외 처리 
2. 인증된 사용자가 권한이 부족한 URL에 접근하려 한다면 accessDeniedHandler에서 예외 처리 


<br />

<br />



## 인증 코드 요청

Spring Security와 OAuth2를 사용해서 자신이 등록한 kakao api의 인증 코드 api를 호출하려면 해당 역할을 하는 endpoint를 알아야 한다.

여기서 endpointsms Filter이고 해당 역할을 하는 Filter는
OAuth2AuthorizationRequestRedirectFilter이다.

이 필터에서 this.authorizationRequestResolver가 registrationID인 kakao 값으로 설정 정보를  조회한다.

인증 코드를 얻기 위해 호출할 API 주를 만들고 해당 주소로 리다이렉션한다.

<br />

Spring Security OAuth2 설정을 끝내고 브라우저 주소창에

`http://localhost::8080/oauth2/authorization/kakao`을 입력하면 카카오톡 로그인 페이지가 뜬다.

사용자가 로그인에 성공하면 카카오에 등록한 Callback URL인 `redirect_uri`가 호출된다.



<br />

<br />





## Access Token 요청

code를 요청할 때 `redirect_uri`을 보내는데 `redirect_uri`를 어떻게 처리해서 access token을 얻어올 수 있을까?

직접 Controller에서 token을 얻는 API를 만드는 예제를 본 적이 있는데  프레임워크를 사용하고 있기 때문에 나는 프레임워크를 이용할 것이다.

<br />

Access token을 획득하는 역할의 Filter는 OAuth2LoginAuthenticationFilter이다.

OAuth2LoginAuthenticationToken 클래스 변수에 access token과 함께 kakao user 정보도 가지고 있다.



<br />

<br />



## 로그인 상태 처리

Spring Security는 기본적으로 세션 기반으로 동작한다.

그러나 stateless로 만들고 싶은 경우 OAuth2 인증 처리 후 실행되는 successHandler를 커스텀하면 된다.

이 방법은 현재 프로젝트에서 kakao라는 provider에 의존하는 문제가 있다.

이 의존 문제는 OAuth2UserService의 `loadUser()` 메소드에서 `Map<String, Object>` 타입으로

OAuth2 회원 정보 조회 API의 Response를 파싱하는 코드를 소셜에 맞게 변경해야 한다는 것이다.

<br />


이 문제는 CustomUserTypesOAuth2UserService를 사용하여 해결할 수 있다.


Custom User Type을 Map에 담아 파라미터로 넘겨서 사용하고
Map의 Key는 Client의 Registration ID로 설정하는 것이다.

설정은 다음과 같이 하면 된다.


```
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .anyRequest().authenticated()
            .and()
            .oauth2Client()
            .authorizedClientService(authorizedClientService())
            .and()
            .oauth2Login()
            .userInfoEndpoint()
            .customUserType(KakaoOAuth2User.class, SOCIAL_TYPE)
            .userService(oAuth2UserService())
            .and()
            .successHandler(authenticationSuccessHandler());
    }
}
```




<br />

<br />



## 저장된 인증 정보 사용

Spring Security는 인증을 하면 인증 정보를 SecurityContextHolder 클래스를 통해 메모리에 저장한다.

세션 키로 메모리에 저장된 인증 정보를 꺼내서 무언가 처리하고 싶다면 다음과 같이 사용하면 된다.

<br />

* controller에서 매개변수로 받는 방법
  `@AuthenticationPrincipal OAuth2User oauth2User`



* 코드에서 얻는 방법
  `Authentication auth = SecurityContextHolder.getContext().getAuthentication();`


<br />

<br />


## 구현

<img width="661" alt="스크린샷 2021-08-24 오후 11 21 13" src="https://user-images.githubusercontent.com/33855307/130633816-e2e3e1a7-0f07-4c92-9657-895a63622688.png">

<br />



위 내용을 토대로 Spring Security와 OAuth2.0을 사용하여 위와 같이 Kakao Login 기능을 구현해봤다.

Spring Security 세션에 별다른 설정 없이 진행한다면 사용자 정보를 출력해보는 것까지는 문제없이 구현할 수 있었다.

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
AccessToken은 ResourceServer를 구현해 Introspaction으로 토큰에 대한 인증 과정을 수행해야 한다.

참고: <https://www.oauth.com/oauth2-servers/token-introspection-endpoint/>

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

