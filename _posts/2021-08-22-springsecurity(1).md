---
title: "Spring Security란?"
categories:
  - Spring 
tags:
  - spring 
---

<br />

# Spring Security
Spring 기반의 애플리케이션의 보안을(인증과 권한, 인가 등) 담당하는 스프링 하위 프레임워크다.   

SpringSecurity는 인증과 권한에 대한 부분을 Filter 흐름에 따라 처리하고 있다.    

Filter는 DispatcherServlet으로 가기 전에 적용되므로 가장 먼저 URL 요청을 받지만,   

Interceptor는 Dispatcher와 Controller 사이에 위치한다는 점에서 적용 시기의 차이가 있다.   



![springsecutiry_architecture](https://user-images.githubusercontent.com/33855307/130345769-916b067f-d136-4bcc-8800-1ee0fc0ba598.jpeg)

<br />



### SpringContextHolder 

SpringContextHolder는 보안 주체의 세부 정보를 포함하여 응용프로그램의 현재 보안 컨텍스트에 대한 세부 정보가 저장된다.   


<br />


### SecurityContext  

Authentication을 보관하는 역할이며, SecurityContext를 통해 Authentication 객체를 꺼내올 수 있다.   


<br />


### Authentication 

Authentication은 현재 접근하는 주체의 정보와 권한을 담은 인터페이스다.   

Authentication 객체는 SecurityContext에 저장되며, 

SecurityContextHolder를 통해 SecurityContext에 접근하고 

SpringContext를 통해 Authentication에 접근할 수 있다. 



```
public interface Authentication extends Principal, Serializable {
	// 현재 사용자의 권한 목록을 가져옴 
	Collection<? extends GrantedAuthority> getAuthorities();
	
	// credenrials(ex.password)을 가져옴 
	Object getCredentials();
	
	Object getDetails();
	
	// Principal 객체를 가져옴 
	Object getPrincipal();
	
	// 인증 여부를 가져옴 
	boolean isAuthenticated();
	
	// 인증 여부를 설정함 
	void setAuthenticated(boolean isAuthenticated) throws IllegalArgumentException;
}
```


<br />


### UsernamePasswordAuthenticationToken 

UsernamePasswordAuthenticationToken은 인증 전 객체를 생성하고 인증 완료된 객체를 생성하는 생성자를 갖고 있다.  


<br />


### AuthenticationProvider  

AuthenticationProvider에서는 실제 인증에 대한 부분을 처리하는데 

인증 전의 Authentication 객체를 받아서 인증이 완료된 객체를 반환하는 역할을 한다. 

AuthenticationProvider 인터페이스를 구현해서 Custom한 AuthenticationProvider을 작성해서 AuthenticationManager에 등록한다. 



```
public interface AuthenticationProvider {
	// 인증 전 Authentication 객체를 받아서 인증된 Authentication 객체를 반환 
	Authentication authenticate(Authentication var1) throw AuthenticationException;
	
	boolean supports(Class<?> var1);
}
```


<br />


### AuthenticationManager

인증에 대한 부분은 SpringSecurity의 AuthenticationManager을 통해서 처리하는데 

실제로는 AuthenticationManager에 등록된 AuthenticationProvider에 의해 처리된다. 

인증에 성공하면 인증 성공 객체를 생성하여 SecurityContext에 저장한다. 

인증 상태를 유지하기 위해 세션에 보관하며, 인증이 실패한 경우 AuthenticationException을 발생시킨다.
<br />


AuthenticationManager를 implements한 ProviderManager는 실제 인증 과정에 대한 로직을 가지고 있는 

AuthenticationProvider를 List로 가지고 있으며, ProviderManager는 for문을 통해 모든 provider를 조회하면서 authenticate 처리를 한다. 
<br />


ProviderManager에 개발자가 직접 구현한 CustomAuthenticationProvider를 등록하는 방법은 

WebSecurityConfigureAdapter를 상속해 만든 SecurityConfig에서 할 수 있다. 

WebSecurityConfigureAdapter의 상위 클래스에서는 AuthenticationManager를 가지고 있기 때문에 

직접만든 CustomAuthenticationProvider를 등록할 수 있다. 


<br />


### UserDetails

인증에 성공하여 생성된 UserDetails 객체는 Authentication 객체를 구현한 UsernamePasswordAuthenticationToken을 생성하기 위해 사용된다. 


<br />


### UserDetailsService 

UserDetailsService 인터페이스는 UserDetails 객체를 반환하는 단 하나의 메소드를 가지고 있는데 

일반적으로 이를 구현한 클래스의 내부에 UserRepository를 주입받아 DB에서 User 정보를 가져온다. 



```
public interface UserDetailsService {
	UserDetails loadUserByUsername(String var1) throws UsernameNotFoundException;
}
```


<br />
<br />

### 참고
* <https://mangkyu.tistory.com/76>
* <https://jeong-pro.tistory.com/205>
* <https://coding-start.tistory.com/153>

<br />
