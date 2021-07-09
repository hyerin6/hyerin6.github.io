---
layout: post
title: "Collection과 Map"  
description: "자바 성능 튜닝 이야기 story 4"
date: 2020-06-07
tags: [java]
comments: true
share: true
published: true
categories: [Java]
---

# 어디에 담아야 하는지?  

배열은 처음부터 크기를 지정해야 하지만,      
Collection의 객체 대부분은 그럴 필요 없이 객체들이 채워질 때마다 자동으로 크기가 증가된다.       
<br />

### Collection 및 Map 인터페이스의 이해

- Collection : 가장 상위 인터페이스이다.

- Set : 중복을 허용하지 않는 집합을 처리하기 위한 인터페이스이다.

- SortedSet : 오름차순을 갖는 Set 인터페이스이다.

- List : 순서가 있는 집합을 처리하기 위한 인터페이스이기 때문에 인덱스가 있어 위치를 지정하여 값을 찾을 수 있다.   
  중복을 허용하며, List 인터페이스를 상속받는 클래스 중에 가장 많이 사용하는 것으로 ArrayLIst가 있다.

- Queue : 여러 개의 객체를 처리하기 전에 담아서 처리할 때 사용하기 위한 인터페이스이다.        
  이 객체는 중복되는 키를 허용하지 않는다. 기본적으로 FIFO를 따른다.

- SortedMap : 키를 오음차순으로 정렬하는 Map 인터페이스이다.

<br />         


### Set 인터페이스
- HashSet : 데이터를 해쉬 테이블에 담는 클래스로 순서 없이 저장된다.

- TreeSet: red-black 이라는 크리에 데이터를 담는다.   
  값에 따라 순서가 정해진다.    
  데이터를 담으면서 동시에 정렬을 하기 떄문에 HashSet보다 성능상 느리다.

- LinkedHashSet : 해쉬 테이블에 데이터를 담는데, 저장된 순서에 따라서 순서가 결정된다.     
  <br />

HashSet과 LinkedHashSet의 성능이 비슷하고, TreeSet의 순서로 성능 차이가 발생한다.     
(LinkedHashSet > HashSet > TreeSet)

TreeSet은 데이터를 저장하면서 정렬한다.   
데이터를 순서에 따라 탐색해야 하는 경우에는 TreeSet을 사용하는 것이 좋다.   
하지만 그럴필요가 없을 때는 HashSet이나 LinkedHashSet을 사용하는 것을 권장한다.

<br />

### List 인터페이스
구현된 클래스에는 ArrayList와 Linked-List 클래스가 있으며, 원조 클래스 격인 Vector 클래스가 있다.

- Vector : 객체 생성시에 크기를 지정할 필요가 없는 배열 클래스이다.

- ArrayList : Vector과 비슷하지만, 동기화 처리가 되어 있지 않다.

- LinkedList : ArrayList와 동일하지만, Queue 인터페이스를 구현했기 때문에 FIFO 큐 작업을 수행한다.

<br />      

(1) 데이터를 넣는 속도 비교   
데이터를 넣은 것은 어떤 클래스든 큰 차이가 없다.   
<br />

(2) 순차적으로 결과를 받아오는 속도 비교 (`list.get(index)`)                 
ArrayList가 가장 빠르고, Vector과 LinkedList는 속도가 매우 느리다.          
LinkedList가 Queue 인터페이스를 상속받기 때문이다.                             
이를 수정하기 위해서는 순차적으로 결과를 받아오는 peek()나 poll() 메서드를 사용해야 한다.

수정 후, 테스트를 다시 해보면    
ArrayList, LinkedList, Vector 순으로 빠르게 바뀐다.     
<br />

**Q.** ArrayList와 Vector의 성능 차이는 왜 이렇게 큰가?    
**A.** Vector은 여러 스레드에서 접근할 경우를 방지하기 위해서     
get() 메서드에 synchronized가 선언되어 있다.      
그래서 성능 저하가 발생할 수밖에 없다.     
ArrayList는 여러 스레드에서 접근할 경우 문제가 발생할 수 있다.    
<br />

(3) 데이터를 삭제하는 시간 비교     
첫 번째 값 삭제와 마지막 값 삭제 속도의 차이가 크다.

LinkedList는 별 차이가 없지만, ArrayList나 Vector은 실제로 그 안에 배열을 사용한다.   
그래서 ArrayList와 Vector의 첫번째 값을 삭제하면 느릴 수 밖에 없다.


