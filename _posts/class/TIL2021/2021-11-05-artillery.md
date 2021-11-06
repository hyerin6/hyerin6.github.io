---  
layout: post  
title: "artillery란?"   
description: "스트레스 테스트 툴로 성능 측정(1)"  
date: 2021-11-05 
tags: [artillery]
comments: true  
share: true
---  

<br />

## artillery 란?  
[artillery](https://artillery.io/)는 node.js로 작성된 스트레스 테스트 도구이다. 

가벼운 테스트부터 시나리오 테스트까지 가능하고 리포트 페이지를 제공한다. 

artillery는 스크립트를 yaml로 작성할 수 있고 원한다면 node.js로도 작성할 수 있다. 

[Document](https://artillery.io/docs/guides/overview/welcome.html) 에 테스트 스크립트 작성법도 상세하게 나와있어서 참고하기 좋았다. 

<br />   

테스트 프로그램은 다음 기준으로 선택하면 된다. 

* 스트레스 테스트로 수집되는 지표 중 나에게 필요한 지표가 있는가?
* 테스트 스크립트로 내가 원하는 시나리오대로 테스트할 수 있는가?

artillery는 위 기준에 부합하는 툴이기 때문에 사용했다. 

<br />   

특징은 다음과 같다. 

* HTTP(S), Socket.io, Websocket 등 다양한 프로토콜을 지원한다.
* 시나리오 테스트를 할 수 있다.
* JavaScript로 로직을 작성해서 추가할 수 있다.
* statsd를 지원해서 Datadog이나 InfluxDB 등에 실시간으로 결과를 등록할 수 있다.


<br />   
<br />   

## artillery 사용법 
#### 1) Node.js 설치 
<https://nodejs.org/ko/>


<br />

#### 2) 스크립트 작성 
`yaml`로 작성한다. 

다음 공식 문서에 스크립트 작성법이 자세하게 나와있기 때문에 기본적인 설정부터 

시나리오 설정까지 따라할 수 있다. 

`yaml`로 작성했기 때문에 가끔 space를 tap으로 인식할 수 있는데 이 경우에는 yaml editor을 사용하면 해결된다. 

첫 테스트를 간단히 진행해보고 싶다면 아래 링크의 공식 문서, 스크립트를 참고하면 된다.  

<https://artillery.io/docs/guides/getting-started/writing-your-first-test.html#Load-Phases>

```
config:
  target: "https://example.com/api"
  phases:
    - duration: 60
      arrivalRate: 5
      name: Warm up
    - duration: 120
      arrivalRate: 5
      rampTo: 50
      name: Ramp up load
    - duration: 600
      arrivalRate: 50
      name: Sustained load
```

<br />

#### 3) 테스트
```
artillery run --output report.json test-script.yaml
```

report.json 파일에 결과가 저장된다.

<br />

#### 4) 테스트 결과 확인 및 저장 

```
artillery report report.json  
```

위 명령어를 통해 html 파일을 만들고 볼 수 있다.


<br />
<br />

## 결과
<img width="1792" alt="스크린샷 2021-11-06 오전 11 43 00" src="https://user-images.githubusercontent.com/33855307/140595283-482c962b-5f76-4ca7-8549-171a32fa328f.png">

결과는 위와 같은 화면으로 확인할 수 있다. 

결과를 보는 법과 테스트 진행에 자세한 개념은 다음 게시물에서 확인할 수 있다. 

<https://hyerin6.github.io/2021-11-06/stress-test/>


<br />
