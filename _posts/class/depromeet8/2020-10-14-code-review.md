---
layout: post
title: "Maven Wrapper, 상수화, 테스트"  
description: "depromeet final 5 1차 코드리뷰"
date: 2020-10-14
tags: [depromeet]
comments: true
share: true
--- 
 
디프만 파이널 프로젝트를 시작하면서 회원가입 기능을 구현해보며 코드리뷰를 했다.      
SNS 로그인 기능을 사용하기로해서 처음 구현한 코드는 프로젝트에서 사용하지 않지만   
코드리뷰를 다시 보면서 공부하기로 했다.     


## Maven Wrapper의 용도      

Apache Maven은 자바 프로젝트 의존성 관리 도구이다.     
이를 좀 더 쉽게 최신 버전을 유지하기 위해 Maven Wrapper가 나왔다.     
Maven Wrapper(mvnw)가 설정된 프로젝트는 maven 설치 없이도 빌드가 가능하다.     
즉 Apache Maven을 프로젝트에서 요구하는 버전으로 유지하기 위해 사용하는 도구이다.     

**실행**    

일반적으로 Maven은 다음 명령어로 실행한다.   

```  
mvn clean package   
```  



mvnw가 설정된 프로젝트는 다음 명령어로 실행한다.   

```  
./mvnw clean package   
```  

<br />    

## 항상 같은 값을 리턴하는 경우      

```java
return new ResponseEntity<>(HttpStatus.BAD_REQUEST);     
```

Controller에서 위 코드를 반복하게 되었고, 상수화하면 불필요한 객체 생성을 줄일 수 있다는걸 알게 되었다.    


```java
private final static ResponseEntity BAD_REQUEST = new ResponseEntity(HttpStatus.BAD_REQUEST);
```
 
<br />     

## 단위테스트 & 통합테스트    

- 단위 테스트 : 클래스를 하나씩 따로 테스트하는 것   
- 통합 테스트 : 클래스들의 객체를 서로 연결하여 같이 테스트하는 것   

Junit 테스트에 대한 것은 이미 블로그에 정리해둔 것이 있다.     
[Junit Test 블로그 보러가기](https://hyerin6.github.io/2020-01-13/Junit-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EA%B5%AC%ED%98%84/)    

<br />     


