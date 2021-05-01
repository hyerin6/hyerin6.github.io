---
layout: post
title: "ssh config 사용해서 ssh 접속하기"  
description: "ssh config, private key"
date: 2021-01-15
tags: [depromeet]
comments: true
share: true
published: true 
---

젠킨스를 도커로 띄워 사용하면서 ssh 접속을 자주 경험했었다.     
* 이전에 젠킨스 사용하며 ssh 접속 방법 : <https://hyerin6.github.io/2020-04-24/0424/>      
 
항상 원격 서버에 public key를 등록하고 (`.ssh/authorized_keys`)                   
`ssh 계정@호스트주소` 명령으로 ssh 접속을 했는데                    
이번에는 config 파일을 이용해 원격 접속을 더 간단하게 해보기로 했다.                           
<br />                   

### config 파일을 이용한 원격 접속              

(1) `~/.ssh`에 config 파일을 생성한다. 접근 권한은 600으로 설정           

```
cd ~/.ssh 
touch config 
chmod 600 config
```  
<br />    

(2) config 파일 내 원격 접속 정보 추가           
* Host : ssh 접속 시 이용할 별명       
* HostName : ip 혹은 host 주소      
* User : (원격 서버) 계정       
* IdentityFile : ssh 접속에 이용할 private key (id_rsa : Private Key file)        


위 정보를 config 파일에 담아야 하는데 다음과 같이 작성하면 된다.    
이번 프로젝트에서는 port도 지정해야해서 Port 설정도 추가했다.            
```  
Host woof
  HostName [ip 혹은 host 주소]
  User [계정]
  IdentityFile [private key (위치)]
  Port [port]
```  


별명을 woof로 설정했기 때문에 ssh 접속할 때 다음과 같이 접속하면 된다.     
```
ssh woof
```

직접 설정한 이름을 이용해서 ssh 접속을 간편하게 할 수 있다.      
<br />        
 
(3) 패스워드가 있다면 패스워드 입력 후 접속이 가능하다.                          
Passphrase 는 키의 비밀번호로, 암호화되어 키 생성에 사용된다.             
    