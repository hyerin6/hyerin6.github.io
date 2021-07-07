---
layout: post
title: "클래스와 메서드의 정보를 확인할 수 있는 API"  
description: "자바 성능 튜닝 이야기 story 7"
date: 2020-06-07
tags: [java]
comments: true
share: true
published: true
categories: [Java]
---

# 클래스 정보, 어떻게 알아낼 수 있나?
자바에는 클래스와 메서드의 정보를 확인할 수 있는 API가 있다.     
<br/>          


### reflection 관련 클래스들   
자바 API에는 reflection 이라는 패키지가 있다.    
이 패키지에 있는 클래스들을 사용하면 JVM에 로딩되어 있는 클래스와 메서드 정보를 읽어 올 수 있다.

<br/>    

### Class 클래스   
Class 클래스는 클래스에 대한 정보를 얻을 때 사용하기 좋고, 생성자는 따로 없다.   
ClassLoader 클래스의 defineClass() 메서드를 이용해서 클래스 객체를 만들 수도 있지만, 좋은 방법은 아니다.     
그보다는 Object 클래스에 있는 getClass() 메서드를 사용하는 것이 일반적이다.

<br/>    

### Method 클래스   
Method 클래스를 사용하면 메서드에 대한 정보를 얻을 수 있다.   
하지만 Method 클래스에는 생성자가 없으므로 Method 클래스의 정보를 얻기 위해서   
Class 클래스의 getMethod() 메서드를 사용하거나 getDeclaredMethod() 메서드를 써야 한다.

<br/>    

### Field 클래스    
Field 클래스는 클래스에 있는 변수들의 정보를 제공하기 위해서 사용한다.    
Method 클래스와 마찬가지로 생성자가 존재하지 않으므로 Class 클래스와 함께 사용해야 한다.

<br/>    

### reflection 클래스를 잘못 사용한 사례    
일반적으로 로그를 프린트할 때 클래스 이름을 알아내기 위해 다음과 같은 Class 클래스를 많이 사용한다.


```java      
this.getClass.getName()       
```      


이 방법은 getClass() 메서드를 호출할 때 Class 객체를 만들고,           
그 객체의 이름을 가져오는 메서드를 수행하는 시간과 메모리를 사용할 뿐이다.

응답 속도에 그리 많은 영향을 주지는 않지만, 많이 사용하면 필요 없는 시간을 낭비하게 된다.

<br/>   

### 정리    

reflection 관련 클래스를 사용하면 클래스의 정보 및 여러 가지 세부 정보를 알 수 있어 매우 편리하다.   
하지만 로그에서 사용하기 위해서라면, instanceof를 사용하는 것이 클래스의 이름으로 해당 객체의 타입을 비교하는 방법보다 낫다.

추가적으로 클래스의 메타 데이터 정보는 JVM의 Perm 영역에 저장된다.            
만약 Class 클래스를 사용하여 엄청나게 많은 클래스를 동적으로 생산하는 일이 벌어지면          
Perm 영역이 더 이상 사용할 수 없게 되어 OutOfMemoryError가 발생할 수도 있어 조심해야 한다.        
