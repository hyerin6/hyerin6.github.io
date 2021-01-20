---
layout: post
title: "Counting Sort 계수 정렬"  
description: "정렬할 값들이 단순할 경우, 이 단순함을 이용해서 O(n) 시간에 정렬하기"
date: 2021-01-20
tags: [algorithm]
comments: true
share: true
---

보통 빠르다는 정렬 알고리즘으로는 대표적으로 퀵 정렬(Quick Sort), 힙 정렬(Heap Sort), 합병 정렬(Merge Sort) 등이 있다.    
정렬은 보통 데이터끼리 비교하는 경우가 많아 𝚶(nlogn)보다 작아질 수 없는 것이 한계다.    
<br />       

### 정렬 방법   
(1) 정렬할 배열에 들어있는 값들 각각의 수를 세기 위한 count 배열을 생성한다.    
값의 종류가 많지 않다면, count 배열의 크기도 크지 않을 것이다.    

(2) 정렬할 배열을 선형 탐색하며, 각 값들의 수를 센다.      

(3) 각 값들의 수는 알고 있다는 것은, 정렬 결과 배열에 각 값들이 순서대로 몇 개씩 들어있어야 하는지 안다는 것이다. 정렬 결과 배열을 생성한다.      

두 수를 비교하는 과정이 없기 때문에 빠른 배치가 가능하지만 count 배열이라는 새로운 배열을 선언해야 한다.          
배열 안에 있는 max값의 범위에 따라 counting 배열의 길이가 달라지게 된다.         
예로들어 10개의 원소를 정렬하고자 하는데, 수의 범위가 0~1억이라면 메모리가 매우 낭비가 된다.       

즉 Counting Sort가 효율적인 상황에서 쓰려면 수열의 길이보다 수의 범위가 극단적으로 크면 메모리가 엄청 낭비 될 수 있다는 것이다.    
상황에 맞게 정렬 알고리즘을 써야하고 Quick 정렬의 경우 시간복잡도 평균값이 𝚶(nlogn)으로 빠른편이면서 배열도 하나만 사용하기 때문에 
공간복잡도는 𝚶(𝑛)으로 시간과 메모리 둘 다 효율적이라 대표적으로 많이 쓰인다.    
<br />    

### 수행시간   
수행시간: O(n + m)    
메모리 요구량: O(n + m)     

* n > m          
수행시간: O(n)    
메모리 요구량: O(n)    
<br />  

* n < m            
수행시간: O(m)    
메모리 요구량: O(m)    
<br />        


### TreeMap       
counting sort를 구현할 때, 자바의 TreeMap 클래스가 유용하다.    
Map 인터페이스를 implements 했기 때문에 사용법은 다음과 같다.      

```
- 데이터 저장: map.put(key, value)
- 데이터 값 조회: map.get(key)
- 데이터 제거: map.remove(key)
```

* HashMap 클래스는 해시 테이블 자료구조로 구현되었고, put, get, remove 모두 O(1) 이다.         
* TreeMap 클래스는 레드 블랙 트리 자료구조로 구현되었다. put, get, remove 모두 O(log N) 이다.    

레드 블랙 트리는 이진 트리이므로, TreeMap 클래스의 데이터 목록은 키(key) 값을 기준으로 정렬되어 있다.          
그러기 때문에 별도에 정렬은 하지 않고 TreeMap에 값을 순차적으로 출력하면 된다.    
하지만 TreeMap은 Key값이 중복이 되지 않기 때문에 값의 갯수를 value에 넣어야 한다.
<br />     

### 알고리즘 문제     
* 1920 수 찾기   
    - 문제 <https://www.acmicpc.net/problem/1920>     
    - 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B1920.java>    
    

* 2750 수 정렬하기    
    - 문제 <https://www.acmicpc.net/problem/2750>         
    - 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B2750.java>    


* 2751 수 정렬하기 2    
    - 문제 <https://www.acmicpc.net/problem/2751>          
    - 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B2751.java>    


* 10989 수 정렬하기 3    
    - 문제 <https://www.acmicpc.net/problem/10989>         
    - 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B10989.java>          
  

* 10815 숫자 카드    
    - 문제 <https://www.acmicpc.net/problem/10815>           
    - 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B10815.java>      


<br />
