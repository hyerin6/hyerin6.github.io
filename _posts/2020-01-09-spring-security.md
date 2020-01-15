---
layout: post
title: "Spring Security"
description: " "
date: 2020-01-09
tags: [spring]
comments: true
share: true
---

### 1. Spring Security   
#### 1) authentication authorization   
**authentication**   
사용자의 신원을 식별하는 기능이다. 쉽게 말해서 로그인 기능, 로그인 아이디와 비밀번호를 사용하여 사용자 신원을 확인하는 방법이다.   

**authorization**    
권한 관리 기능이다.   
현재 사용자가 어떤 권한을 가지고 있는지 이 기능을 실행할 수 있는 권한은 무엇인지의 정보를 바탕으로 권한 관리 및 통제를 수행한다.   

**role**   
authorization 에서 권한을 역할(role) 이라고 부르기도 한다.    
예) 관리자 권한 = 관리자 역할   
spring security 에서 권한(역할)은 다음과 같이 표현하는 것이 관례이다.   
예) ROLE_ADMIN, ROLE_STUDENT    

#### 2) Spring Security   
authentication 기능, authorization 기능, 보안 공격에 대한 보호 기능 등이 잘 구현된 프레임워크이다.   
spring security 를 이용해서 로그인 기능과 권한관리 기능을 구현하는 것이 바람직하다.    

#### 3) Spring Security 확장 태그   
```html
<%@ taglib uri="http://www.springframework.org/security/tags" prefix="sec" %> 
```
spring security 확장 태그를 사용하기 위한 선언이다. JSP 파일의 선두에 이 선언이 있어야 한다.   

```jsp 
<sec:authorize access="authenticated"> 
   . . .
</sec:authorize>
```
spring security 확장 태그이다.   
현재 사용자가 로그인한 사용자인 경우에만 이 태그 사이의 내용이 출력된다.   

```jsp
<sec:authentication property="user.loginId" />
```
현재 로그인된 사용자 객체의 loginId 속성값을 출력한다.       
즉 User 객체의 getLoginId() 메소드 리턴값이 출력된다.       

```jsp
<sec:authentication property="user.name" />
```
현재 로그인된 사용자 객체의 name 속성값을 출력한다.   

```jsp
<sec:authentication property="user.email" />
```
현재 로그인된 사용자 객체의 email 속성값을 출력한다.   

#### 4) Spring Security 권한 검사   
**@Secured("ROLE_ADMINISTRATOR")**    
이 어노테이션을 액션 메소드에 붙이면 그 액션 메소드는 "ROLE_ADMINISTRATOR" 권한을 가진 사용자만 실행할 수 있다.   
이 권한이 없는 사용자가 실행하면 에러가 발생한다. 클래스에 붙였을 때도 권한이 없는 사용자가 실행하면 마찬가지로 에러가 발생한다.   

**request.IsUserInRole(String role)**   
Java 코드에서 현재 사용자 권한을 검사할 때, 이 메소드를 사용한다.       
```java
@RequestMapping("/user/index.do")
public String index(Model model, HttpServletRequest request) {
    if (request.isUserInRole("ROLE_PROFESSOR"))
        return "redirect:professor";
    if (request.isUserInRole("ROLE_ADMINISTRATOR"))
        return "redirect:adminostrator";
    return "redirect:student";
}
```

**뷰에서 사용자 권한을 검사할 때**   
```jsp
<sec:authorize access="hasRole('ROLE_ADMINISTRATOR')">
   . . .
</sec:authorize>
```
현재 사용자가 ROLE_ADMINISTRATOR 권한일 경우에 이 태그와 사이 내용을 출력한다.   


