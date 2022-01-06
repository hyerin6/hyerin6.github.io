---
layout: post
title: "spring form validation"  
description: "입력 폼에 입력된 내용의 오류 자동 검사"
date: 2020-02-14
tags: [spring]
categories: [Spring]
comments: true
share: true
---

# Spring Form Validation 1         

### 1. 배경지식   
#### (1) spring form validation       
입력폼에 입력된 내용의 오류를 spring이 자동으로 검사해주는 기능이다.         

**입력폼 submit 과정 #1**                         
spring web mvc로 구현한 입력폼의 submit 과정은 다음과 같다.           

1. 사용자가 웹 브라우저에서 입력폼에 데이터를 입력하고 submit 버튼을 누른다.       

2. 서버의 url이 요청된다. (http request)              
입력폼에 입력된 데이터도 이 요청에 같이 담겨 전송된다. (request parameter)            

3. spring web mvc 엔진이 그 요청을 받아서 요청된 url과 일치하는 액션메소드를 찾는다.               
```java
@RequestMapping(value="studentEdit", method=RequestMethod.POST)
public String studentEdit(@valid Student student, Model model,
						BingingResult bindingResult){ ... }
```

spring form validation 기능을 구현하기 위해,   
@valid 어노테이션과 BindingResult 객체가 추가되었다.     
검사결과를 bindingResult에 채워진 후에 액션 메소드가 호출된다.      

4. 위 액션메소드의 파라미터가 객체이기 때문에 spring web mvc 엔진이 아래의 일들을 자동으로 처리한다.         
```java
Student student = new Student(); // Student 객체 생성
student.setStudentNumber("201732017"); // 생성된 객체에 request parameter 데이터를 채운다.  
model.addAttribute("student", student); // 객체를 model 객체에 등록한다.   
```

5. Student 객체에 채워진 데이터에 문제가 없는지 검사한다.      
데이터를 검사할 규칙이 Student 클래스에 등록되어 있어야 한다.      
```java
public class Student{
	@NotEmpty 
	@Size(min=9, max=12)
	Stirng studentNumber;
	
	@NotEmpty
	@Size(min=2)
	String name;
```
spring form validation 기능을 구현하기 위해 @NotEmpty, @Size 어노테이션이 추가되었다.           
Student 객체에 채워진 데이터가 이 어노테이션 규칙이 부합하는지 spring web mvc 엔진이 검사한다.     
검사결과가 BindingResult 객체에 등록한다.      

6. 액션메소드가 호출된다.       
``` studentEdit(student, model, bindingResult) ```       


#### (2) 모델 클래스(model)       
- request parametet 데이터를 채우기 위한 객체      
앞에서 설명한 입력폼 submit 과정에서 사용된 Student 클래스 처럼,              
request parameter 데이터를 채우기 위한 클래스를 모델 클래스라고 부른다.              

- model 객체에 채워져 뷰에 전달되기 위한 객체        
spring form validation 규칙을 설정하기 위한 어노테이션을 model 클래스에 추가해야 하는데,       
그 어노테이션들은 Entity 클래스에서는 사용할 수 없다.         

**DTO - 데이터를 채워서 전달**           
**model - 입력 받은 값을 view에서 controller에 전달**                
**Entity - DB 테이블 구조 그대로**    

#### (3) 엔터티 클래스       
JPA 프로그래밍에서 구현하게 되는 @Entity 어노테이션이 붙은 클래스를 엔터티 클래스라고 부른다.         
엔터티 객체는 JPA Repository의 조회결과 데이터를 리턴할 때 사용되는 객체이다.        
JPA Repository의 save 메소드를 호출하여 데이터를 저장할 때도 엔터티 객체를 사용한다.        


#### (4) 모델 클래스와 엔터티 클래스         
예를들어, 학생 테이블의 경우에 아래의 클래스들을 따로 구현한다.          
모델 클래스 - StudentModel.java       
엔터티 클래스 - Student.java         

spring form validation 기능을 구현하기 위해서는 엔터티 클래스와 모델 클래스를 구별해서 따로 구현해야 한다.       
spring form validation 기능을 구현하려면 @NotEmpty, @Size 어노테이션을 사용해야 하는데,      
이 어노테이션들을 엔터티 클래스에 붙이는 것은 바람직하지 않기 때문이다. (중복이 많아지면 상속해서 구현한다.)           

#### (5) 객체지향 설계          
객체지향적으로 바람직한 구조를 만들기 위한 설계 원칙 중 하나는, 클래스들의 역할을 분명히 구분하는 것이다.           

