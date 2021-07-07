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


## synchronized는 제대로 알고 써야 한다.

**Q. 자바에서 스레드는 어떻게 사용하나?**

<br/>       

#### - 프로세스와 스레드

하나의 프로세스에는 한 개 ~ 여러 개의 스레드가 생성된다.   
그러므로 프로세스와 스레드의 관계는 일대다 관계라고 보면 된다.     
스레드는 다른 말로 Lightweight Process(LWP) 라고도 한다.   
즉, 가벼운 프로세스이고 프로세스에서 만들어 사용하고 있는 메모리를 공유한다.     
그래서 별개의 프로세스가 하나씩 뜨는 것보다는 성능이나 자원 사용에 있어서 많은 도움이 된다.

<br/>       

#### - Thread 클래스 상속과 Runnable 인터페이스 구현

스레드의 구현은 Thread 클래스를 상속받는 방법과 Runnable 인터페이스를 구현하는 방법 두 가지가 있다.   
Thread 클래스는 Runnable 인터페이스를 구현한 것이기 때문에 어느 것을 사용해도 거의 차이가 없다.   
대신 Runnable 인터페이스 구현하면 원하는 기능을 추가할 수 있다. 하지만 해당 클래스를 수행할 때 별도의 스레드 객체를 생성해야 한다.   
또한, 자바는 다중 상속을 인정하지 않기 때문에 스레드를 사용해야 할 때 이미 상속받은 클래스가 존재한다면 Runnable 인터페이스를 구현해야 한다.

Runnable 인터페이스를 구현한 경우,   
Thread 클래스의 Runnable 인터페이스를 매개변수로 받는 생성자를 사용해서 Thread 클래스를 만든 후 start() 메서드를 호출해야 한다.   
그렇지 않고 그냥 run() 메서드를 호출하면 새로운 스레드가 생성되지 않는다.

```java     
RunnableImpl ri = new RunnableImpl();  
ThreadExtends te = new ThreadExtends();  

new Thread(ri).start();  
te.start();   
```       

<br/>       

#### - sleep(), wait(), join() 메서드

현재 진행 중인 스레드를 대기하도록 하기 위해서는 sleep(), wait(), join() 세 가지 메서드를 사용하는 방법이 있다.

(1) wait() : 모든 클래스의 부모 클래스인 Object 클래스에 선언되어 있어서 어떤 클래스에서도 사용할 수 있다.

(2) sleep() : 명시된 시간만큼 해당 스레드를 대기시킨다. 매개변수를 지정해서 사용한다.

아래 두 메서드는 static 메서드이기 떄문에 반드시 스레드 객체를 통하지 않아도 사용할 수 있다.
- sleep(long millis)
- sleep(long millis, int nanos)

(3) join() : 명시된 시간만큼 해당 스레드가 죽기를 기다린다. 만약 아무런 매개변수를 지정하지 않으면 죽을 때까지 계속 대기한다.

wait() 메서드도 명시된 시간만큼 해당 스레드를 대기시킬 수 있는데 sleep() 메서드와 다른 점은 매개변수이다.   
만약 아무런 매개변수를 지정하지 않으면 notify() 메서드 혹은 notifyAll() 메서드가 호풀될 때까지 대기한다.   
wait() 메서드가 대기하는 시간 설정 방법은 sleep() 메서드와 동일하다.

이 세 가지 메서드는 모두 예외를 던지도록 되어 있어 사용할 때 반드시 예외 처리를 해주어야 한다.

<br/>       


#### - interrupt(), notify(), notifyAll() 메서드

앞서 명시한 세 개의 메서드를 '모두' 멈출 수 있는 유일한 메서드는 interrupt() 메서드다.   
interrupt() 메서드가 호출되면 중지된 스레드에는 InterruptedException이 발생한다.    
제대로 수행되었는지 확인하려면 interrupted() 메서드(스레드의 상태를 변경시킴)를 호출하거나    
isInterrupted() 메서드(스레드의 상태만을 리턴함)를 호출하면 된다.

notify() 메서드와 notifyAll() 메서드는 모두 wait() 메서드를 멈추기 위한 메서드다.   
이 두 메서드는 Object 클래스에 정의되어 있는데, wait() 메서드가 호출된 후 대기 상태로 바뀐 스레드를 꺠운다.   
notify() 메서드는 객체의 모니터와 관련있는 단일 스레드를 깨우며, notifyAll() 메서드는 객체의 모니터와 관련 있는 모든 메서드를 깨운다.


