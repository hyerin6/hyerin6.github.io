---
layout: post     
title: "algorithm class3"   
description: "Algorithm"      
date: 2021-11-25        
tags: [algorithm]       
categories: [Algorithm]       
comments: true     
share: true
---  

<br />

## [패션왕 신해빈](https://www.acmicpc.net/problem/9375)   

```text
[input]
2
3
hat headgear
sunglasses eyewear
turban headgear
3
mask face
sunglasses face
makeup face
```

경우의 수를 구하기 위해 정리하면 다음과 같다. NULL은 착용하지 않는 경우    
* headgear: hat, turban, NULL  
* eyewear: sunglasses, NULL  

그러면 headgear 에 대해서는 3C1 을 구하면 되고, eyewear 에 대해서는 2C1 을 구하면 된다.

```
3C1 = 3
2C1 = 2
```


이 둘을 곱하면 종류별로 조합하는 공식이 나온다.

`3C1 × 2C1 = 3 × 2 = 6`

두 종류 모두 안 입는 경우가 있기 때문에 마지막에 1을 빼주어야 한다.

```
3C1 × 2C1 - 1
= 3 × 2 - 1
= 5
``` 
 
<br />

## 💻  풀이 코드 

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;

public class B9375 {

	public static void main(String[] args) throws Exception {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

		int t = Integer.parseInt(br.readLine());

		while (t-- > 0) {
			Map<String, Integer> clothes = new HashMap<>();
			int n = Integer.parseInt(br.readLine());
			for (int i = 0; i < n; ++i) {
				String category = br.readLine().split(" ")[1];
				clothes.put(category, clothes.getOrDefault(category, 0) + 1);
			}

			int result = 1;

			// 안 입는 경우를 고려하여 각 종류별 옷의 개수에 +1 해준 값을 곱해주어야 한다.
			for (int val : clothes.values()) {
				result *= (val + 1);
			}
			// 알몸인 상태를 제외해주어야 하므로 최종값에 -1 
			System.out.println(result - 1);
		}

	}
}
```

<br />
<br />

## [구간 합 구하기 4](https://www.acmicpc.net/problem/11659)       

```
주어진수 [1, 2, 3, 4, 5] 
누적합  [1, 3, 6, 10, 15]
```

만약 2 ~ 4 범위의 수들의 합을 구한다면, 

```
2+3+4 
= numbers[4] - numbers[1] 
= 10 - 1 
= 9
```

<br />

## 💻  풀이 코드 

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class B11659 {
	public static void main(String[] args) throws Exception {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine());

		int N = Integer.parseInt(st.nextToken());
		int M = Integer.parseInt(st.nextToken());

		int[] numbers = new int[N + 1];
		st = new StringTokenizer(br.readLine());
		for (int i = 1; i <= N; ++i) {
			numbers[i] = numbers[i - 1] + Integer.parseInt(st.nextToken());
		}

		for (int i = 0; i < M; ++i) {
			st = new StringTokenizer(br.readLine());
			int start = Integer.parseInt(st.nextToken());
			int end = Integer.parseInt(st.nextToken());
			int result = numbers[end] - numbers[start - 1];
			System.out.println(result);
		}
	}
}
```


<br />
<br />


## [색종이 만들기](https://www.acmicpc.net/problem/2630)   

색종이를 자를 때는 1/2 씩, 즉 절반씩 잘라서 정사각형을 얻어야 한다.       


<br />

## 💻  풀이 코드 

```java
public static void partition(int row, int col, int size) {
	if (check(row, col, size)) {
		cnt[map[row][col]]++;
		return;
	}

	int newSize = size / 2;
	partition(row, col, newSize);
	partition(row, col + newSize, newSize);
	partition(row + newSize, col, newSize);
	partition(row + newSize, col + newSize, newSize);
}
```

<br />
<br />

## [유기농 배추](https://www.acmicpc.net/problem/1012)     

dfs와 bfs 모두 풀 수 있는 문제이다.  

<br />

## 💻  풀이 코드

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class B1012 {

	static int[] dx = {0, -1, 0, 1};
	static int[] dy = {1, 0, -1, 0};

	static int M, N, K;
	static int[][] map;
	static boolean[][] visited;

	public static void dfs(int x, int y) {
		visited[x][y] = true;

		for (int i = 0; i < 4; i++) {
			int cx = x + dx[i];
			int cy = y + dy[i];

			if (cx >= 0 && cy >= 0 && cx < M && cy < N) {
				if (!visited[cx][cy] && map[cx][cy] == 1) {
					dfs(cx, cy);
				}
			}
		}
	}

	public static void main(String[] args) throws Exception {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st;

		int t = Integer.parseInt(br.readLine());

		while (t-- > 0) {
			st = new StringTokenizer(br.readLine());
			M = Integer.parseInt(st.nextToken());
			N = Integer.parseInt(st.nextToken());
			K = Integer.parseInt(st.nextToken());
			map = new int[M][N];
			visited = new boolean[M][N];
			for (int i = 0; i < K; ++i) {
				st = new StringTokenizer(br.readLine());
				int row = Integer.parseInt(st.nextToken());
				int col = Integer.parseInt(st.nextToken());
				map[row][col] = 1;
			}
			int count = 0;
			for (int x = 0; x < M; x++) {
				for (int y = 0; y < N; y++) {
					if (map[x][y] == 1 && !visited[x][y]) {
						dfs(x, y);
						count++;
					}
				}
			}
			System.out.println(count);
		}
	}
}
```

<br />
<br />

## [IOIOI](https://www.acmicpc.net/problem/5525)

```
N+1개의 I와 N개의 O로 이루어져 있으면, I와 O이 교대로 나오는 문자열을 PN이라고 한다.
* P1 IOI
* P2 IOIOI
* P3 IOIOIOI
* PN IOIOI...OI (O가 N개)
```

문제에서 핵심은 'O'가 N개만큼 들어있다는 점이다.          
'OI'가 몇 개 반복되었는지 체크하면 된다.       


<br />

## 💻  풀이 코드: 그냥 문자열 비교   


```java
import java.io.BufferedReader;
import java.io.InputStreamReader;

public class B5525 {

	public static void main(String[] args) throws Exception {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

		int N = Integer.parseInt(br.readLine());
		int M = Integer.parseInt(br.readLine());
		char[] s = br.readLine().toCharArray();

		int result = 0;
		int patternCnt = 0;
		for (int i = 1; i < M - 1; i++) {
			if (s[i - 1] == 'I' && s[i] == 'O' && s[i + 1] == 'I') {
				patternCnt++;
				if (patternCnt == N) {
					patternCnt--;
					result++;
				}
				i++;
			} else
				patternCnt = 0;
		}

		System.out.println(result);
	}
}
```

<br />

## 💻  풀이 코드: DP 활용

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class B5525_v2 {
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

		int N = Integer.parseInt(br.readLine());
		int M = Integer.parseInt(br.readLine());
		char[] s = br.readLine().toCharArray();

		int[] memo = new int[M];
		int ans = 0;

		for (int i = 1; i < M - 1; ++i) {
			if (s[i] == 'O' && s[i + 1] == 'I') {
				memo[i + 1] = memo[i - 1] + 1;

				if (memo[i + 1] >= N && s[i - 2 * N + 1] == 'I')
					ans++;
			}
		}

		System.out.println(ans);
	}
}
```