<br />         

### Map 인터페이스
Map은 key-value 쌍으로 저장되는 구조체이다.     
그래서 Map은 단일 객체만 저장하는 다른 Collection API들과는 다르게 따로 분리되어 있다.

구현한 클래스들은 HashMap, TreeMap, LinkedHashMap 세 가지와    
원조 클래스 격인 Hashtable 클래스가 있다.

- Hashtable : 데이터를 해쉬 테이블에 담는 클래스이다. 내부에서 관리하는데 해쉬 테이블 객체가 동기화되어 있으므로,   
  동기화가 필요한 부분에서는 이 클래스를 사용하기 바란다.

- HashMap : 데이터를 해쉬 테이블에 담는 클래스이다.   
  Hashtable 클래스와 다른 점은 null 값을 허용한다는 것과 동기화되어 있지 않다는 것이다.

- TreeMap : red-blak 트리에 데이터를 담는다.   
  TreeSet과 다른 점은 키에 의해서 순서가 정해진다는 점이다.

- LinkedHashMap : HashMap과 거의 동일하며 이중 연결 리스트라는 방식을 사용하여 데이터를 담는다는 점만 다르다.   
  <br />

대부분의 클래스가 동일하지만,     
트리 형태로 처리하는 TreeMap 클래스가 가장 느리다.

<br />         


### Queue 인터페이스
Queue는 데이터를 담아 두었다가 먼저 들어온 데이터부터 처리하기 위해서 사용된다.   
List의 큰 단점은 데이터가 많은 경우 처리 시간이 늘어난다는 점이다.   
가장 앞에 있는 데이터를 지우면 한 칸씩 옮기는 작업을 수행해야 하므로 느리다.


Queue 인터페이스를 구현한 클래스는 두 가지로 나뉜다.
* java.util 패키지에 속하는 LinkedList와 PriorityQueue -> 일반적인 목적의 큐 클래스
* java.util.concurrent 패키지에 속하는 클래스들 -> 컨커런트 큐 클래스     
  <br />

Queue 구현 클래스
- Priority Queue : 큐에 추가된 순서와 상관없이 먼저 생성된 객체가 먼저 나오도록 되어 있는 큐다.

- LinkedBlockingQueue : 저장할 데이터의 크기를 선택적으로 정할 수도 있는 FIFO 기반의 링크 노드를 사용하는 블로킹 큐다.

- ArrayBlockingQueue : 저장되는 데이터의 크기가 정해져있는 FIFO 기반의 블로킹 큐다.

- PriorityBlockingQueue : 저장되는 데이터의 크기가 정해져 있지 않고, 객체의 생성순서에 따라서 순서가 저장되는 블로킹 큐다.

- DelayQueue : 큐가 대기하는 시간을 지정하여 처리하도록 되어 있는 큐다.

- SynchronousQueue : put() 메서드를 호출하면, 다른 스레드에서 take() 메서드가 호출될 때까지 대기하도록 되어 있는 큐다.   
  이 큐에는 저장되는 데이터가 없다. API에서 제공하는 대부분의 메서드는 0이나 Null을 리턴한다.     
  <br />

> 블로킹 큐(blocking queue)란 크기가 지정되어 있는 큐에 더 이상 공간이 없을 때,   
> 공간이 생길 때까지 대기하도록 만들어진 큐를 의미한다.   


<br />         

### Collection 관련 클래스의 동기화       
HashSet, TreeSet, LinkedHashSet, ArrayList, LinkedList,      
HashMap, TreeMap, LinkedHashMap은 동기화되지 않은 클래스이다.    
(JDK 1.2 버전 이후에 만들어짐)   


동기화되어 있는 클래스로는 Vector와 Hashtable이 있다.    
(JDK 1.0 버전에 만들어짐)       

Collection 클래스에는 최신 버전 클래스들의 동기화를 지원하기 위한 synchronized로 시작하는 메서드들이 있다.    
`synchronized`: 현재 데이터를 사용하고 있는 해당 스레드를 제외하고          
나머지 스레드들은 데이터에 접근할 수 없도록 막는 개념      


synchronized 키워드를 너무 남발하면 오히려 성능 저하를 일으킬 수 있다.          
자바 내부적으로 blocking, non-blocking 처리를 하기 때문이다.          

<br />        