- 컨트롤러의 역할    
컨트롤러의 역할은 이름 그대로 지휘 통제하는 것이다.       
지휘 통제 역할만 해야 한다. 구체적으로 작업을 하는 것은 바람직하지 않다.             

- DAO(Data Access Object) 클래스의 역할         
DAO의 역할은 데이터베이스 테이블에 SELECT/INSERT/UPDATE/DELETE 하는 작업을 하는 것이다.         

- Service 클래스의 역할       
DAO 작업을 제외한 나머지 작업들은 서비스 클래스에 구현되어야 한다.         


### 7. 컨트롤러 클래스 구현         
#### (1) UserController.java         
``` @RequestMapping(value="register", method=RequestMethod.GET) ```        
회원가입 입력폼이 처음 실행될 때, GET 방식의 register 액션 메소드가 실행된다.        


``` model.addAttribute("departments", departmentService.findAll()); ```       
user/register.jsp 뷰 파일을 실행할 때, 학과 목록이 출력되어야 하기 때문에       
학과 목록이 모델이 들어있어야 한다.         


``` @RequestMapping(value="register", method=RequestMethod.POST) ```            
submit 버튼을 눌렀을 때, POST 방식의 register 액션 메소드가 실행된다.       


```java
if(bindingResult.hasErrors()){
	model.addAttribute("departments", departmentService.findAll());
	return "user/register";
```
userModel 모델 객체에 채워진 데이터에 오류가 있다면, if문이 true가 된다.       
회원가입 입력 항목에 에러가 있기 때문에 회원가입 입력폼이 다시 화면에 나타나야 한다.          
그래서 "user/register" 뷰 이름을 리턴한다.            


```java
userService.save(userModel);
return "redirect:registerSuccess";
```
데이터가 오류가 없으면, UserRegistrationModel 객체에 입력된 데이터를 user 테이블에 저장한다.         
그리고 회원가입 성공 화면으로 리다이렉트한다.        


### 8. 뷰 구현      
#### (1) user/register.jsp        
```
<form:form method="post" modelAttribute="userRegistrationModel">
	...
	<form:errors path="userid" class="error" />
```
userid 데이터 항목과 관련된 에러 메시지가 자동으로 여기에 표시된다.         


## 코드 분석           
**Q.** user/register.jsp 뷰 파일은 어떤 메소드의 뒤에 이어서 실행되는가?       
**A.** register GET, POST 방식 메소드        

**Q.** GET 방식 액션 메소드에서 userRegistrationModel 객체를 모델에 어떻게 등록하였는가?         
**A.** userRegistrationModel 객체를 파라미터로 받아,        
spring web mvc 엔진이 객체 생성 객체에 전달받은 파라미터 값 저장, model에 등록을 자동을 해준다.        

**Q.** POST 방식 액션 메소드에서 userRegistrationModel 객체를 모델에 어떻게 등록하였는가?     
**A.** 파라미터로 객체를 전달받으면 자동으로 model에 등록된다.   

**Q.** GET 방식 액션 메소드에서 departments 객체를 모델에 어떻게 등록하였는가?         
**A.** Model 객체에 addAttribute() 메소드를 이용하여 등록할 수 있다.         

**Q.** POST 방식 액션 메소드에서 departments 객체를 모델에 어떻게 등록하였는가?       
**A.** Model 객체에 addAttribute() 메소드를 이용하여 등록할 수 있다.    


### 11. 사용자 아이디 중복 검사 기능 구현     
user 테이블의 userid 필드      
이 필드는 primary key 가 아니다. 이 필드로 unique index를 만들지도 않았다.                 
그렇기 때문에 동일한 값이 INSERT 되어도 에러가 발생하지 않는다.         

중복 방지를 위해 unique index를 만들어 주는게 좋다. 하지만 DB에 삽입하기 전에 먼저 검사해서        
입력폼에서 에러를 표시해 주는 것이 바람직하다.         


<br/>  
---      
<br/>  


# Spring Form Validation 2           

### 1. validation message 수정        
#### (1) 목표     

<img width="536" alt="스크린샷 2019-12-03 오후 11 17 42" src="https://user-images.githubusercontent.com/33855307/70058923-29d96180-1623-11ea-9632-bb2190683411.png">

위 화면에서 자동으로 출력된 에러 메시지 문구를 수정하자.      

각 validation annotation에 대한 에러 메시지를 ValidationMessage.properties 파일에 등록해 주면 된다.     

### (2) ValidationMessage.properties 생성          

