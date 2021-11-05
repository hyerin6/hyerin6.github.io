---  
layout: post  
title: "artillery 사용해서 부하 테스트하기"   
description: "artillery 간단 소개, 스트레스 테스트"  
date: 2021-11-08  
tags: [spring, artillery]
comments: true  
share: true
---  

<br />

## artillery란?  
[artillery](https://artillery.io/)는 node.js로 작성된 스트레스 테스트 도구이다. 

많은 개발자들이 ngrinder를 사용하는걸 봤는데 내가 필요한 학습 목적으로는 artillery도 적합했다. 

스크립트는 yaml로 작성하고 사용법이 간단했다.  

결과를 html로 저장할 수 있어 편하게 테스트 결과들을 비교할 수 있는 것도 좋았다. 

[Document](https://artillery.io/docs/guides/overview/welcome.html) 에 테스트 스크립트 작성법도 상세하게 나와있어서 참고하기 좋았다. 

<br />   

특징은 다음과 같다. 

* HTTP(S), Socket.io, Websocket 등 다양한 프로토콜을 지원한다.
* 시나리오 테스트를 할 수 있다.
* JavaScript로 로직을 작성해서 추가할 수 있다.
* statsd를 지원해서 Datadog이나 InfluxDB 등에 실시간으로 결과를 등록할 수 있다.


<br />   





