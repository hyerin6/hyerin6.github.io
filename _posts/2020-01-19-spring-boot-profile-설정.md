---
layout: post
title: "Spring Boot Profile 설정"
description: " "
date: 2020-01-19
tags: [spring, nexters16]
comments: true
share: true
---

Spring Boot를 이용하면 보다 손쉽게 profile을 설정할 수 있다.

---

#### 환경    
- local     
내 컴퓨터 환경   
- develop      
개발 서버 환경   
- production    
실제 운영 서버 환경     
- test     
테스트 서버 환경   


### 1) application.properties  
application-{profile}.properties 형식으로 파일을 생성한다.   

```
application-default.properties
application-dev.properties
application-prod.properties
```

**jpa application.properties 설정에서..**    

```
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
```

예를들어, 엔터티 클래스의 studentNumber 속성에 자동으로 연결될 데이터베이스 필드 명이    
studentNumber 형태이면 이 설정이 필요하다. (camel case)  
student_number 형태이면 이 설정이 필요없다. (snake case)    
camel case의 예: departmentManagerOfficeNumber     
snake case의 예: department_manager_office_number     

### 2) application.yml    
- 한 파일에 profile 설정이 가능     
- spring.profiles.active로 profile 설정이 가능하다.   
- '---' 로 나누면 다른 파일에서 불러온 것처럼 사용할 수 있다.      
- profiles를 설정해주지 않으면 기본 default로 동작한다.    

```
spring:
  profiles:
    active: local
  jpa:
    database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
    open-in-view: false
    properties:
      hibernate:
        format_sql: true
    generate-ddl: true
    database: mysql
    show-sql: true

--- #Production 
spring:
  profiles: production
  datasource:
    url:
    username: 
    password: 

--- #local
spring:
  profiles: local
  datasource:
    url: 
    username: 
    password: 
```

-----------------------

지금 프로젝트에서는 젠킨스 자동 배포를 사용하고 있기 때문에 DB 관련 설정 파일을 분리해서 배포하는 경우,        
default 파일의 active를 push 전에 production으로 다시 설정해줘야 하는 번거로움이 생긴다.          
그래서 application.yml 파일을 하나로 관리하면서 서버에는 production으로 설정해놓고 git에 있는 코드를 clone(or pull) 해서 개발할 때는 각자
applicaiton.yml 파일을 만들고 슬랙으로 공유하는 게 좋은 것 같다 ! 
