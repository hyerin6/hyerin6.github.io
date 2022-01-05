---
title: "이진탐색이란?"   
categories:
  - Algorithm
tags:
  - algorithm  
--- 

<br />


## 순차 탐색

순차 탐색은 리스트 안에 있는 특정한 데이터를 찾기 위해 앞에서부터 데이터를 하나씩 차례대로 확인하는 방법이다. 

보통 정렬되지 않은 리스트에서 데이터를 찾아야할 때 사용한다. 

데이터가 아무리 많아도 시간이 충분하면 원하는 원소를 찾을 수 있다. 

데이터 개수가 N개일 때 최악의 시간 복잡도는 O(N)이다. 


<br />
<br />

## 이진 탐색: 반으로 쪼개면서 탐색 

이진 탐색은 배열 내부의 데이터가 정렬되어 있어야만 사용할 수 있는 알고리즘이다. 

데이터가 무작위일 때는 사용할 수 없지만, 이미 정렬된 데이터는 빠르게 찾을 수 있다. 

이진 탐색은 위치를 나타내는 변수 3개를 이용하는데 시작점, 끝점, 중간점을 기준으로 

반복적으로 데이터를 비교해서 원하는 데이터를 찾는 게 이진 탐색 과정이다. 

<br />

```java
public int binarySearch(int[] arr, int target, int start, int end) {
    while(start <= end) {
        int mid = (start+end)/2;
        if(arr[mid] == target) {
            return mid;
        } else if (arr[mid] > target) {
            end = mid - 1;
        } else {
            start = mid + 1;
        }
    } 
    return -1;
}
```

<br />

## 코딩 테스트에서의 이진 탐색 

이진 탐색 코드는 쉬워보이지만 문제를 풀어보면 구현이 까다로울 수 있다. 

이진 탐색의 원리는 다른 알고리즘에서도 폭넓게 적용되는 원리와 유사하기 때문에 매우 중요하다. 

또 높은 난이도의 문제에서 여러 알고리즘과 함께 사용되기도 한다. 

탐색 범위가 큰 상황에서 (2,000만 이상) 이진 탐색으로 접근해보길 권한다. 

<br />
<br />


## 예제 문제1: 부품 찾기 

전자 매장에 부품이 N개 있다. 

각 부품은 정수 형태의 고유한 번호가 있다. 

손님이 M개 종류의 부품을 대량으로 구매하겠다며 당일 날 견적서를 요청했다. 

손님이 문의한 부품 M개 종류를 모두 확인해서 견적서를 작성해야 한다. 

이때 가게 안에 부품이 모두 있는지 확인하는 프로그램을 작성해보자. 

```text
N = 5
[8, 3, 7, 9, 2]

M = 3
[5, 7, 9]
```


위에서 봤던 이진 탐색 코드를 그대로 사용하면 된다. 

[전체 코드 보러가기](https://github.com/hyerin6/Algorithm/blob/master/programmers/src/programmers/practice/%EB%B6%80%ED%92%88%EC%B0%BE%EA%B8%B0.java)


<br />

위 문제는 이진 탐색 말고도 계수 정렬의 개념을 이용해서 문제를 풀 수도 있다. 

또는 단순하게 Set 자료형을 이용해서 해결할 수도 있다. 

<br />
<br />

## 예제 문제2: 떡볶이 떡 만들기 

오늘은 떡볶이 떡을 만드는 날이다. 떡의 길이가 일정하지 않다. 

대신에 한 봉지 안에 들어가는 떡의 총 길이는 절단기로 잘라서 맞춰준다. 

절단기 높이(H)를 지정하면 줄지어진 떡을 한 번에 전달한다. 

높이가 H보다 긴 떡은 H 위의 부분이 잘릴 것이고, 낮은 떡은 잘리지 않는다. 

N: 떡의 개수, M: 손님이 요청한 최소한의 떡의 길이 

```text
N: 4 
M: 6
19 15 10 17
```

`[19, 15, 10, 17]` 높이의 떡이 나란히 있다. 절단기 높이를 15로 지정하면 

자른 뒤 떡의 높이는 `[15, 15, 10, 15]`가 될 것이다. 

잘린 떡의 길이는 차례대로 `[4, 0, 0, 2]` 이다. 

손님은 6cm 만큼의 길이를 가져간다. 


손님이 왔을 때 요청한 총 길이가 M일 때 적어도 M 만큼의 떡을 얻기 위해 

절단기에 설정할 수 있는 높이의 최댓값을 구하는 프로그램을 작성하라. 


<br />

이 문제는 전형적인 이진 탐색 문제이다.

'원하는 조건을 만족하는 가장 알맞은 값을 찾는 문제'에 주로 사용한다. 

예를 들어 범위 내에서 조건을 만족하는 가장 큰 값을 찾으라는 최적화 문제라면 

이진 탐색으로 결정 문제를 해결하면서 범위를 좁혀갈 수 있다. 

<br />

이 문제는 적절한 높이를 찾을 때까지 절단기의 높이 H를 반복해서 조정하면 된다. 

범위를 좁힐 때는 이진 탐색의 원리를 이용해 절반씩 탐색 범위를 좁혀나가면 된다. 

시작은 다음과 같이 지정하여 시작한다. 

```text
시작점: 0
끝점: 19
중간점: 9

잘린 떡의 길이: [10, 6, 1, 8]
```

[전체 코드 보러가기](https://github.com/hyerin6/Algorithm/blob/master/programmers/src/programmers/practice/%EB%96%A1%EB%B3%B6%EC%9D%B4.java)

<br />
<br />

## 연습 문제 

* [랜선 자르기](https://www.acmicpc.net/problem/1654)
* [공유기 설치](https://www.acmicpc.net/problem/2110)
* [민호와 강호](https://www.acmicpc.net/problem/11662)

<br />