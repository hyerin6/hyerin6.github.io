---
layout: post     
title: "분할 정복이란?"   
description: "Divide and Conquer"      
date: 2021-11-18        
tags: [algorithm]       
categories: [Algorithm]       
comments: true     
share: true
---  

<br />


## 분할정복법이란? 
주어진 문제를 작은 사례로 나누고(divide) 각각의 작은 문제들을 해결하여 정복(conquer)하는 방법이다. 

작은 사례의 해답을 바로 얻을 수 있으면 해를 구하고 아니면 더 작은 사례로 나눈다. 

해를 구할 수 있을 만큼 충분히 작은 사례로 나눠서 해결하는 방법이다. 

```text
1. 문제 사례를 하나 이상의 작은 사례로 분할 
2. 작은 사례들을 각각 정복한다. (재귀)
3. 필요하다면, 작은 사례에 대한 해답을 통합하여 원하는 해답을 구한다. 
```

<br />
<br />

## 장단점 

#### 장점 

문제를 나눔으로써 어려운 문제를 해결한다. 

문제를 나누어 해결한다는 특징상 병렬적으로 문제를 해결하느 데 큰 강점이 있다. 

<br />

#### 단점 

함수를 재귀적으로 호출한다는 점에서 함수 호출로 인한 오버헤드가 발생하며, 

스택에 다영한 데이터를 보관하고 있어야 하므로 스택오버플로우가 발생하거나 과도한 메모리를 사용하게 된다. 


<br />
<br />



## 문제: [종이의 개수](https://www.acmicpc.net/problem/1780)

N(1 ≤ N ≤ 37, N은 3k 꼴)의 범위가 문제를 나누는 기준이 된다. 


```java
int newSize = size / 3;

partition(row, col, newSize);
partition(row, col + newSize, newSize);
partition(row, col + 2 * newSize, newSize);

partition(row + newSize, col, newSize);
partition(row + newSize, col + newSize, newSize);
partition(row + newSize, col + 2 * newSize, newSize);

partition(row + 2 * newSize, col, newSize);
partition(row + 2 * newSize, col + newSize, newSize);
partition(row + 2 * newSize, col + 2 * newSize, newSize);
```

<br />

## 문제: [하노이 탑 이동 순서](https://www.acmicpc.net/problem/11729)

```java
// N-1 개의 원판을 목적지가 아닌 곳으로 옮긴다.
move(N - 1, start, sub, end);
// 제일 큰 원판을 목표 타워로 옮긴다.
move(1, start, end, sub);
// 서브 타워로 옮겨놨던 원판을 목적지로 옮긴다.
move(N - 1, sub, end, start);
```

<br />

## 문제: [쿼드트리](https://www.acmicpc.net/problem/1992)

```java
int newSize = size / 2;

sb.append("(");
// 왼쪽 위, 오른쪽 위, 왼쪽 아래, 오른쪽 아래 순서가 정해져있음
partition(newSize, x, y);
partition(newSize, x, y + newSize);
partition(newSize, x + newSize, y);
partition(newSize, x + newSize, y + newSize);
sb.append(')');
```

<br />


