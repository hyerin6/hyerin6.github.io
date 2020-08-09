---
layout: post
title: "디프만 8기 4조 워밍업 프로젝트 : 꿀단지"  
description: "꿀단지"
date: 2020-08-09
tags: [depromeet]
comments: true
share: true
--- 


## Git Rebase     

두개의 브랜치에서 작업할 때 하나의 브랜치를 다른 브랜치로 합치기 위해서   
git에서 두 가지 방법을 주로 사용한다.   

- merge   
- rebase  

나는 merge만 사용했었는데 이번 프로젝트에서 rebase에 대해 알아보고 사용해보기로 했다!   

<br />           


## Rebase       

브랜치의 공통 조상이 되는 base를 다른 브랜치의 커밋 지점으로 바꾸는 것이라고 할 수 있다.      

기본 전략은 다음과 같다.      
먼저 rebase하려는 브랜치 커밋들의 변경사항을 patch라는 것으로 만든 다음에      
어딘가에 저장해 두고 이를 master 브랜치에 하나씩 적용하여 새로운 커밋을 만든다.       

feature를 master 브랜치로 rebase하는 명령어를 살펴보자.               

<br />     

```shell script
git checkout feature            
git rebase master            

git merge feature           
```

1. feature 브랜치로 checkout           
2. master 브랜치로 rebase           
3. feature 브랜치를 master로 fast-forward merge     


참고 : <https://velog.io/@godori/Git-Rebase>             