---
title: "Token 인증 방식이 생긴 이유"    
categories:
  - Web
tags:
  - web 
toc: true
author_profile: true
toc_sticky: true
--- 


Session 인증 방식은 공부해보기 전에도 많이 들었고 많이 사용하기 때문에 익숙하다.       
그런데 왜 Token 방식이 생겼을까? 어떤 차이가 있는지 알아보기로 했다.      

session, cookie, token을 학습하며 자주 마주치는  
CORS, XSS, CSRF도 간단하게 알아보자.   



<br />   

# Stateless한 HTTP     
HTTP는 stateless한 특성을 가지고 있어 각 통신의 상태는 저장되지 않는다.     
즉 매번 새 페이지를 요청할 때마다 로그인을 해야 한다는 것인데, 이렇게 되면 사용자는 매우 불편해진다.   

이 문제를 해결하기 위한 대표적인 도구가 바로 세션(Session)과 토큰(Token)이다.  
세션과 토큰 모두 존재 목적은 거의 같지만 차이점은 많다.   

<br />     
<br />       


# 세션 기반 인증 (Session, Cookie)   
![KakaoTalk_Photo_2021-07-23-23-33-31 001](https://user-images.githubusercontent.com/33855307/126797627-b2c53bcc-e353-417b-9bb7-2aa8adef4834.jpeg)


<br />     
<br />         

# 토큰 기반 인증   
![KakaoTalk_Photo_2021-07-23-23-33-31 002](https://user-images.githubusercontent.com/33855307/126797619-e9645816-ff93-462f-bd97-12170fe29ae9.jpeg)

<br />         
  

# CORS(Cross-Origin Resource Sharing)        
CORS는 보안 기능이다.      
CORS가 도메인 `website.com`에서 사용 가능한 서버에서 구성되면,    
리소스는 AJAX를 통해 동일한 도메인에서 제공되는 주소에서 시작되어야 한다.       

`domain-b.com`에서 CORS를 활성화하고 `domain-b.com`의 GET 요청만 허용하도록 구성한 경우,     
`domain-a.com`에 호스팅된 `https://domain-b.com/images/test.png` 이미지를 이용하려고 할 때     
대부분의 방문자에게 해당 이미지가 로드되지 않는다.

브라우저는 origin URL을 담고 있는 `origin`이라는 요청헤더를 더해준다.     
서버는 이때 반드시 `Access-Control-Allow-Origin`이라는 응답헤더로 답해줘야한다.       
해당 응답헤더의 값은 반드시 origin 요청헤더의 값과 같거나 `*`(모든 URL이 괜찮다는 것을 의미함)이어야한다.       

<br />       
<br />         


# XSS(Cross Site Scripting)   
xss는 주입식 공격이다.   
공격자가 악의적인 스크립트를 신뢰할 수 있는 웹사이트에 삽입하는 방법의 공격이다.     

<br />       
<br />         

# CSRF(Cross Site Request Forgery)   
CSRF는 악의적인 웹사이트, 전자 메일, 블로그, 인스턴트 메시지 또는 프로그램으로 인해    
사용자의 웹 브라우저가 사용자가 인증 된 다른 신뢰할 수 있는 사이트에서 원치 않는 작업을 수행 할 때 발생하는 공격 유형이다.   

이 취약점은 브라우저가 세션 쿠키, IP주소 또는 각 요청과 유사한 인증 리소스를 자동으로 보내는 경우에 발생 할 수 있다.    


<br />            


