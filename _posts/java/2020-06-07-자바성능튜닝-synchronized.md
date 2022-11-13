---
layout: post
title: "제대로 알고 써야하는 synchronized"  
description: "자바 성능 튜닝 이야기 story 8"
date: 2020-06-07
tags: [java]
comments: true
share: true
published: true
categories: [Java]
---

<br />

# 자바에서 스레드는 어떻게 사용하나?      
WAS는 여러 개의 스레드가 동작하도록 되어 있다. synchronized를 자주 사용한다.             
synchronized를 쓴다고 무조건 안정적인 것은 아니며 성능에 영향을 미치는 부분도 있다.                

<br/>       

## 프로세스와 스레드    
클래스를 하나 수행시키거나 WAS를 기동하면 서버에 자바 프로세스가 하나 생성된다.      
하나의 프로세스에는 여러 개의 스레드가 생성된다.      
단일 스레드가 생성되어 종료될 수도 있고, 여러 개의 스레드가 생성되어 수행될 수도 있다.      
프로세스와 스레드의 관계는 1:N 관계라고 보면 된다.    


스레드는 가벼운 프로세스라고도 하며, 프로세스에서 만들어 사용하고 있는 메모리를 공유한다.          
그래서 별개의 프로세스가 하나씩 뜨는 것보다는 성능이나 자원 사용에 있어서 많은 도움이 된다.       


<br/>       


## Synchronized를 이해하자.    
웹 기반의 시스템에서 스레드 관련 부분 중 가장 많이 사용하는 것은 synchronized일 것이다.     
synchronized는 동시에 처리한다는 의미로 하나의 객체에 여러 객체가 동시에 접근하여 처리하는 상황에 사용한다.     
하나의 객체에 여러 요청이 동시에 들어오면 한 명씩 들어오라고 해당 메서드나 블록에서 제어하게 된다.        

synchronized와 static을 연결해서 생각하면 더욱 복잡해진다.     
synchronized는 절대로 생성자의 식별자로는 사용할 수 없다는 점을 기억하자.      



```java  
public synchronized void sampleMethod() {
    // 생략
}

private Object obj = new Object();
public void sampleBlock() {
    synchronized(obj) {
        // 생략
    }
}
```

간단하게 synchronized라는 식별자로 동기화할 수 있다.           
그럼 언제 동기화를 사용해야 할까?         

(1) 하나의 객체를 여러 스레드에서 동시에 사용할 경우              
(2) static으로 선언한 객체를 여러 스레드에서 동시에 사용할 경우        

위의 경우가 아니면 동기화를 할 필요가 없다.        


<br />        
<br />            

## 동기화 사용법: (1) 동일 객체 접근 시              
예를들어, 여러 기부자가 어떤 기부금을 처리하는 단체에 기부금을 낸다고 가정하자.      

* 총 기부금 : amount  
* 기부자 : Contributor   
* 기부단체 : Contribution   
* 기부금 받는 창구 : donate()       

<br />      

```
public class Contribution {
    private int amount = 0;
    
    public void donate() {
        amount++;
    }
    
    public int getTotal() {
        return amount;
    }
}

public class Contributor extends Thread {
    private Contribution myContribution;
    private String myName;
    
    . . .
    
}
```

<br />      

### 동기화하기 전 기부단체 10개 (1000원씩 출력)       
1인당 1원씩 1,000번 기부하고 기부가 완료되면 전체 기부금을 프린트한다.    

```
public static void main(String[] args) {
    Contributor[] cts = new Contributor[10];
    
    // 기부자, 단체 초기화 
    for(int i = 0; i < 10; ++i) {
        Contribution group = new Contribution();
        crs[i] = new Contributor(group, "Contributor " + i);
    }
    
    // 기부 실행 
    for(int i = 0; i < 10; ++i) {
        crs[i].start();
    }
}
```

위 코드에서 기부금을 받는 단체인 group 객체를 매번 생성했기 때문에       
10명의 객체가 각기 다른 단체에 기부하게 된다.       

<br />      

### 동기화하기 전 기부 단체 1개 (10,000원 출력 X)       
기부 단체가 하나인 경우 어떻게 될까?       

```
Contribution group = new Contribution();

for(int i = 0; i < 10; ++i) {
    crs[i] = new Contributor(group, "Contributor " + i);
}
```

총 기부금 금액은 10,000원이 출력되어야 하는데 예상대로 출력되지 않는다.    


<br />      


### 예상대로 출력되지 않는 이유는?    
예상대로라면 1000원씩 기부했기 때문에 총 10,000원이 프린트되어야 하는데    
대부분 10,000원이 프린트되지 않는다.    

그 이유는 10개의 Contributor 객체에서 하나의 Contribution 객체의 `donate()` 메서드를     
동시에 접근할 수 있도록 되어 있기 때문이다.           
이 오류를 수정하기 위해 `donate()` 메서드에 synchronized를 써서 동기화 식별자를 추가해야 한다.     

위 예제들을 성능 테스트해보면 필요 없는 부분에 synchronized를 사용하면 약간이지만 성능에 영향을 준다.   


<br />        
<br />                 

## 동기화 사용법: (2) static 사용 시           
앞 예제에서 amount를 static으로 선언하고 synchronized를 사용하면 어떻게 될까?      
<br />   

### 동기화 전: 기부 단체 10개에 기부하는 경우 (synchronized 사용 X)    
총 10,000원이 출력되어야 하는데 원하는 결과가 안나온다.    
각 단체에 기부하는 케이스라 하더라도 amount를 static으로 선언하면   
각 기부 단체에 따로 기부하는 것은 불가능하다.         

<br />   

### `donate()` 메서드 동기화: 기부 단체 10개에 기부하는 경우 (synchronized 사용 O)    
이번에도 원하는 결과가 나오지 않는다.    
synchronized는 각각의 객체에 대한 동기화를 하는 것이기 때문에    
각각의 단체에 대한 동기화는 되었지만 amount에 대한 동기화는 되지 않는다.      

<br />     

### `donate()` 메서드 동기화, static 추가         
amount는 클래스의 변수이지 객체의 변수가 아니다.     

```
public static synchronized void donate() {
    amount++;
}
```

이제 원하는 대로 결과가 나왔다.      

<br />   


항상 변하는 값에 대해서 static으로 선언하여 사용하면 위험하다.    
synchronized도 꼭 필요할 때만 사용해야 한다.       



<br/>       

## 동기화를 위해서 자바에서 제공하는 것들    

JDK 5.0부터 추가된 `java.util.concurrent` 패키지에 대해서 간단히 알아보자.     
이 패키지에 주요 개념 4가지가 포함되어 있다.

(1) Lock : 실행 중인 스레드를 간단한 방법으로 정지시켰다가 실행시킨다. 상호참조로 인해 발생하는 데드락을 피할 수 있다.

(2) Execute : 스레드를 더 효율적으로 관리할 수 있는 클래스들을 제공한다.

(3) Concurrent 콜렉션 : 앞서 살펴본 콜렉션의 클래스들을 제공한다.

(4) Atomic 변수 : 동기화가 되어 있는 변수를 제공한다. 이 변수를 사용하면, synchronized 식별자를 메서드에 지정할 필요가 없이 사용할 수 있다.   



<br/>     


## 정리      
동일한 객체를 공유하거나, static을 사용한 변수를 공유할 경우 반드시 synchronized를 사용해야 한다.     
synchronized는 여러 스레드에서 접근하는 것을 막아주는 장점이 있지만,   
성능 저하가 발생한다는 단점이 있다.   

<br/>       
