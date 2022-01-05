---
title: "순열 permutation"
categories:
  - Algorithm
tags:
  - algorithm  
toc: true
author_profile: true
toc_sticky: true
---


### [소수 찾기](https://programmers.co.kr/learn/courses/30/lessons/42839)          


```text    
한자리 숫자가 적힌 종이 조각이 흩어져있습니다.     
흩어진 종이 조각을 붙여 소수를 몇 개 만들 수 있는지 알아내려 합니다.    
각 종이 조각에 적힌 숫자가 적힌 문자열 numbers가 주어졌을 때,    
종이 조각으로 만들 수 있는 소수가 몇 개인지 return 하도록 solution 함수를 완성해주세요.     

ex. INPUT : "17" RETURN : 3      
```


에라토스테네스의 체를 이용해서 소수인지 아닌지 검사하는 알고리즘은 예전에 풀어본 적이 있어          
쉽게 떠올랐지만 문자열을 조합해서 숫자를 만드는 것은 쉽지 않았다.          

먼저 순열 알고리즘을 공부해보자.     


<br />       

### 순열       

순열이란 n개의 값 중에서 r개의 숫자를 모든 순서대로 뽑는 경우를 말한다.   
예를들어 [1, 2, 3] 이라는 배열에서 길이가 2인 숫자를 만든다고 가정하면   

[1, 2]    
[1, 3]      
[2, 1]    
[2, 3]     
[3, 1]    
[3, 2]    

총 6개의 숫자가 만들어진다. 

<br />       

### swap을 이용한 순열   

배열의 첫번째 값부터 하나씩 바꾸며 모든 값을 한번씩 swap 한다.   
depth를 기준 인덱스로 depth 보다 작으면 고정, 크면 다시 swap 한다.   

순서는 보장되지 않는다.  

```  
static void permutation(int[] arr, int depth, int n, int r) {  
    if (depth == r) {  
        print(arr, r);  
        return;  
    }  
 
    for (int i=depth; i<n; i++) {  
        swap(arr, depth, i);  
        permutation(arr, depth + 1, n, r);   
        swap(arr, depth, i);    
    }  
}   

static void swap(int[] arr, int depth, int i) {  
    int temp = arr[depth];  
    arr[depth] = arr[i];  
    arr[i] = temp;  
}  
```

<br />       


### visited 배열을 이용한 순열    

swap을 이용한 구현과는 다르게 사전식으로 순열을 구할 수 있다.   

- arr : r개를 뽑기 위한 n개의 값      
- output : 뽑힌 r개의 값   
- visited : 중복해서 뽑지 않기 위해 체크하는 값    

DFS를 돌면서 모든 인덱스를 방문하여 output에 값을 넣는다.   
이미 들어간 값은 visited를 true로 바꿔서 중복을 방지한다.   
depth값은 output에 들어간 숫자의 길이이다.  
depth의 값이 r만큼 되면 output에 들어있는 값을 출력하면 된다.   


```        
static void perm(int[] arr, int[] output, boolean[] visited, int depth, int n, int r) {    
    if (depth == r) {    
        print(output, r);    
        return;    
    }    
 
    for (int i=0; i<n; i++) {    
        if (visited[i] != true) {    
            visited[i] = true;    
            output[depth] = arr[i];    
            perm(arr, output, visited, depth + 1, n, r);           
            visited[i] = false;;    
        }
    }
}
```

<br />          

### 에라토스테네스의 체   

```
public static boolean isPrime(int num){  
		// 에라토스테네스의 체를 이용  
		for(int i = 2; i <= Math.sqrt(num); i++){  
			for(int j = 2; j * i <= num ; j++){  
				NUMBER[j * i] = false;  
			}  
		}  
		return NUMBER[num];  
}
```

<br />        

#### 참고    
<https://bcp0109.tistory.com/14>



