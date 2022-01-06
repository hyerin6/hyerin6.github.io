---
title: "HTTP API vs. RESTful API"     
categories:
  - Web
tags:
  - web 
toc: true
author_profile: true
toc_sticky: true
--- 


> API, REST, REST API, RESTful API, HTTP, URL, URI, URN...        
> 개발하면서 많은 키워드들을 보게 되는데 정확히 어떤 의미인지 정리해보자.       




<br />  

# API   
```
예) 
나 - 리모컨 - 에어컨
손님 - 점원 - 요리사 
```

여기서 API는 리모컨과 점원이라고 할 수 있다.   
<br />   

API는 응용 프로그램(애플리케이션)에서 운영체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 의미한다.    
즉 API는 애플리케이션과 운영체제, 애플리케이션과 프로그래밍 언어가 제공하는 기능 사이의 `상호작용`을 도와준다.   

<br />   
<br />   

# REST (Representational State Transfer)      
REST는 자원을 이름으로 구분, 해당 자원의 상태를 주고 받는 것을 의미한다.    

* http uri를 통해 자원을 명시한다.   
* http method를 통해 해당 자원에 대한 CURD 연상을 적용하는 것을 의미한다.   

<br />    

### REST 구성 요소   
* 자원(resource): HTTP URI    
* 자원에 대한 행위: HTTP Method   
* 자원에 대한 행위의 내용: HTTP Message Pay Load   


<br />    

### REST 특징  
* server-client 구조  
* stateless (무상태)  
* cacheable (캐시 처리 기능)  
* Layered system (계층화)   
* uniform interface (인터페이스 일관성)  


<br />      
<br />      
 

# REST API  
REST의 원리를 따르는 API를 의미한다.   
리소스에 대한 행위를 http method로 정의하는 방식이다.     
<br />  

* `http://hyerin.com/run` 명사 사용, 마지막에 슬래시(`/`) ❌     
* `http://hyerin.com/test-blog` 언더바(`_`) ❌, 하이픈(`-`) 🆗      
* `http://hyerin.com/photo.png` ➡️ `/photo` 파일 확장자는 URI에 포함되지 않는다.      
* `http://hyerin.com/post/1` 행위를 포함하지 않는다.   


<br />    
<br />    

# RESTful API    
REST API 설계 가이드를 따라 API를 만드는 것이다.    
RESTful API 자체만으로 API의 목적이 무엇인지 쉽게 알 수 있다.   

<br />  
<br />  

# HTTP   
하이퍼텍스트(html) 문서를 교환하기 위해 만들어진 프로토콜이다.    
윕상에서 네트워크로 서버끼리 통신할 때 어떠한 형식으로 서로 통신하자고 규정해 놓은 통신구조라고 보면 된다.     

<br />   


### 비연결성 (Connectionless)    
비연결성은 클라이언트와 서버가 한 번 연결을 맺은 후,   
클라이언트 요청에 대해 서버가 응답을 마치면 연결을 끊어버리는 성징을 말한다.     

http 프로토콜은 왜 한 번 맺은 연결을 끊을까?  
http는 인터넷 상에서 불특정 다수의 통신 환경을 기반으로 설계되었다.    
만약 서버에서 다수의 클라이언트와 연결을 계속 유지해야 한다면 많은 리소스가 발생한다.   
<br />     

**장점)** 연결을 유지하기 위한 리소스를 줄이면 더 많은 연결을 할 수 있으므로 비연결적인 특징을 갖고 있다.   
**단점)** 그러나 서버는 클라이언트를 기억하고 있지 않으므로 동일한 클라이언트의 모든 요청에 대해   
매번 새로운 연결을 연결/해제 해야 하므로 오버헤드가 발생한다.  
<br />       

**keepAlive**  
오버헤드에 대한 해결책으로 http의 keepAlive 속성을 사용할 수 있다.    
keepAlive는 지정된 시간동안 서버와 클라이언트 사이에서 패킷 교환이 없을 경우,    
상대방에게 안부를 묻기 위해 패킷을 주기적으로 보내는데 패킷에 반응이 없으면 접속을 끊는다.      
그러나 keepAlive 속성이 완벽한 해결책은 아니다.   
keepAlive 상태로 유지하기 위해 메모리를 많이 사용하게 되므로 주의해야 한다.   

<br />           
<br />         


