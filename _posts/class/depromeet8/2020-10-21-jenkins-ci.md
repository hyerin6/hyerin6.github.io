---
layout: post
title: "jenkins 테스트 자동화"  
description: "디프만 파이널 프로젝트 : jenkins CI"
date: 2020-10-21
tags: [depromeet, jenkins]
comments: true
share: true
--- 

로그인 기능 개발을 시작하면서 젠킨스를 이용한 자동 배포와 테스트 자동화를 해보기로 했고 테스트 자동화를 담당하게 되었다.     

우선 작업(job)을 새로 만들고 git repogitory와 연동했다.   

<br />    
 
## 1. Jenkins & Git 연동                

jenkins의 첫 화면에서 사람 > user 선택 > 설정 > token 생성       
위와 같은 경로로 들어가 jenkins에서 토큰을 만들어주고          

git webhook에서 Payload URL을 다음과 같이 작성하면 된다.             

```
http://<jenkins_user_name>:<jenkins_token>@<jenkins_ip>/job/<jenkins_job>/buildWithParameters?token=<Authentication_Token>
```

jenkins_user_name은 젠킨스 유저 ID,       
jenkins_token은 젠킨스 유저 설정에서 생성한 token,       
Authentication_Token은 젠킨스 job에서 빌드 유발을 `빌드를 원격으로 유발` 로 선태했을 때        
내가	`Authentication Token` 에 적은 문자열이다.           

다음 화면에서 원하는 문자열을 입력하면 된다.      

 
![스크린샷 2020-11-02 오후 10 22 55](https://user-images.githubusercontent.com/33855307/97872903-24682800-1d5a-11eb-9e3d-b45ec542b776.png)    


git webhook에서 Pull request를 선택하고 저장하면        
pr에 이벤트가 발생할 때 젠킨스에서 스크립트 실행이 가능해진다.       

<br />    



## 2. 젠킨스 설정   

젠킨스에서 dangdang_ci라는 job을 만들고 git 연동까지 완료했다.       
플러그인을 사용하지 않고 스크립트를 직접 작성하며 공부해보기로 했기 때문에   
젠킨스에 특별한 설정은 필요없고 매개변수 설정이랑 스크립트 작성, 빌드 전 workspace(git clone 받은 것) 삭제 설정만 하면된다.    

다만, pr 테스트 결과를 git에 다시 보내려면 git에서 token을 발급 받아야 한다. (한번만 보여주니까 저장해둬야함)    
git 계정 Settings > Developer settings > Personal access tokens 에서 토큰을 생성해서      
헤더에 다음과 같은 형식으로 보내주면 된다.   
`Authorization: token <TOKEN>`    

(url에 query string(access_token)으로 보내도 되는데 git에서 권장하지 않기 때문에 언제든지 작동하지 않을 수 있다는 메일이 날아온다.)


 

스크립트는 다음과 같이 작성했다.      



```    
#!/bin/bash -li


# git pr 정보를 payload 매개변수로 받아 payload.txt 파일로 저장합니다.    
echo $payload > payload.txt 

# pr 의 상태를 action 변수에 저장합니다. (ex. opened, closed)
action=`python -c 'import json, os; d = json.loads(open("payload.txt").read()); print d["action"]'` 

# pr의 상태가 opened이나 reopened이 아니면 테스트를 진행하지 않습니다.   
if [ $action != "opened" ] || [ $action != "reopened" ]; then exit ; fi   

# ci 결과를 다시 git에 보내주기 위해 payload로 받은 정보를 파싱합니다. 
pr_branch=`python -c 'import json, os; d = json.loads(open("payload.txt").read()); print d["pull_request"]["head"]["ref"]'` 
curl_url=`python -c 'import json, os; d = json.loads(open("payload.txt").read()); print d["pull_request"]["statuses_url"]'` 

# pr branch의 코드만 가져옵니다. 
git clone -b $pr_branch --single-branch https://github.com/depromeet/8th-final-team5-backend.git

cd 8th-final-team5-backend 

# 테스트 진행 
./mvnw test > build.txt

# 테스트 결과를 build.txt 파일에 저장하고 결과가 실패인지 result 변수에 저장합니다. (테스트 실패하면 result에 0이 저장됨)
result=`cat build.txt | grep -q "BUILD FAILURE" ; echo $?` 

# payload로 받은 statuses_url을 사용하여 pr 상태를 변경할 수 있다.   
# $BUILD_NUMBER 는 jenkins에서 제공하는 변수다. git에서 detail 링크를 누르면 스크립트 결과를 바로 볼 수 있다.     
# 테스트 결과 성공하면 state를 success 실패하면 failure로 git에 전달   
if [ $result == "1" ]; then \
	curl "${curl_url}" \
  		-H "Content-Type: application/json" \
  		-H "Authorization: token <TOKEN>" \
  		-X POST \
  		-d "{\"state\": \"success\",\"context\": \"continuous-integration/jenkins\", \"description\": \"Jenkins\", \"target_url\": \"http://20.194.0.141/job/dangdang_ci/$BUILD_NUMBER/console\"}"; \
else \ 
	curl "${curl_url}" \ 
  		-H "Content-Type: application/json" \
  		-H "Authorization: token <TOKEN>" \
  		-X POST \
  		-d "{\"state\": \"failure\",\"context\": \"continuous-integration/jenkins\", \"description\": \"Jenkins\", \"target_url\": \"http://20.194.0.141/job/dangdang_ci/$BUILD_NUMBER/console\"}"; \
fi
```    


   