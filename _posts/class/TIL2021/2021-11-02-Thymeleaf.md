---  
layout: post  
title: "Spring boot Thymeleaf"   
description: "Spring Web MVC"  
date: 2021-11-02  
tags: [Spring, Thymeleaf]  
categories: [Spring]
comments: true  
share: true  
---  

<br />  

테스트를 하기 전 편리함을 위해 배포, 테스트 자동화를 구축했다. 

<https://hyerin6.github.io/2021-10-20/jenkins/>

<br />

이번 프로젝트는 백엔드에 집중하기 위해 프론트를 따로 개발하지 않았지만 

테스트를 위해 사용자 회원가입, token, 유저 정보를 확인해야 했기 때문에   

간단하게 `index.html`, `login.html`, `user.html` 파일을 생성했다.  

<br />


스프링 부트가 자동 설정을 지원하는 템플릿 엔진 Thymeleaf로 구현했다. 
 
예전에 자세히 알아보지 않고 JSP를 많이 사용했었는데  


스프링 부트가 JSP를 권장하지 않는 이유와 Thymeleaf에 대해 알아보자. 

<br />
<br />

# 템플릿 엔진이란? 
템플릿 엔진은 동적 컨텐츠를 생성하는 방법이다. 

템플릿 양식과 특정 데이터 모델에 따른 입력 자료를 결합하여 결과 문서를 출력하는 소프트웨어를 말하며,   

view(html)와 data logic(DB connection)을 분리해주는 기능을 한다. 

<br />

* 서버 사이드 템플릿 엔진 : 서버에서 가져온 데이터를 미리 정의된 템플릿에 넣어 html을 그린 뒤 클라이언트에게 전달해준다.    
HTML 코드에서 고정적으로 사용되는 부분은 템플릿으로 만들어두고, 동적으로 생성되는 부분만 템플릿에 소스코드를 끼워넣는 방식으로 동작한다.  
<br />  
* 클라이언트 사이드 템플릿 엔진 : HTML 형태로 코드를 작성하며, 동적으로 DOM을 그리게 해주는 역할을 한다.
데이터를 받아서 DOM 객체에 동적으로 그려주는 프로세스를 담당한다.

<br />

#### 스프링 부트가 자동 설정을 지원하는 템플릿 엔진   
* FreeMarker  
* Groovy  
* Thymeleaf  
* Mustache  

<br />
<br />

# JSP 자동 설정을 지원하지 않는 이유
Spring boot는 JSP를 권장하지 않는다. 

JSP를 사용하면 WAR 패키징을 해야한다.   

WAR 패키징으로도 임베디드 톰캣으로 실행할 수 있고 배포할 수 있으나 

Undertow라는 최근에 만들어진 서블릿 엔진이 JSP를 지원하지 않는 등 제약사항이 있다.

또한 JSP에 대한 의존성을 넣으면 의존성 문제가 생기는 경우도 있다. 

<br />
<br />


# jar vs war
jar, war 모두 java의 jar 툴을 이용해 생성된 파일이며 애플리케이션을 쉽게 배포하고 동작시킬 수 있도록 관련 파일을 패키징해준다.   

<br />

### JAR (Java Archive)
* `.jar` 자바 프로젝트를 압축한 파일 
* 자바 리소스, 속성파일, 라이브러리 등이 포함되어 있다. 
* 원하는 구조로 구성이 가능하고 JDK에 포함된 JRE만 가지고도 실행 가능하다. 

<br />

### WAR(Web Application Archive)
* `.war` servlet/jsp 컨테이너에 배치할 수 있는 웹 애플리케이션 압축 파일 
* 웹 관련 자원만 포함한다. (JSP, Servlet, JAR, Class, HTML 등)
* JAR과 달리 WEB-INF 및 META-INF 디렉토리로 사전 정의된 구조를 사용하며 실행하기 위해서 Tomcat과 같은 웹 서버 또는 웹 컨테이너(was)가 필요하다.   
* WAR도 java의 jar 옵션 (`java -jar`)을 이용해 생성하는 JAR 파일의 일종이다.   

<br />
<br />

# Thymeleaf 
Thymeleaf는 비교적 최근에 만들어진 템플릿 엔진이며 서버사이드 자바 템플릿 엔진의 한 종류이다.

JSP와 Thymeleaf의 가장 큰 차이점은 JSP와 달리 Servlet Code로 변환되지 않다는 점이다. 

따라서 비즈니스 로직과 분리되어 오로지 View에 집중할 수 있다.

<br />

### 의존성 추가

```
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```

<br />

### html 파일 생성 위치  

`src` > `main` > `resource` > `templates` 에 생성하면 된다.  


```java
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

위 코드처럼 th 네임스페이스를 추가하면 된다.


<br />


### 테스트 
`.andDo(print())` 를 통해 렌더링 된 결과까지 확인할 수 있다. 

이것은 Thymeleaf를 사용했기 때문이다.   

JSP를 사용하면 본문을 확인(렌더링된 결과)하는 것이 매우 힘들다.

서블릿 엔진 자체가 JSP 템플릿을 완성시키기 때문에 응답으로 내보낼 최종적인 View를 확인하기 위해서는 서블릿 엔진 개입이 필수적이다.

반면 Thymeleaf는 서블릿 컨테이너의 개입 없이 독자적으로 최종적인 View를 완성한다.

테스트에서 사용한 mockMVC는 가짜 서블릿 컨테이너이며 실제 서블릿 컨테이너가 할 수 있는 일을 다 할 수 없다. 

<br />