### 무상태 (Stateless)   
Connectionless로 인해 서버는 클라이언트를 식별할 수 없다. 이를 Stateless라고 한다.   
클라이언트의 상태를 모른다는 것은 요청이 오면 그에 따른 응답을 할 뿐   
각각의 요청과 응답은 독립적이며, 어떤 상태를 저장하지 않는다는 의미다.   
<br />

```
쇼핑몰에서 옷을 구매하기 위해 로그인 했다.     
로그아웃하기 전까지 '로그인'이라는 상태가 계속 유지되는 것처럼 보이지만     
로그인 상태는 유지되지 않는다.   
```  

<br />  

그렇다면 매번 로그인을 다시 해야하나?     
상태를 기억하는 방법은 다양하다.    
  
* Cookie, Session, Token: <https://hyerin6.github.io/2021-07-23/session-token/>    
* OAuth 2.0: <https://hyerin6.github.io/2021-07-23/OAuth2/>  



<br />    
<br />   

### 응답 상태 코드   
* 100 ~ 메시지 정보   
* 200 ~ 요청 성공   
* 300 ~ 리다이렉션  
* 400 ~ 클라이언트 에러   
* 500 ~ 서버 에러   
* 참고: <https://brunch.co.kr/@leedongins/65>    




<br />    
<br />   


### HTTP Method    
클라이언트가 서버에 요청할 때 어떤 목적을 갖는 행위힌지 HTTP Method에 명시한다.    
<br />  


* GET  
GET 메서드는 특정 리소스의 표시를 요청한다.   
GET을 사용하는 요청은 오직 데이터를 받기만 한다.  


* HEAD  
HEAD 메서드는 GET 메서드의 요청과 동일한 응답을 요구하지만, 응답 본문을 포함하지 않는다.  


* POST  
POST 메서드는 특정 리소스에 엔티티를 제출할 때 쓰인다.    
이는 종종 서버의 상태의 변화나 부작용을 일으킨다.    


* PUT  
PUT 메서드는 목적 리소스 모든 현재 표시를 요청 payload로 바꾼다.  


* DELETE  
DELETE 메서드는 특정 리소스를 삭제한다.   


* CONNECT  
CONNECT 메서드는 목적 리소스로 식별되는 서버로의 터널을 맺는다. 


* OPTIONS  
OPTIONS 메서드는 목적 리소스의 통신을 설정하는 데 쓰인다.


* TRACE (en-US)  
TRACE 메서드는 목적 리소스의 경로를 따라 메시지 loop-back 테스트를 한다. 


* PATCH  
PATCH 메서드는 리소스의 부분만을 수정하는 데 쓰인다. 




<br />    
<br />   



# URI, URL, URN   

![스크린샷 2021-07-24 오후 4 44 37](https://user-images.githubusercontent.com/33855307/126861448-ed582c74-9085-45ad-b5dc-8aab2e6b7c20.png)  

<br />  

### URI   
서버 리소스 이름은 통합 자원 식별자(uniform resource identifier) 혹은 URI라고 불린다.   
URI는 인터넷의 우편물 주소 같은 것으로, 정보 리소스를 고유하게 식별하고 위치를 지정할 수 있다.   
그리고 이 URI에는 두 가지 형태가 있는데 이것이, URL, URN이라는 것이다.  


<br />  
<br />  

### URL  
통합 자원 지시자(uniform resource locator, URL)는 URI의 가장 흔한 형태이다.   
URL은 특정 서버의 한 리소스에 대한 구체적인 위치를 서술한다.   
URL은 리소스가 정확히 어디에 있고 어떻게 접근할 수 있는지 분명히 알려준다.  

<br />  
<br />  


### URN   
URI의 두 번째 형태는 유니폼 리소스 이름(uniform resource name, URN) 이다.    
URN은 콘텐츠를 이루는 한 리소스에 대해, 그 리소스의 위치에 영향 받지 않는 유일무이한 이름 역할을 한다.    
이 위치 독립적인 URN은 리소스를 여기저기로 옮기더라도 문제없이 동작한다.    
리소스가 그 이름을 변하지 않게 유지하는 한, 여러 종류의 네트워크 접속 프로토콜로 접근해도 문제없다.    
예를 들어, 다음의 URN은 인터넷 표준 문서 'RFC 2141'가 어디에 있거나 상관없이 그것을 지칭하기 위해 사용할 수 있다.  


단순하게 말하자면, URI는 규약이고, URL은 규약에 대한 형태라고 생각하면 된다.   

<br />    
<br />   
  

### 참고  
* <https://stackoverflow.com/questions/176264/what-is-the-difference-between-a-uri-a-url-and-a-urn>  

<br />     


