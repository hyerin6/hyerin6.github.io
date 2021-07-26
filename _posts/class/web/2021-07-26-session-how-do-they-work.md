---
layout: post     
title: "Session은 어떻게 작동할까?"    
description: "같은 key를 쓰는데 어떻게 구분할까?"  
date: 2021-07-26    
tags: [web, session]  
categories: [Web]  
comments: true
share: true
---  

<br />      

# 질문        
* 유튜버 개발바닥의 기술면접 대표 질문 3가지: <https://youtu.be/3ArYMq5AomI>        
<br />      
  
요즘 개발서적 외에 유튜브로도 가끔 개발 관련 영상을 보는데    
위 영상의 질문이 좋아 정리해야겠다는 생각이 들었다.    

```
HttpSession.getAttribute("user");

사용자 A가 접속해도 "user"를 key로 가져오고 
사용자 B가 접속해도 "user"를 key로 가져온다.   
같은 key를 쓰는데 어떻게 A와 B를 구분해서 값을 가져올까?
```

<br />   
<br />   

## 답변     
session은 각 클라이언트마다 하나씩 생성되어 제공합니다.    
그래서 `HttpSession.getAttribute("user")`는 서로 다른 값을 리턴합니다.   

<br />      
<br />      

## 그럼 왜 session은 각 클라이언트마다 하나씩 생성될까?   

* HTTP 특성: <https://hyerin6.github.io/2021-07-23/restfulapi/>   
* Session 특성: <https://hyerin6.github.io/2020-02-06/session/>    
* Cookie 특성: <https://hyerin6.github.io/2020-02-06/cookie/>     

<br />   

**HTTP 특성**    
(1) Stateless(무상태) 프로토콜이다.       
(2) 클라이언트와 서버가 요청과 응답을 주고 받으면 연결이 끊어진다.   
(3) 클라이언트가 다시 요청하면 서버는 이전 요청을 기억하지 못한다.  
(4) 클라이언트와 서버는 서로 상태를 유지하지 않는다.   

<br />    

**Session 특성**    
(1) 클라이언트가 처음 서버에 연결을 하면 어떤 하나의 SessionID가 생성된다.   
(2) SessionID는 고유한 ID이다.   
(3) 이 ID를 통해 서버는 클라이언트를 구분하고 요청에 대한 응답을 한다.
<br />         

즉 식별할 수 있는 고유한 값이 없다면 서버는 요청에 대한 응답을 내릴 수가 없을 것이다.      
때문에 세션에서는 식별이 가능한 고유 값을 생성하고 서버는 그 값으로 사용자를 구분합니다.       

<br />      