```
javax.validation.constraints.Size.message=크기가 {min} 이상 {max} 이하이어야 합니다.
org.hibernate.validator.constraints.Email.message=이메일 주소가 바르지 않습니다.
org.hibernate.validator.constraints.NotEmpty.message=필수 입력항목입니다. 
```
javax.validation.constraints.Size.message 는 @Size 어노테이션 클래스의 경로명                   
org.hibernate.validator.constraints.Email.message 는 @Email 어노테이션 클래스의 경로명           
org.hibernate.validator.constraints.NotEmpty.message 는 @NotEmpty 어노테이션 클래스의 경로명             

### 2. 개별 항목에 대한 에러 메시지 등록          
#### (1) 목표            
@NotEmpty 어노테이션에 위배되는 모든 항목에 "필수 입력 항목입니다." 에러메시지가 동일하게 출력된다.      
항목에 따라서 다른 에러 메시지가 표시되어야 하는 경우에는         
@NotEmpty 어노테이션에 message 애트리뷰트 값으로 에러 메시지를 등록하면 된다.      

#### (2) message 애트리뷰트     
어떤 항목의 @NotEmpty 에러 메시지를 다른 것으로 바꾸려면,      
이 어노테이션에 message 애트리뷰트 값으로 에러 메시지를 등록하면 된다.      

Example
```java
@NotEmpty(message="학번을 입력하세요")
@Size(min=6, max=12, message="6 자리 이상 12 자리 이하이어야 합니다.")
@Min(value=1, message="양의 정수를 입력하세요")
```

``` @Min(1) ```     
위와같이 값이 한개인 어노테이션에서 그 값 한개인 애트리뷰트 이름은 value이다.       
애트리뷰트 값이 하나 뿐일 때, value 이름을 생략할 수 있다.      
하지만, 값이 여러개 일 때는 애트리뷰트 이름을 생략할 수 없다. -> ``` @Min(value=1, message="error message") ```        


### 3. 로직 에러 처리하기         
#### (1) 목표     
validation annotation에 의해서 자동으로 검사되기 힘든 에러들도 있다.     
예를들어, 사용자 아이디 중복을 검사하려면 DB의 user 테이블을 조회해야 한다.      
입력된 두 비밀번호가 일치하는지 검사하려면, 두 멤버변수를 비교해야 한다.       
이런 에러 검사는 validation annotation으로 구현하기 힘들고 따로 직접 구현해야 한다.      

- 로직 에러 처리하기 절차      

(1) spring form validation 에러가 있다면, 회원가입 뷰 이름을 리턴한다.    
회원가입 뷰 이름이 리턴되면 웹 브라우저 창에 회원가입 화면이 다시 출력된다.   

(2) 입력된 두 비밀번호가 일치하는지 검사한다.    

(3) 일치하지 않으면 비밀번호 불일치 에러이다.   
&emsp;	(3-1) bindingResult 객체에 비밀번호 불일치 에러메시지를 등록한다.   
&emsp;	(3-2) 회원가입 뷰 이름을 리턴한다.    

(4) 입력된 사용자 아이디로 DB의 사용자 테이블에서 조회한다.    

(5) 조회결과가 null이 아니면, 사용자 아이디 중복 에러이다.   
&emsp;	(5-1) bindingResult 객체에 사용자 아이디 중복 에러 메시지를 등록한다.   
&emsp;	(5-2) 회원가입 뷰 이름을 리턴한다.        


- bindingResult 객체에 에러 메시지 등록하기   

```bindingResult.rejectValue("password2", null, "비밀번호가 일치하지 않습니다.");```  
rejectValue 메소드를 호출하여 에러 메시지를 등록한다.      

첫번째 파라미터 : 에러가 발생한 멤버변수 이름    
세번쨰 파라미터 : 에러 메시지        

bindingResult에 위 에러가 등록되면, 아래 태그에 그 에러 메시지가 출력된다.           
``` <form:errors path="password2" class="error" /> ```           


#### (2) UserService.java        
```java
public boolean hasErrors(UserRegistrationModel userModel, BindingResult bindingResult){
	if(bindingResult.hasErrors()){
		return true;
	}
		
	...

}
```

#### (3) UserController.java           
```java
public String register(...){
	if(userService.hasErrors(userModel, bindingResult)){
		return "user/register";
	}

	...

}
```
**Q.** 아까 입력했던 내용이 view에 어떻게 나타나는걸까?    
**A.** 액션 메소드의 파라미터가 객체일 때, 자동으로 일어나는 일은      
userModel이 model에 자동으로 addAttribute 된다. (userRegistration이라는 이름으로)        




