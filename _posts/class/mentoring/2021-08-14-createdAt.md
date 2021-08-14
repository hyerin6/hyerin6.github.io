---
layout: post    
title: "createdAt 어떻게 저장할까?"      
description: "@CreationTimestamp vs. @CreatedDate"      
date: 2021-08-14    
tags: [spring, jpa]     
categories: [Java, Spring, JPA]      
comments: true     
share: true
---   

<br />      


아래 코드는 sns 프로젝트의 게시글 엔티티 클래스이다.     
많은 어노테이션이 붙어있는데 이번에는 엔티티 생성, 수정 시간을 기록하는    
필드에 대해 알아보자.    
<br />      


```
@Setter
@Getter
@Builder
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor(access = AccessLevel.PRIVATE)
@EntityListeners(AuditingEntityListener.class)
@Entity
public class Post {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private long id;

	private String content;

	@ManyToOne
	@JoinColumn(name = "user_id")
	private User user;

	@CreatedDate
	@Column(updatable = false, nullable = false)
	private LocalDateTime createdAt;

	@LastModifiedDate
	private LocalDateTime updatedAt;
}
```


<br />        
<br />       
<br />        



## `@CreatedDate` & `@LastModifiedDate`              

`@CreatedDate`는 엔티티 생성 시 특정 필드를 자동으로 데이터베이스에 매핑해주기 위해 사용한다.       
그런데 아무런 설정 없이 `@CreatedDate` 어노테이션만 붙이면 다음과 같은 에러가 발생하거나 NULL이 저장된다.       

```
not-null property references a null or transient value createddate
```

<br />     


다음과 같은 설정이 필요하다.   

* `SpringApplication`에 `@EnableJpaAuditing` 붙이기       
* 콜백 요청을 원하는 엔티티에 `@EntityListeners(AuditingEntityListener.class)` 붙이기

왜 이런 설정이 필요한지 더 알아보자.  



<br />        
<br />     
<br />        



## `@EntityListeners`        
엔티티를 DB에 적용하기 이전에 콜백을 요청할 수 있는 어노테이션   

<br />     
<br />       


## AuditingEntityListener   
* Auditing이란 감시하다라는 뜻이다.      
  <br />    
  
* 엔티티를 영속성 컨텍스트에 저장하거나 수정한 후 `update`하는 경우       
자동으로 auditor, time을 매핑하여 DB에 넣도록 도와준다.      
  <br />   
  
* 스프링 부트의 엔트리 포인트인 실행 클래스에 `@EnableJpaAuditing`을 적용하여 JPA Auditing을 활성화 해야한다.     
<br />     


위 설정들을 마치면 엔티티에 따로 `createdAt`과 `updatedAt`을 set 하지 않아도 자동으로 DB에 저장된다.        


<br />        
<br />     
<br />    



## `@CreationTimestamp`
앞서 알아본 `@CreatedDate`는 Spring Data에서 제공하는 어노테이션이고    
`@CreationTimestamp`는 하이버네이트에서 제공하는 어노테이션이다.   

설정 없이 사용할 수 있지만 하이버네이트에 종속되어 버리니      
`@CreatedDate`를 사용하기로 했다.       








<br />        
<br />     
<br />  


## 참고 
* <https://docs.spring.io/spring-data/jpa/docs/1.7.0.DATAJPA-580-SNAPSHOT/reference/html/auditing.html>    
* <https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#auditing.annotations>
* <https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#mapping-generated-CreationTimestamp>     
* <https://bgpark.tistory.com/133>    
* <https://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#events-jpa-callbacks>     



<br />    
