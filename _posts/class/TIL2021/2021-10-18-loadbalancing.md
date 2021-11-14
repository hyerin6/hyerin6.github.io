---
layout: post
title: "Nginx 로드 밸런싱 구성"
description: "로드밸런싱이란?"
date: 2021-10-18
tags: [infra]
comments: true
share: true
---
<br />

이전 게시글에서 무중단 배포 환경에 대해 알아보면서

로드 밸런싱에 대해 알게 되었다.

애플리케이션 서버와 사용자 사이에 중계 해줄 리버스 프록시 서버가 필요했고

자연스럽게 트래픽을 분산하여 각 서버가 받는 부하를 분산하는 로드밸런싱도 구성할 수 있게 되었다.

[무중단 배포 환경 이해 포스팅 보러가기](https://hyerin6.github.io/2021-10-17/deploy/)

<br />
<br />

# 로드 밸런싱이란?

로드밸런서는 서버에 가해지는 부하(로드)를 분산(밸런싱)해주는 장치 또는 기술이다.

<br />

### Q. 로드 밸런서가 왜 필요할까?

클라이언트가 한 두명이면 서버가 여유롭게 응답할 수 있지만

수천만명이라면 하나의 서버는 지쳐서 동작을 멈추게 된다.

<br />

이러한 문제를 해결하기 위한 방법은 2가지 이다.

장단점이 있기 때문에 각각의 서비스에 특징과 사용량을 생각해 최적의 방법을 적용하면 된다.

* scale-up : 현재 사용하고 있는 서버 자체의 성능을 증가시켜 처리 능력을 향상시키는 것으로
  cpu, 메모리 업그레이드 등으로 서버의성능을 높이는 방식
* scale-out : 기존 서버와 비슷한 사양의 사양의 여러 대의 서버를 두는 방법

<br />

scale-out의 방식으로 서버를 증설하기로 결정했다면

여러 대의 서버로 트래픽을 균등하게 분산해주는 로드밸런싱이 필요하다.

<br />
<br />

# 로드 밸런싱 기법

다음과 같은 다양한 로드 밸런싱 기법이 있다.

<br />

• 라운드로빈 방식(Round Robin Method)
서버에 들어온 요청을 순서대로 돌아가며 배정하는 방식이다.
클라이언트의 요청을 순서대로 분배하기 때문에 여러 대의 서버가 동일한 스펙을 갖고 있고,
서버와의 연결(세션)이 오래 지속되지 않는 경우에 활용하기 적합하다.

<br />

• 가중 라운드로빈 방식(Weighted Round Robin Method)
각각의 서버마다 가중치를 매기고 가중치가 높은 서버에 클라이언트 요청을 우선적으로 배분한다.
주로 서버의 트래픽 처리 능력이 상이한 경우 사용되는 부하 분산 방식이다.
예를 들어 A라는 서버가 5라는 가중치를 갖고 B라는 서버가 2라는 가중치를 갖는다면,
로드밸런서는 라운드로빈 방식으로 A 서버에 5개 B 서버에 2개의 요청을 전달한다.

<br />

• IP 해시 방식(IP Hash Method)
클라이언트의 IP 주소를 특정 서버로 매핑하여 요청을 처리하는 방식이다.
사용자의 IP를 해싱해(Hashing, 임의의 길이를 지닌 데이터를 고정된 길이의 데이터로 매핑하는 것, 또는 그러한 함수) 로드를 분배하기 때문에 사용자가 항상 동일한 서버로 연결되는 것을 보장한다.

<br />

• 최소 연결 방식(Least Connection Method)
요청이 들어온 시점에 가장 적은 연결상태를 보이는 서버에 우선적으로 트래픽을 배분한다.
자주 세션이 길어지거나, 서버에 분배된 트래픽들이 일정하지 않은 경우에 적합한 방식이다.

<br />

• 최소 리스폰타임(Least Response Time Method)
서버의 현재 연결 상태와 응답시간(Response Time, 서버에 요청을 보내고 최초 응답을 받을 때까지 소요되는 시간)을 모두 고려하여 트래픽을 배분한다.
가장 적은 연결 상태와 가장 짧은 응답시간을 보이는 서버에 우선적으로 로드를 배분하는 방식이다.

<br />
<br />

# Nginx란?

Nginx는 웹 서버, 리버스 프록시, 캐싱, 로드 밸런싱, 미디어 스트리밍 등을 위한 오픈소스 소프트웨어이다.

위에서 계속 언급한 리버스 프록시 서버에 해당한다.

요청을 전달하고 실제 요청에 대한 처리는 뒷단의 웹 애플리케이션 서버들이 처리한다.

<br />

어떻게 운영되는지 알아보기 위해 간단한 예제로 Nginx를 사용해보자.

<br />
<br />

# Nginx 무중단 배포

Nginx와 스프링 부트 3개의 서버로 무중단 배포를 완성해보자.

<br />

<img width="707" alt="스크린샷 2021-10-18 오후 2 37 51" src="https://user-images.githubusercontent.com/33855307/137674794-5e4c3335-c0a7-443d-a50c-3ff73bb516bb.png">

<br />

* Nginx는 80번 포트, 443 포트 할당 
* 스프링 부트 1, 2, 3은 8080 포트 할당  
* Jenkins는 8080 포트 할당     

<br />
<br />

### 1) 젠킨스 설정

우선 원하는 서버를 생성하고 Jenkins의 공개키(`.ssh/id_rsa.pub`)를

스프링 부트 서버 1, 2, 3의 authorized_keys(`.ssh/authorized_keys`)에 붙여넣는다.

authorized_keys의 권한을 `chmod 600 ~/.ssh/authorized_keys` 변경한다.

<br />

> GCP를 사용하면 메타 데이터 메뉴에서 SSH 키를 쉽게 등록할 수 있다.

<br />
<br />

위 설정이 끝나고 `젠킨스 관리` > `시스템 설정` > `Publish over SSH` 항목에서

Test Configuration을 해보면 SUCCESS가 출력될 것이다.

<br />

![ins1](https://user-images.githubusercontent.com/33855307/137674994-2428b81f-e271-4cc0-8947-fba97c3707af.jpeg)
![ins2](https://user-images.githubusercontent.com/33855307/137674986-ccf45041-60dd-4fa0-b18c-a37b152bdcac.jpeg)

<br />

* Name: 각 인스턴스 구분이 가능하게 지정 (Job에서 표시될 이름)
* Hostname: 내부 IP
* Username: ssh 접근 계정 이름, 중복되어도 상관 없음 원하느대로 지정
* Remote Directory: Jenkins 시스템 설정에서 SSH 설정시 지정한 홈 디렉토리 뒤에 추가로 입력하는 디렉토리 경로

<br />
<br />

git에서 프로젝트를 받아오는 jenkins 설정은 아래 게시글에서 확인할 수 있다.

[https://hyerin6.github.io/2020-10-21/jenkins-ci/](https://hyerin6.github.io/2020-10-21/jenkins-ci/)

<br />



컨트롤러만 있는 스프링 부트 프로젝트이기 때문에 

배포 스크립트는 다음과 같이 간단하게 작성했다. 

<br />


<img width="982" alt="스크린샷 2021-10-18 오후 2 46 25" src="https://user-images.githubusercontent.com/33855307/137733391-914b6c0e-a07f-4cfe-aa74-05c190ae3a82.png">
<img width="979" alt="스크린샷 2021-10-18 오후 2 45 50" src="https://user-images.githubusercontent.com/33855307/137733399-06a45e91-f042-459c-8ce2-67e0ed7aa1d5.png">


<br />
<br />
<br />

### 2) Nginx 설치 

#### Docker 컨테이너를 실행시킬 준비

```shell
sudo yum install docker
sudo systemctl start docker
sudo chmod 666 /var/run/docker.sock
```


<br />


#### Nginx 설치

```shell
sudo yum install nginx
sudo systemctl start nginx
```

<br />

#### 로드밸런싱 설정

```shell
sudo vi /etc/nginx/nginx.conf
```

위 경로의 파일로 들어가 다음과 같은 내용을 추가해야 한다. 

```shell
upstream cpu-bound-app {
  server {instance_1번의_ip}:8080 weight=100 max_fails=3 fail_timeout=3s;
  server {instance_2번의_ip}:8080 weight=100 max_fails=3 fail_timeout=3s;
  server {instance_3번의_ip}:8080 weight=100 max_fails=3 fail_timeout=3s;
}

location / {
  proxy_pass http://cpu-bound-app;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection 'upgrade';
  proxy_set_header Host $host;
  proxy_cache_bypass $http_upgrade;
}
```

* proxy_pass : 요청이 오면 `http://cpu-bound-app`로 전달
* proxy_set_header XXX : 실제 요청 데이터를 header의 각 항목에 할당
    - ex) proxy_set_header X-Real-IP $remote_addr: Request Header의 X-Real-IP에 요청자의 IP를 저장

<br /> 


#### 설정 파일 적용 

```shell
sudo systemctl reload nginx
```

<br /> 

여기까지 설정하면 nginx는 에러 로그를 남긴다. 


이유는 SELinux (Security Enhanced Linux)가 함께 작동하는데, 

이 SELinux가 HTTP 프록시를 차단하고 있는 것이 문제이다. 

<br />

#### Nginx 에러 로그 확인 

```shell
sudo tail -f /var/log/nginx/error.log
```

<br />


#### HTTP 프록시를 차단 해결 

HTTP 프록시를 허용해주는 커맨드를 실행하면 위 에러를 해결할 수 있다. 

```shell
sudo setsebool -P httpd_can_network_connect on
```

<br />


여기서 -P는 persist의 의미로, 재부팅 후에도 환경설정이 그대로 적용된다는 말이다. 

이렇게 함으로써 정상적으로 도메인 접속 후 포트포워딩이 문제 없이 이루어 지는 것을 확인할 수 있었다.


<br />
<br />

# Nginx 흐름


![nnx](https://user-images.githubusercontent.com/33855307/141668538-e175bdc4-93d0-4a43-9b8c-d7a4b11bda1c.png)
 
서버를 늘리지 않고 단일 서버로 두고 nginx로 요청을 받았을 뿐인데 
 
postman으로 확인해보니 빨라진 응답 결과를 확인할 수 있었다. 

<br />

Nginx는 Event-Driven 구조로 동작하기 때문에 한 개 또는 고정된 프로세스만 생성하여 사용하고, 

비동기 방식으로 요청들을 동시에 처리되는 것처럼 처리할 수 있다. 

Nginx는 새로운 요청이 들어오더라도 새로운 프로세스와 스레드를 생성하지 않기 때문에 

프로세스와 스레드 생성 비용이 존재하지 않고, 적은 자원으로도 효율적인 운용이 가능하다. 

그래서 단일 서버에서도 동시에 많은 연결을 처리할 수 있었다. 

<br />
<br />


### 참고

* [https://m.post.naver.com/viewer/postView.nhn?volumeNo=27046347&memberNo=2521903](https://m.post.naver.com/viewer/postView.nhn?volumeNo=27046347&memberNo=2521903)
* <https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/>
* <https://jojoldu.tistory.com/267>


<br />
