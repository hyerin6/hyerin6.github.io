---
title: "로그인한 User 정보는 어디에서 가져올까?"
categories:
  - Spring 
tags:
  - spring 
---


<br />


spring security를 사용하지 않고 Kakao API와 JWT를 이용해 직접 회원가입, 로그인을 구현했다. 

유저 정보가 필요한 API들이 많은데 로그인에 성공한 유저의 정보는 어떻게 가져와야할까?

<br />
<br />

## 로그인한 정보 가져오는 방법 

### 1. 로그인에 성공할 때마다 DB에서 조회하기 

`JwtInterceptor` 에서 헤더의 Jwt AccessToken을 파싱해서 userId 값을 가져올 수 있다. 

userId 값으로 DB에서 유저 정보를 가져올 수 있는데 그렇다면 `Interceptor`에서  어디에 저장하면 

`Controller`나 `Service` 레이어에서 유저 정보를 사용할 수 있을까?



ThreadLocal 변수를 선언하면 멀티 스레드 환경에서 각 스레드마다 독립적인 변수를 가지고, 

`get()`, `set()` 메소드를 통해 값에 대해 접근할 수 있다.

즉, Java의 ThreadLocal을 사용하면 `Interceptor`에서 유저 정보를 저장하고 원하는 곳에서 꺼내 사용할 수 있다.

<br />



* ThreadLocal 변수 선언 

```
public class UserContext {
	
	public static final ThreadLocal<User> CONTEXT = new ThreadLocal<>();

	public static User getContext() {
		return UserContext.USER_CONTEXT.get();
	}
	
}
```

<br />

* ThradLocal 값 저장 

```
// JwtInterceptor에서 조회 및 저장 
User user = userService.getUser(userId);
UserContext.CONTEXT.set(user);
```

<br />

* ThreadLocal 변수 사용 

```
User user = UserContext.getContext();
```


<br />
<br />

그러나 위 코드에 아쉬운 점이 있다. 

* User의 `userId` 값만 필요한 API가 있기 때문에 `JwtInterceptor` (사용자자의 요청마다) 에서 

  매번 DB에서 User 엔티티를 조회하는 것은 불필요하다. 



* User 정보가 필요한 곳에서 `User user = UserContext.getContext();` 코드가 반복된다. 

<br />  

위 두 문제를 해결하기 위해 다음과 같은 방법을 적용해봤다. 



<br />

<br />

### 2. 커스텀 어노테이션 만들기



우선 어노테이션을 만든다. 

```
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface Authenticationprincipal {
}

```



<br />



어노테이션을 통해 사용자 정보를 받기 위해

다음 `HandlerMethodArgumentResolver` 인터페이스를 구현해야 한다.

![스크린샷 2021-09-12 오후 8 35 29](https://user-images.githubusercontent.com/33855307/132986004-db8542fc-39c0-4258-814e-3920712bd082.png)


<br />

`supportsParameter` 메서드를 거쳐 `resolverArgument` 메서드에서 헤더값을 꺼내와 다음과 같이 유저 정보를 리턴할 수 있다.   

이번 프로젝트에서 구현한 코드는 다음과 같다.  

<br />

```
@RequiredArgsConstructor
@Component
public class AuthenticationArgumentResolver implements HandlerMethodArgumentResolver {

	private final JwtService jwtService;

	@Override
	public boolean supportsParameter(MethodParameter parameter) {
		return parameter.getParameterAnnotation(Authenticationprincipal.class) != null;
	}

	@Override
	public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
		NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
		String accessToken = HeaderUtil.getAccessToken(webRequest);
		return jwtService.decode(accessToken);
	}

}
```



<br />

<br />



### Spring Security에서는 User 정보를 어떻게 가져올까?

Spring Security 사용 시, 로그인한 유저 정보를 가져오는 방법은 다음과 같다. 



```
// 방법 1
Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
User user = (User)authentication.getPrincipal();

// 방법 2
@GetMapping("/")
public String home(@AuthenticationPrincipal User user) { ... }
```


<br />