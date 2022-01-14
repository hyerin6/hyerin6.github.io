---
layout: post  
title: "Spring Boot Redis Sub/Pub "  
description: "Messaging Pub/Sub VS. Messaging Queuing"  
date: 2022-01-14  
tags: [spring, redis]  
categories: [Spring, Redis]  
comments: true  
share: true  
published: true
---
<br />

## Publish/Subscribe 아키텍처

많이 사용하는 HTTP Request/Response 방식은 Client-Server 사이에 강한 의존성이 생기며

서버가 실행 중일 때만 데이터를 전달받을 수 있다. (동기식 통신 방식)

HTTP 통신과 반대로 느슨하게 결합된 비동기 시스템 통합 방식 중 대표 기술이 `메시징` 이다.

메시징은 중간 시스템을 통해서 전송하기 때문에 발신자는 수신자에 대해서 전혀 알지 못한다.

<br />

## Messaging Pub/Sub VS. Messaging Queuing

메시징이라는 용어는 매우 포괄적인 용어이다.

메시징을 구현하는 방법도 다양한데, 그 중 Pub/Sub와 메시지 큐잉에 대해서 알아보자.

<br />

#### Messaging Queuing

메시지 큐잉은 point-to-point channel 방식으로 오직 한 수신자만 메시지를 수신(소비) 한다.

> 순차적으로 처리하기 때문에 메시지가 채널에 쌓이게 되면 소비할 수 있는 client를 늘려서 병목현상을 해결할 수 있다.
>
> 그런데 client를 늘리며 이벤트에 대한 순서를 보장받을 수 없다.
>
> 이벤트 순서를 보장하기 위해 별도의 작업이 또 필요하다.
>
> 예) kafka 브로커의 파티션 지정 메시지 전송

<br />

#### Messaging Pub/Sub

메시지 큐잉과 반대로 Pub/Sub는 수신자(client) 모두에게 동일한 메시지를 전송한다.

<br />

## Redis Pub/Sub

Redis Pub/Sub는 매우 심플한 기능을 제공하는데 메시지를 별도로 저장하지 않기 때문에

메시지 전송 후 해당 메시지가 삭제되어 전송 신뢰성을 보장하지는 않는다.

실시간 데이터 처리에 매우 적합한데, 이를 개발자가 인지하고 있어야 한다.

<br />

## redis-cli로 기능 확인해보기

#### `subcribe channel` 
"ch01" 이라는 이름의 채널에 메시지를 수신  

```shell
6379> subscribe ch01
1) "subscribe"
2) "ch01"
3) (integer) 1
```

<br />

#### `publish channel "message"`  
"ch01" 이라는 채널에 메시지 발행  

```shell 
6379> publish ch01 "test message"
(integer 2)
```

여기서 2는 2개의 수신자에서 메시지를 받았다는 것을 의미한다.   

```shell
6379> subscribe ch01
1) "subscribe"
2) "ch01"
3) (integer) 1
1) "message"
2) "ch01"
3) "test message"
```


<br />

#### `pubsub numsub channel`
pubsub 명령으로 해당 채널 커넥션 연동 중인 수신자 개수 확인 

```shell 
6379> pubsub numsub ch01
1) "ch01"
2) (integer) 2
```


<br />

## Spring Boot Redis Sub/Pub 





### 참고 
* <https://redis.io/topics/pubsub>  
* <https://www.baeldung.com/spring-data-redis-pub-sub>  
* <https://brunch.co.kr/@springboot/374>  

<br />
