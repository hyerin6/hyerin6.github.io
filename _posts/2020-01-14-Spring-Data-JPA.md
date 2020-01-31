---
layout: post
title: "JPA 식별자 자동 생성"
description: "Spring Data JPA"
date: 2020-01-14
tags: [spring, jpa, nexters16]
comments: true
share: true
---

#### JPA가 제공하는 데이터베이스 기본 키 생성 전략   
- 직접 할당 : 기본 키를 어플리케이션에서 직접 할당한다.  
```@Id``` 만 사용한다.      
- 자동 생성 : 대리 키 사용 방식     
```@Id```와 ```@GeneratedValue```를 함께 사용한다.     
    + IDENTITY : 기본 키 생성을 데이터베이스에 위임한다.  
    + SEQUENCE : 데이터베이스 시퀀스를 사용해서 기본 키를 할당한다.   
    + TABLE : 키 생성 테이블을 사용한다.   
    + AUTO : 데이터베이스에 관계없이 식별자를 자동 생성하라는 의미, DB가 변경되더라도 수정할 필요 없다.  

#### 1. GenerationType.AUTO    
```java
@Id 
@GeneratedValue // @GeneratedValue(strategy=GenerationType.AUTO) 와 동일 
protected Long id;
```
**JPA 공급자에 따라 기본 설정이 다르다.**  
- Oracle을 사용할 경우 SEQUENCE가 기본   
- MS SQL Server의 경우 IDENTITY가 기본     
- (Hibernate 5.0부터) MySQL의 AUTO는 IDENTITY가 아닌 TABLE을 기본 시퀀스 전략으로 선택된다.     

> Spring Boot는 Hibernate의 id 생성 전략을 그대로 따라갈지 말지를 결정하는 useNewIdGeneratorMappings 설정이 있다.  
1.5에선 기본값이 false, 2.0부터는 true  
Hibernate 5.0부터 MySQL의 AUTO는 IDENTITY가 아닌 TABLE을 기본 시퀀스 전략으로 선택된다.  
즉, 1.5에선 Hibernate 5를 쓰더라도 AUTO를 따라가지 않기 때문에 IDENTITY가 선택  
2.0에선 true이므로 Hibernate 5를 그대로 따라가기 때문에 TABLE이 선택  
(Hibernate 6.0에서 다시 IDENTITY로 돌아간다고 합니다...!)   

