---
title: "Gradle vs. Maven"  
categories:
  - Spring
tags:
  - spring 
toc: true
author_profile: true
toc_sticky: true
--- 


<br />    

예전에 안드로이드 실습과 디프만 워밍업 프로젝트에서 gradle을 사용해봤는데      
우선 왜 gradle이 선택되었는지 알아보자.     

<br />      
  
## 빌드 도구 종류: Gradle vs Maven    
maven과 gradle은 빌드 관리 도구이다.    
빌드 관리 도구란 빌드 자동화를 수행해 실행 가능한 프로그램으로 바꿔주는 도구이다.   
즉 코드를 컴파일해서 binary code로 만들고 패키징, 테스트하여 실행 가능한   
프로그램이 나오기 까지의 과정(빌드)을 자동화하는 것이다.    


- Performance 측면에서 gradle이 maven보다 빠른 성능을 보여준다.
- <https://gradle.org/maven-vs-gradle/>         
   

<br />      
<br />      

## Maven    
* 자바용 프로젝트 관리 도구    
* `pom.xml` 을 이용한다.   
* 사용할 라이브러리 뿐만 아니라 해당 라이브러리가 작동하는데 필요한 다른 라이브러리들까지 네트워크를 통해 자동으로 다운로드한다.   
* 정해진 라이프사이클에 의하여 작업을 수행하며, 프로젝트 관리 기능도 포함하고 있다.      


<br />      
<br />      



## Gradle     
* 오픈소스 기반의 build 자동화 시스템       
* JVM 기반의 빌드 도구로 기존의 Ant와 Maven을 보완    
* 설정 주입 방식 (Configuration Injection)    
* Groovy 문법 사용

<br />             
<br />

## Gradle 사용을 고려해야할 이유    
#### (1) 속도가 빠르다.    
<br />      

#### (2) Build라는 동적인 요소를 XML로 정의하기에는 어려운 부분이 많다.    
* 설정 내용이 길어지고 가독성이 떨어진다.   
* 의존 관계가 복잡한 프로젝트 설정하기에 부적절하다.   
* 상속 구조를 이용한 멀티 모듈 구현    
* 특정 설정을 소수의 모듈에서 공유하기 위해서는 부모 프로젝트를 생성하여 상속하게 해야한다.   

<br />     

#### (3) Gradle은 Groovy를 사용하기 때문에 동적인 빌드는 Groovy 스크립트로 플러그인을 호출하거나 직접 코드를 작성하면 된다.   
* 설정 주입 방식을 사용해서 공통 모듈을 상속해서 사용하는 단점을 커버한다.   
* 설정 주입 시 프로젝트의 조건을 체크할 수 있어서 프로젝트별로 주입되는 설정을 다르게 할 수 있다.   

<br />     

#### (4) 성능   
* Incrementality: gradle은 가능한 경우 변경된 파일만 작업해 중복을 피한다.      
* build cache: 동일한 입력에 대해서 gradle 빌드를 재사용한다.      
* gradle 데몬: 빌드 정보를 메모리에 유지하는 프로세스를 구동한다.     
* Performance 측면에서 gradle이 maven보다 빠른 성능을 보여준다.
* <https://gradle.org/maven-vs-gradle/>  

<br />             
<br />

**참고**
- <https://docs.gradle.org/current/userguide/java_library_plugin.html>
- <https://tomgregory.com/how-to-use-gradle-api-vs-implementation-dependencies-with-the-java-library-plugin/>
- <https://medium.com/mindorks/implementation-vs-api-in-gradle-3-0-494c817a6fa>
- <https://docs.gradle.org/4.6/release-notes.html>
- <https://blog.gradle.org/incremental-compiler-avoidance#about-annotation-processors>             

