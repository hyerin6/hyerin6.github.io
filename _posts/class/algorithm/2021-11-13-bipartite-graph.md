---
layout: post     
title: "이분그래프란?"   
description: "Bipartite Graph"      
date: 2021-11-13     
tags: [algorithm]       
categories: [Algorithm]       
comments: true     
share: true   
---  


<br />


이분 그래프란 모든 꼭짓점을 빨강과 파랑으로 색칠하되, 

모든 변이 빨강과 파랑 꼭짓점을 포함하도록 색칠할 수 있는 그래프이다.    

<br />

![스크린샷 2021-11-13 오후 9 31 08](https://user-images.githubusercontent.com/33855307/141643915-b068310b-669d-4472-8c88-fbe12f8cff66.png)


<br />
<br />

## 이분 그래프 판별

이분 그래프인지 확인하는 방법은 다음과 같은 절차로 진행된다. 

```text
1. 모든 정점을 방문했는가?

YES >>️ 2.YES 출력 

NO  >>️ 3. 방문하지 않은 정점을 한 곳 방문하고 빨간색으로 칠한다. 
         queue에 해당 정점을 push 
   
4. queue에서 하나의 정점을 꺼내고 그 정점과 연결된 모든 정점을 가져온다. 
    4-1. 연결된 정점이 이미 방문한 적이 있다면 연결된 정점과 현재 정점의 색을 비교, 같으면 NO 
    4-2. 연결된 정점을 방문한 적이 없다면 현재 정점과 다른 색을 칠하고 queue에 넣는다. 
    4-3. queue에 아무것도 없을때까지 4-1 과정을 반복한다. 
    
5. 1번부터 반복한다.  
```

<br />
<br />

## 코드 

```java
static boolean bfs(int idx) {
	// 임의의 노드 방문
	Queue<Node> q = new LinkedList<>();
	q.add(nodes[idx]);

	while (!q.isEmpty()) {
		Node node = q.poll();

		// 이미 방문했는데 같은 색이라면 NO
		if (check(node)) {
			return false;
		} else {
			for (Node child : node.child) {
				if (!visited[child.idx]) {
					visited[child.idx] = true;
					child.setColor(1 - node.color);
					q.add(child);
				}
			}
		}

	}

	return true;
}
```


인접한 노드들을 check 해야하므로 Queue를 사용한 BFS로 탐색한다. 

<br />
<br />


## 예제 
* <https://www.acmicpc.net/problem/1707>
* <https://programmers.co.kr/learn/courses/30/parts/12486>



<br />

