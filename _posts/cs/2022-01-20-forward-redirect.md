---
layout: post  
title: "Forward & Redirect"    
description: "Filter, Interceptor에서 어떻게 동작할까?"  
date: 2022-01-20  
tags: [spring]  
categories: [Spring]  
comments: true  
share: true
---  

<br />

## Redirect    
redirect는 서버가 클라이언트에게 응답결과로 JSP나 HTML 같은 데이터가 아니라 요청할 주소를 응답 결과로 전달하는 것을 의미한다.       

* 클라이언트는 응답 결과로 받은 요청 주소를 직접 요청하게 된다.   
* 브라우저가 요청하는 것이기 때문에 주소창의 주소는 변경된다.   
* 새로운 요청이 발생하는 것이기 때문에 HttpServletRequest 객체는 소멸 후 새롭게 생성되며     
  HttpSession 객체는 그대로 유지된다.     


```java 
@GetMapping("/redirect")
public String redirect() {
    return "redirect:/sub";
}
```

위와 같이 `return "redirect:/sub";` 리턴하면   
브라우저가 직접 요청하는 것이기 때문에 sub.jsp가 실행되고 주소창의 주소도 변경된다.    

<img width="600" alt="스크린샷 2022-01-20 오후 10 21 01 1" src="https://user-images.githubusercontent.com/33855307/150346655-54c733d8-8dee-41c8-bf5b-4dab6d700acb.png">   


<br />  
<br />

## Forward   
forward는 코드의 흐름을 서버상에서만 이동하는 것이다.     

* 브라우저는 다른 곳으로 흐름이 이동되었다는 것을 알 수 없기 때문에 주소창의 주소는 변경 되지 않는다.  
* HttpServletRequest, HttpSession 모두 유지된다.  

<img width="600" alt="스크린샷 2022-01-21 오후 12 20 07" src="https://user-images.githubusercontent.com/33855307/150459871-d74bc093-205f-4624-a035-124f010b8d68.png">

<br />
<br />

## 차이점   
redirect와 forward의 차이점은 크게 두 가지로 나눌 수 있다.     

* URL의 변화 여부 (redirect ⭕️ / forward ❌)     
* 객체의 재사용 여부 (redirect ❌ / forward ⭕)    
<br />  

위와 같은 차이점이 있기 때문에 상황에 맞게 적절하게 선택할 수 있어야 한다.     

예) 게시판 게시글 작성 
사용자가 보낸 요청 정보를 이용해 글쓰기 기능을 수행해야 한다면 redirect를 사용해야 한다.    
사용자가 계속 확인이나 완료 버튼을 누르면 요청 정보가 그대로 살아있으면 똑같은 글이 여러번 등록될 수 있기 때문이다.   
redirect는 요청 정보가 남아 있지 않기 때문에 글쓰기가 여러 번 수행되지 않는다.   

단순하게 시스템(session, DB)에 변화가 생기는 요청은 redirect 방식으로 응답하고   
단순 조회의 경우 forward 방식으로 응답하는 것이 좋다.   

<br />
<br />

## Filter, Interceptor에서의 차이   
redirect는 `요청` → [`302 응답`](https://developer.mozilla.org/ko/docs/Web/HTTP/Status/302) → `Location에 대한 요청` → `Location에 대한 응답` 으로 동작하기 때문에       
필터에는 처음 요청한 URL과 리다이렉션된 URL 모두가 Filter와 Interceptor에 걸린다.                

forward는 `요청 → 서버 내에서 target URL로 요청 전달 → target URL에 대한 응답` 이므로,             
Interceptor는 두 URL에 대해 모두 동작하지만, DispatcherServlet 내부에서 포워딩되기 때문에            
Filter에는 처음 요청한 URL만 걸린다. (Filter는 DispatcherServlet 바깥에 있음)          


<br />  
