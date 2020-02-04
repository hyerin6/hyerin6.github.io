---
layout: post
title: "Redis 사용해보자!"  
description: "Spring boot Redis Cache"
date: 2020-02-02
tags: [spring, redis, nexters16]
comments: true
share: true
---

#### 1. Redis란?         
in-memory 기반의 data structure 저장 기술로 데이터베이스 서버, 데이터 캐싱 등이 가능한 시스템이다.   

**특징**  
- 오픈소스 소프트웨어   
- 디스크가 아닌 메모리 기반의 데이터 저장소   
- NoSQL&Cache 솔루션이며, 메모리 기반으로 구성된다.   
- 명시적으로 삭제, expire를 설정하지 않으면 데이터는 삭제되지 않는다.   
- 여러개의 서버 구성이 가능하다.   
- 데이터베이스로 사용할 수 있으며 Cache로도 사용될 수 있는 기술이다.   

**사용 가능한 데이터형**  
- String   
- List  
- Set  
- Sorted set  
- Hash   

**장점**  
- 리스트, 배열곽 같은 데이터 처리에 유용   
- 메모리를 사용하면서 영속적인 데이터 보존이 가능   
- Redis server는 1개의 싱글 스레드로 수행되기 떄문에 서버 하나에 여러 서버를 띄울 수 있음   

#### 2. Redis 설치 및 spring boot 에서 사용       
- docker 이용해서 설치 및 접속      

```
# 최신 이미지 가져오기, 레디스 서버 실행 
docker pull redis 
docker run --name redis -d -p 6379:6379 redis

# Docker의 redis-cli로 접속  
docker run -it --link redis:redis --rm redis redis-cli -h redis -p 6379 
# Redis-cli로 직접 접속하기: 연결된 6379 포트를 사용
redis-cli -p 6379
# Shell로 Docker 리눅스에 접속 
docker exec -it redis /bin/bash
```

- 의존성 추가     

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

- application.yml 설정 정보 추가     

```yaml
redis:
    host: 
    port: 
    passwd:
```

    
#### 3. Nexters16 프로젝트에서 Redis 사용하기      
회원끼리 편지를 주고 받는 기능을 위해 우리는 회원가입 시 사용자에게 전화번호를 받고 인증까지 진행하기로 했다.      
전화번호 인증을 하려면 인증 코드도 저장해야 하고 인증 결과도 저장해야 하는데   
redis를 이용해서 몇가지 데이터를 key-value 형태로 저장하는게 좋겠다는 회의 결과가 나왔다!   

**프로젝트에서 redis 사용**   
Java의 redis client는 크게 jedis와 lettuce 2가지가 있는데, 우리는 lettuce를 사용하고 있다.   
spring boot 2.0에서 lettuce가 기본 클라이언트가 되서 사용해보기 시작했는데 
어떤 점에서 lettuce가 더 좋을지 알아보자!   


- Jedis   
여러 스레드에서 Jedis 인스턴스를 공유하려 할 떄 jedis는 스레드에 안전하지 않다. 따라서 멀티 스레드 환경에서 고려할 상황이 생긴다.   
안전한 방법은 pooling(Thread-pool)과 같은 jedis-pool을 사용하는 것이지만 물리적인 비용의 증가가 따른다.    
(connection할 인스턴스를 미리 만들어놓고 대기하는 연결비용의 증가)   

  >Spring Boot에서는 기본 의존성인 lettuce를 제거하고 Jedis를 등록해야 한다.            

- Lettuce  
Lettuce는 Netty(비동기 이벤트 기반 고성능 네트워크 프레임워크) 기반의 레디스 클라이언트이다. Thread-safe!    
비동기로 요청을 처리하기 때문에 고성능을 자랑한다.     
Jedis에 비해 몇배 이상의 성능과 하드웨어 자원 절약이 가능하다.     

connection 인스턴스의 공유라는 점에서 Thread-safe인 lettuce를 사용해야 겠다는 생각이 들지만     
Single-Thread의 레디스에 데이터에 접근할 때? 혹은 다른 관점에서는 어떨지 더 알아보면 좋을 것 같다.    

