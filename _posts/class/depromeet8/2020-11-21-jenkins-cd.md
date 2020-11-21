---
layout: post
title: "Jenkins 배포 자동화"    
description: "디프만 파이널 프로젝트 : jenkins CD"
date: 2020-11-21
tags: [depromeet, jenkins]
comments: true
share: true
--- 

어쩌다보니 배포 자동화도 하게 되었는데 ci와 마찬가지로 스크립트를 작성해서 꼼꼼하게 배포 과정을 공부해보기로 했다.      

처음에는 젠킨스가 docker로 띄워진줄 모르고 ssh 키를 생성하고 배포용 서버에 넣어줬는데 다음과 같은 메시지가 뜨면서 실패해서 당황했지만   
`Host key verification failed.Host key verification failed.`  

젠킨스를 docker로 띄웠을 때는 ssh를 jenkins_home에서 생성해야 한다는 것을 알 수 있었다..      


<br />       

## Script              

아직 완성된건 아니지만 원하는 git branch 로 언제든지 배포용 서버에 배포될 수 있게 젠킨스 설정을 마쳤다.   


```
#!/bin/bash 

#원하는 브랜치에서 clone받아 배포해볼 수 있게 파라미터로 브랜치만 받는다. 
git clone -b $branch --single-branch https://github.com/depromeet/8th-final-team5-backend.git

// properties를 생성하는 쉘 파일을 만들 계획인데 프로젝트 진행을 위해 일단 다음과 같이 생성했다. 
cd 8th-final-team5-backend
./mvnw clean package
cd ./target
mkdir config
cd ./config
touch application.properties

#active를 prod로 설정해줘야 디폴트 파일로 실행되지 않는다. 
echo "spring.profiles.active=prod" >> application.properties
echo "spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver" >> application.properties
echo "spring.datasource.url=jdbc:mysql://dodo.mysql.database.azure.com:3306/dodo?useUnicode=yes&characterEncoding=UTF-8&allowMultiQueries=true&serverTimezone=Asia/Seoul" >> application.properties
echo "spring.datasource.username=" >> application.properties
echo "spring.datasource.password=" >> application.properties
echo "spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl" >> application.properties

#배포용 서버에 있던 이전 파일들을 삭제한다. 
ssh dangdang@20.196.153.12 "rm -rf ~/dodo/*"

#jar파일과 properties가 들어있는 config 디렉토리를 배포용 서버로 보내준다. 
cd ../
scp -r -P 22 config dangdang@20.196.153.12:dodo
scp -P 22 5th-final-0.0.1-SNAPSHOT.jar dangdang@20.196.153.12:dodo

#우선 java가 들어가는 프로세스를 kill하는데 이 부분도 어떻게 해결해야 할지 아직 고민중이다.
ssh dangdang@20.196.153.12 "pkill -9 java"   

#배포, log는 dodo 디렉토리의 tomcat.log에 저장된다. 
ssh dangdang@20.196.153.12 "cd dodo; nohup java -jar 5th-final-0.0.1-SNAPSHOT.jar >> tomcat.log &"
```


아직 많은 부분을 수정해야 하지만 원하는 브랜치를 젠킨스의 `Build With Parameters`에서 파라미터로 넘겨 빌드시키면      
자동으로 배포용 서버에 jar와 config를 생성해 넘겨 배포되는 자동 배포가 완성되었다.   

properties 파일을 생성하는 부분과 실행 중인 프로세스를 kill하는 부분을 조금 더 고민해보고 수정할 예정이다.   



