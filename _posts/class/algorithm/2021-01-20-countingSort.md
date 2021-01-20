---
layout: post
title: "Counting Sort & Radix Sort"  
description: "계수 정렬 & 기수 정렬"
date: 2021-01-20
tags: [algorithm]
comments: true
share: true
---

# Counting Sort (계수 정렬)     

보통 빠르다는 정렬 알고리즘으로는 대표적으로      
퀵 정렬(Quick Sort), 힙 정렬(Heap Sort), 합병 정렬(Merge Sort) 등이 있다.      
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


# Radix Sort(기수 정렬)      

낮은 자리 수 부터 비교하여 정렬해 간다는 것을 기본 개념으로 하는 정렬 알고리즘이다.          
자릿수가 고정되어 있으니, 안전성이 있고(이때 데이터들 간의 상대적 순서는 보존되어야 한다.)        
기수 정렬은 비교 연산을 하지 않으며, 무엇보다도 전체 시간복잡도 역시 O(dn)이어서, 정수와 같은 자료의 정렬 속도가 매우 빠르다.        
정렬할 데이터의 radix가 작은 경우에 활용할 수 있다.      

하지만, 데이터 전체 크기에 기수 테이블의 크기만한 메모리가 더 필요하다.         
기수 정렬은 정렬 방법의 특수성 때문에, 부동소수점 실수처럼 특수한 비교 연산이 필요한 데이터에는 적용할 수 없지만, 사용 가능할 때에는 매우 좋은 알고리즘이다.       
<br />    

### 용어 정리   
* digit   
십진수의 digit는 10개이다. (0,1,2,3,4,5,6,7,8,9)   
이진수의 digit는 2개이다. (0,1)    
16진수의 digit는 16개이다. (0,1,2,3,4,5,6,7,8,9,A,B,C,D,E,F)    
<br />           
  
* radix      
digit의 수를 radix라고 한다.  
십진수의 radix는 10 이다.  
이진수의 radix는 2 이다.  
16진수의 radix는 16 이다.  
<br />

| |(1)|(2)|(3)|(4)|
|---|---|---|---|---|
|0123|156**0**|00**0**4|0**0**04|**0**004|
|2154|215**0**|02**2**2|1**0**61|**0**123|
|0222|106**1**|01**2**3|0**1**23|**0**222|
|0004|022**2**|21**5**0|2**1**50|**0**283|
|0283|012**3**|21**5**4|2**1**54|**1**061|
|1560|028**3**|15**6**0|0**2**22|**1**560|
|1061|215**4**|10**6**1|0**2**83|**2**150|
|2150|000**4**|02**8**3|1**5**60|**2**154|

(1) 일의 자리를 기준으로 정렬한다.  
(2) 십의 자리를 기준으로 정렬한다.  
(3) 백의 자리를 기준으로 정렬한다.  
(4) 천의 자리를 기준으로 정렬한다. 이 단계에서 정렬이 완료된다.     
<br />   

만약 배열에 음수도 들어있다면,    
모든 자릿 수를 정렬한 후에, 부호(+,-)를 고려하여 순서를 변경하는 작업을 추가하거나,   
아니면 미리 양수와 음수를 분리한 후, 양수 부분과 음수 부분을 따로 radix sort해야 한다.   
배열에서 양수와 음수를 분리할 때는 quick sort의 partition을 응용할 수 있다.      
<br />   

대부분의 데이터에서 자릿 수는 상수이다.     
그런데, 각 자리의 정렬을 O(N lo gN) 시간에 한다면, radix sort의 시간도 O(N lo gN) 시간이된다.   
각 자리의 정렬을 O(N) 시간에 한다면, radix sort의 시간도 O(N) 시간이 된다.    
각 자리의 값의 수는 digit 수와 같다.    
digit 수는 작기 때문에, digit 값을 기준으로 정렬하는 작업은 counting sort 알고리즘을 적용하면 된다.        

<br />    

### 구현      

```java
/*
src 배열의 값들을 정렬하여, dest 배열에 저장함
signed : 부호를 고려하여 정렬함.
radix sort 할 때, 마지막 counting sort는 부호를 고려해야함.
*/
public static void countingSort(int[] src, int[] dest, int nth, boolean signed) {
	int N = src.length;
	int[] count = new int[256];

	for (int i = 0; i < N; ++i) {
		int value = src[i];
		int digit = value >> (nth * 8) & 0xFF;
		++count[digit];
	}
	
	int[] index = new int[256];
	if (signed == false) {
		index[0] = 0;
		for (int i = 1; i < index.length; ++i) {
			index[i] = index[i - 1] + count[i - 1];
		}
	} else {
		index[128] = 0;
		for (int i = 129; i < index.length; ++i) {
			index[i] = index[i - 1] + count[i - 1];
		}
		for (int i = 0; i < 128; ++i) {
			int prev = i == 0 ? 255 : i - 1;
			index[i] = index[prev] + count[prev];
		}
	}
	
	for (int i = 0; i < N; ++i) {
		int value = src[i];
		int digit = value >> (nth * 8) & 0xFF;
		dest[index[digit]++] = value;
	}
}

public static void radixSort(int[] a) {
	int[] b = new int[a.length];
	for (int i = 0; i < 4; ++i) {
		if (i % 2 == 0) {
			countingSort(a, b, i, i == 3);
		} else {
			countingSort(b, a, i, i == 3);
		}
	}
}
```


<br />    


### 알고리즘 문제     
* 1920 수 찾기   
    - 문제 <https://www.acmicpc.net/problem/1920>     
    - 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B1920.java>    
<br />          


* 2750 수 정렬하기    
    - 문제 <https://www.acmicpc.net/problem/2750>         
    - 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B2750.java>    
<br />    


* 2751 수 정렬하기 2    
    - 문제 <https://www.acmicpc.net/problem/2751>          
    - 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B2751.java>    
<br />    


* 10989 수 정렬하기 3    
    - 문제 <https://www.acmicpc.net/problem/10989>         
    - 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B10989.java>          
<br />   


* 10815 숫자 카드    
    - 문제 <https://www.acmicpc.net/problem/10815>           
    - 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B10815.java>      


* 2075 N번째 큰 수
  - 문제 <https://www.acmicpc.net/problem/2075>    
  - 코드1 (priorityQueue 사용) <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B2075.java>     
  - 코드2 (radix sort 사용) <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B2075_v2.java>    
<br />     

![스크린샷 2021-01-20 오후 10 53 03](https://user-images.githubusercontent.com/33855307/105185553-fbdf1580-5b73-11eb-8cdc-f6030c0f58bb.png)     

* 제출번호 `25490906`: priorityQueue로 구현한 결과        
* 제출번호 `25477319`: radix sort로 구현한 결과           


<br />
