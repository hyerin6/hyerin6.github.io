---
layout: post
title: "Spring Security 로그인 절차"
description: "Spring Security"
date: 2021-08-22
tags: [spring, security, architecture]
categories: [Security, Spring]
comments: true
share: true
---



<br />



# 로그인은 어떻게 진행될까?      

![SpringSecurity](https://user-images.githubusercontent.com/33855307/130346760-f38deeb1-6f0d-4291-b129-1d36f620eb9e.jpeg)



<br />



1. 사용자가 로그인을 요청한다. 



2. AuthenticationFilter (사용할 구현체 : UsernamePasswordAuthenticationFilter)가 HttpServletRequest에서 사용자가 보낸 아이디와 패스워드를 인터셉트한다.
Front 단에서 유효성 검사를 할 수 있지만 서버에서도 가능하다.
HttpServletRequest에서 꺼내온 사용자 아이디와 패스워드를
진짜 인증을 담당할 AuthenticationManager 인터페이스 (구현체: ProviderManager)에게
인증용 객체(UsernamePasswordAuthenticationToken)로 만들어줘서 위임한다.



3. AuthenticationFilter에게 인증용 객체(UsernamePasswordAuthenticationToken)을 전달받는다.



4. 실제 인증을 할 AuthenticationProvider에게 Authentication 객체 (UsernamePasswordAuthenticationToken)을 다시 전달한다.



5. DB에서 사용자 인증 정보를 가져올 UserDetailsService 객체에게 사용자 아이디를 넘겨주고
DB에서 인증에 사용할 정보 (id/password, 권한 등)을 UserDetails 객체로 전달받는다.



6. AuthenticationProvider는 UserDetails 객체를 전달받은 이후 실제 사용자의 입력 정보와 UserDetails 객체를 가지고 인증을 시도한다.



7~10. 인증이 완료되면 사용자 정보를 가진 Authentication 객체를 SecurityContextHolder에 담은 이후 AuthenticationSuccessHandler를 실행한다.
실패 시 AuthenticationFilureHandler를 실행한다.





<br />      
<br />






## 로그인  

###  `AuthenticationFilter`, `DelegatingFilterProxy`

우선 클라이언트(브라우저)로 부터 요청(Request)이 오면, 그 요청은 ApplicationFilter 객체들로 먼저 가게 된다. 

DispatcherServlet에 도달하기 전이다. 
<br />


ApplicationFilter들을 거치다가 DelegatingFilterRegistrationBean 이라는 필터를 만나게 된다.

이 필터는 DelegatingFIlterProxy라는 클래스로 만들어진 스프링 빈을 등록시켜주는 역할을 한다.



<br />
<br />


### Filter 

스프링 부트에서는 `@EnableAutoConfiguration` 어노테이션을 이용해서 

SecurityFilterAutoConfiguration 클래스를 로드하고 디폴트로 이름이 SpringSecurityFilterChain 빈을 등록해준다. 

이때 스프링 시큐리티가 만든 DelegatingFilterProxy 클래스인 SpringSecurityFilterChain 이라는 이름의 스프링 빈을 등록하고 

이후에는 이 DelegatingFilterProxy(SpringSecurityFilterChain)가 필터로 동작하게 된다. 



> DelegatingFilterProxy가 처리를 위임하는 필터 클래스는 FilterChainProxy다. 
>
> 이 클래스 내부에 체인으로 등록된 필터를 순서대로 수행하는 것이다.
>
> DelegatingFIlterProxy ▶️ FIlterChainProxy ▶️ List 구조 


<br />

SpringSecurityFilterChain은 스프링에서 보안과 관련된 여러 필터 리스트를 갖고 있는 객체로 필터 리스트를 순회하면서 필터링한다.

필터 리스트는 AuthenticationFilter들이고, 스프링 시큐리티가 자동으로 생성한다. 



SpringSecurityFilterChain이라는 필터가 갖고 있는 필터 중 하나로 UsernamePasswordAuthenticationFilter라는 필터가 있다.

이 필터는 ID/Password를 이용한 인증을 담당하는 필터다.


<br />
<br />




### OAuth 2.0 

그렇다면 OAuth 2.0을 이용한 인증을 하려면 어떻게 동작할까?

UsernamePasswordAuthenticationFilter는 OAuth 2.0 인증을 할 수 없으니 인증되지 않은 채로 다음 필터로 넘어간다. 

그 후 OAuth2ClientAuthenticationProcessingFilter라는 필터에서 OAuth 2.0을 이용한 인증이 진행된다. 

이 필터로 OAuth를 쓴다고 설정하면 스프링 시큐리티가 설정해준다. 
<br />




여러 필터를 거치며 인증이 완료되면 해당 요청(Request)은 인증된 요청이 되는 것이다. 

요청에 대한 인증은 UsernamePasswordAuthenticationFilter가 담당한다.



```
public Authentication attempAuthentication(HttpServletRequest request, HttpServletResponse response) {
	
	...
	
	UsernamePasswordAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(username, password);
	this.setDetails(request, authRequest);
	return this.getAuthenticationManager().authenticate(authRequest);
}
```





`attempAuthentication(request, response)` 메소드를 보면 요청으로부터

username, password를 얻어오고

그 값으로 UsernamePasswordAuthenticationToken(Authentication)을 생성한다. 

그 다음 참조하고 있던 AuthenticationManager(구현체: ProviderManager)에게 인증을 진행하도록 위임한다.
<br />


여기서 UsernamePasswordAuthenticationToken은 Authentication 인터페이스의 구현체다. 

Authentication의 구현체만 AuthenticationManager 인증 과정을 수행할 수 있다.
<br />




그렇다면 개발자가 스프링 시큐리티를 이용해 인증 절차를 만들려면? 

AuthenticationProvider 인터페이스를 구현해서 ProviderManager가 

그 클래스의 객체에게 인증을 위임하도록 하면 개발자가 원하는 인증 처리를 할 수 있게 된다.


<br />