<br/>       



#### - interrupt() 메서드는 절대적인 것이 아니다.

interrupt() 메서드를 호출하여 특정 메서드를 중지시키려고 할 때 항상 해당 메서드가 멈출까?    
답은 아니다. interrupt() 메서드는 해당 스레드가 'block' 되거나 특정 상태에서만 작동한다.

interrupt() 메서드는 대기 상태일 때에만 해당 스레드를 중단시키기 때문에   
while(true)와 같은 코드에서는 멈추지 않는다.    
이와 같은 반복 구문을 안전하게 만들려면 안전장치를 추가하는 것이 좋다.   
여러 방법이 있는데 flag 값을 수정하거나 sleep()을 추가하는 방법을 알아보자.

(1) flag 값 수정하기

```java   
public void setFlag(boolean flag) {  
    this.flag = flag;    
}    
```  

다른 스레드에서 interrupt() 메서드를 호출한 후 flag를 변경하면 된다. (main() 메서드 가장 마지막 줄)

(2) sleep() 추가하기

```java  
try {
    Thread.sleep(0, 1);
} catch(Exception e) {
    break;
}  
```  


<br/>       

#### - Synchronized를 이해하자.

synchronized는 메서드와 블록으로 사용할 수 있다.   
절대로 생성자의 식별자로는 사용할 수 없다.

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

<br/>       

#### - 동기화는 이렇게 사용한다. - 동일 객체 접근 시

예를들어, 여러 기부자가 어떤 기부금을 처리하는 단체에 기부금을 낸다고 가정하자.      
총 기부금 : amount

(1) 각각 단체에 시부 동기화 미사용 : 1.3 ms             
(2) 동일 단체에 기부 동기화 미사용 : 정상적인 결과 값이 넘어오지 않는다.          
(3) 동일 단체에 기부 동기화 사용 : 10.1ms

만약 1번 케이스에 synchronized를 사용하면 어떻게 될까?   
이 경우에는 2.2ms 가 소요된다.   
필요없는 부분에 synchronized를 사용하면 약간이지만 성능에 영향을 준다는 의미이다.

반드시 필요한 부분에만 동기화를 사용해야 성능 저하를 줄일 수 있다.

<br/>       

#### - 동기화는 이렇게 사용한다. - static 사용 시

static을 사용하는 경우에 동기화를 사용한다.          
앞서 살펴본 예제에서 amount를 static으로 선언하고 synchronized를 사용하면 어떻게 될까?

우선 amount를 static으로 선언하면 여러 단체가 있더라도 (객체의 변수가 아닌 클래스의 변수가 된다.)      
하나의 amount에 값을 지정하게 되므로 각 단체에 따로 기부하는 것은 구현할 수 없다.

그럼 donate() 메서드에 synchronized를 추가하면 될까?    
아니다. 우리가 원하는 결과 값이 나오지 않는다.   
synchronized는 각각의 객체에 대한 동기화를 하는 것이기 때문에 이렇게 하면 각각의 단체에 대한 동기화는 되지만   
amount에 대한 동기화는 되지 않는다.

amount는 클래스 변수이기 때문에 메서드도 클래스 메서드로 참조하도록 static을 추가해 주어야 한다.

```java               
public static synchronized void donate() {           
    amount++;        
}       
```        

이렇게 구현하면 원하는 결과 값을 얻을 수는 있다.   
하지만 항상 변하는 값에 대해서 static으로 선언하는 것은 위험하다.   
synchronized도 꼭 필요할 때만 사용하는 것이 좋다.

<br/>       

### - 동기화를 위해서 자바에서 제공하는 것들

JDK 5.0부터 추가된 java.util.concurrent 패키지에 대해서 간단히 알아보자.     
이 패키지에 주요 개념 4가지가 포함되어 있다.

(1) Lock : 실행 중인 스레드를 간단한 방법으로 정지시켰다가 실행시킨다. 상호참조로 인해 발생하는 데드락을 피할 수 있다.

(2) Execute : 스레드를 더 효율적으로 관리할 수 있는 클래스들을 제공한다.

(3) Concurrent 콜렉션 : 앞서 살펴본 콜렉션의 클래스들을 제공한다.

(4) Atomic 변수 : 동기화가 되어 있는 변수를 제공한다. 이 변수를 사용하면, synchronized 식별자를 메서드에 지정할 필요가 없이 사용할 수 있다.   


