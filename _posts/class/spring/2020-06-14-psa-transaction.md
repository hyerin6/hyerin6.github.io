---
layout: post
title: "토비의 스프링 (5)"  
description: "트랜잭션의 서비스 추상화" 
date: 2020-06-14
tags: [spring]
comments: true
share: true
---


# 트랜잭션의 서비스 추상화    

### 기술과 환경에 종속되는 트랜잭션 경계설정 코드   

로컬 트랜잭션은 하나의 DB Connection에 종속된다.   
한 개 이상의 DB로의 작업을 하나의 트랜잭션으로는 불가능하다.   

각 DB와 독립적으로 만들어지는 Connection을 통해서가 아니라   
별도의 트랜잭션 관리자를 통해서 트랜잭션을 관리하는 글로벌 트랜잭션 방식을 사용해야 한다.   

글로벌 트랜잭션을 적용하면 여러 개의 DB가 참여하는 작업을 하나의 트랜잭션으로 만들 수 있다.   

또 JMS와 같은 트랜잭션 기능을 지원하는 서비스도 트랜잭션에 참여시킬 수 있다.       


<br/>    

자바는 JDBC 이외 이런 글로벌 트랜잭션을 지원하는 
트랜잭션 매니저를 지원하기 위한 API인 JTA (Java Transactional API)를 제공하고 있다.   

JTA를 이용해 트랜잭션 매니저를 활용하면 여러 개의 DB나 메시징 서버에 대한 작업을 하나의 트랜잭션으로  
통합하는 분산 트랜잭션 또는 클로벌 트랜잭션이 가능해진다.      
(분산 트랜잭션은 다음에 알아보도록하자.)            

우선 하나 이상의 DB가 참여하는 트랜잭션을 만들려면 JTA를 사용해야 한다는 사실만 기억하자.    




하지만 또 문제가 있다.   
JDBC 로컬 트랜잭션을 JTA를 이용하는 글로벌 트랜잭션으로 바꾸려면 UserService의 코드를 수정해야 한다는 점이다.   
로컬 트랜잭션을 사용하면 충분한 고객을 위해서는 JDBC를 이용한 트랜잭션 관리 코드를 
다중 DB를 위한 글로벌 트랜잭션을 필요로하는 곳을 위해서는 JTA를 이용한 트랜잭션 관리 코드를 적용해야 한다는 문제가 생긴다.   

UserService는 자신의 로직이 바뀌지 않았음에도 기술환경에 따라서 코드가 바뀌는 코드가 돼버리고 말았다.     


<br/>     


### 트랜잭션 API의 의존관계 문제와 해결책    

UserService의 메소드 안에서 트랜잭션 경계설정 코드를 제거할 수는 없다.   
하지만 특정 기술에 의존적인 Connection, UserTransaction, Session/Transaction API 등에   
종속되지 않게 할 수 있는 방법은 없을까?   

트랜잭션의 경계설정을 담당하는 코드는 일정한 패턴을 갖는 유사한 구조다.   
이렇게 여러 기술의 사용 방법에 공통점이 있다면 추상화를 생각해볼 수 있다.   
추상화란 하위 시스템의 공통점을 뽑아내서 분리시키는 것을 말한다.    

그렇게 하면 하위 시스템이 어떤 것인지 알지 못하고 하위 시스템이 바뀌더라도   
일관된 방법으로 접근할 수가 있다.   



<br/>     


### 스프링의 트랜잭션 서비스 추상화       

스프링은 트랜잭션 기술의 공통점을 담은 트랜잭션 추상화 기술을 제공하고 있다.   
이를 이요하면 각 기술의 트랜잭션 API를 이용하지 않고도 일관된 방식으로 트랜잭션을 제어하는 트랜잭션 경계설정 작업이 가능해진다.   

스프링이 제공하는 트랜잭션 경계설정을 위한 추상 인터페이스는 `PlatformTransactioManeger` 이다.   


<br/>    

# 서비스 추상화와 단일 책임 원칙   
 
### 수직, 수평 계층구조와 의존관계   

이렇게 기술과 서비스에 대한 추상화 기법을 이용하면 특정 기술환경에 종속되지 않는 포터블한 코드를 만들 수 있다.   

UserDao와 UserService는 각각 담당하는 코드의 기능적인 관심에 따라 분리되고,   
서로 불필요한 영향을 주지 않으면서 독자적으로 확장이 가능하도록 만든 것이다.   

같은 애플리케이션 로직을 담은 코드지만 내용에 따라 분리했다.   
같은 계층에서 수평적인 분리라고 볼 수 있다.      

<br/>     

# 정리      

- 비즈니스 로직을 담은 코드는 데이터 액세스 로직을 담은 코드와 깔끔하게 분리되는 것이 바람직하다.   
비즈니스 로직 코드 또한 내부적으로 책임과 역할에 따라서 깔끔하게 메소드로 정리돼야 한다.  


- 이를 위해서는 DAO의 기술 변화에 서비스 계층의 코드가 영향을 받지 않도록 인터페이스와   
DI를 잘 활용해서 결합도를 낮춰야 한다.   


- DAO를 사용하는 비즈니스 로직에는 단위 작업을 보장해주는 트랜잭션이 필요하다.   


- 트랜잭션의 시작과 종료를 지정하는 일을 트랜잭션 경계설정이라고 한다.   
트랜잭션 경계설정은 주로 비즈니스 로직 안에서 일어나는 경우가 많다.    


- 시작된 트랜잭션 정보를 담은 오브젝트를 파라미터로 DAO에 전달하는 방법은 매우 비효율적이기 때문에     
스프링이 제공하는 트랜잭션 동기화 기법을 활용하는 것이 편리하다.    


- 자바에서 사용되는 트랜잭션 API의 종류와 방법은 다양하다.   
환경과 서버에 따라서 트랜잭션 방법이 변경되면 경계설정 코드도 함께 변경돼야 한다.   


- 트랜잭션 방법에 따라 비즈니스 로직을 담은 코드가 함께 변경되면 단일 책임 원칙에 위배되며,   
DAO가 사용하는 기술에 대한 강한 결합을 만들어낸다.   


- 트랜잭션 경계설정 코드가 비즈니스 로직 코드에 영향을 주지 않게 하려면   
스프링이 제공하는 트랜잭션 서비스 추상화를 이용하면 된다.     

    
- 서비스 추상화는 로우레벨의 트랜잭션 기술과 API의 변화에 상관없이 일관적인 API를 가진 추상화 계층을 도입한다.   



<br/>    

