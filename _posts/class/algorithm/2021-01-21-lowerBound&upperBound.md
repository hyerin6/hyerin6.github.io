---
layout: post
title: "lowerBound & upperBound"  
description: "KEY를 찾아도 끝내지 않고 계속 탐색 범위를 줄여가는 BinarySearch"
date: 2021-01-20
tags: [algorithm]
categories: [Algorithm]
comments: true
share: true
---

## BinarySearch      
다음은 일반적으로 원하는 값(Key)을 찾을 때 사용하는 binarySearch이다.     

```java
static int binarySearch(int[] list, int key) {
    int left = 0;
    int right = list.length - 1;
    
    while(left <= right) {
        int mid = (left + right) / 2;
        
        if(list[mid] == key) {
            return mid;
        } else if(list[mid] < key) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
	
    return -1;
}
```

key 값을 찾으면 바로 index를 return 하고 못 찾으면 -1을 return 한다.       

<br />    
<br />         

## lowerBound, upperBound        

```java
private static int lowerBound(List<Integer> data, int target) {
    int begin = 0;
    int end = data.size();

    while(begin < end) {
        int mid = (begin + end) / 2;

        if(data.get(mid) >= target) {
            end = mid;
        } else {
            begin = mid + 1;
        }
    }
    return end;
}

private static int upperBound(List<Integer> data, int target) {
    int begin = 0;
    int end = data.size();

    while(begin < end) {
        int mid = (begin + end) / 2;

        if(data.get(mid) <= target){
            begin=mid+1;
        } else {
            end = mid;
        }
    }
    return end;
}
```

<br />     

#### lowerBound        
범위 [begin, end] 안의 원소들 중, 특정 key보다 크거나 같은 첫번째 원소의 인덱스를 리턴한다.        
만약 그런 원소가 없다면 end 인덱스를 리턴한다.            

#### upperBound  
범위 [begin, end] 안의 원소들 중, 특정 key보다 큰 첫번째 원소의 인덱스를 리턴한다.       
만약 그런 원소가 없다면 end 인덱스를 리턴한다.          


<br />     
<br />         

### 관련 문제          
* 문제 <https://www.acmicpc.net/problem/10816>         
* 풀이 코드 <https://github.com/hyerin6/Algorithm/blob/master/Baekjoon/src/training/B10816.java>                 

upperbound에서 key보다 큰 인덱스값을 뽑아내는 이유는      
`upperBound - lowerBound`를 통해서 중복값의 개수를 구하기 위해서이다.         


<br />        