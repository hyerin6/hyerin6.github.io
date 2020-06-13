


# AOP 란?     
AOP는 Aspect-Oriented Programming의 약자이고, 이를 번역하면 관점 지향 프로그래밍이 된다.   

스프링 DI가 의존성(new)에 대한 주입이라면 스프링 AOP는 로직(code) 주입이라고 할 수 있다.   

```
DB 커넥션 준비 
Statement 객체 준비 

try{
    DB 커넥션 연결 
    Statement 객체 세팅 
    insert / update / delete / select 실행 
} catch ... {
    예외처리 
} catch ...{
    예외처리 
} final {
    DB 자원 반납
}
```   

어떤 데이터베이스 연산을 하든 공통적으로 나타나는 코드가 있다. 
이를 바로 횡단 관심사라고 한다. 그리고 ```insert / update / delete / select 실행 ``` 이 부분을 핵심 관심사라고 한다.   

**코드 = 핵심 관심사 + 횡단 관심사**   

핵심 관심사는 모듈별로 다르지만 횡단 관심사는 모듈별로 반복되어 중복해서 나타나는 부분이다.   
반복/중복은 분리해서 한 곳에서 관리하라 !   
그런데 AOP에서는 더욱 진보적인 방법을 사용한다.   

로직을 주입한다면 어디에 주입할 수 있을까?  
객체지향에서 로직(코드)이 있는 곳은 메소드의 안쪽이다.   
메소드에 주입할 수 있는 곳은 총 5곳이다.   
- Around 메소드 전 구역    
- Before 메소드 시작 전 (시작 직후)     
- After 메소드 종료 후 (종료 직전)   
- AfterReturning 메소드 정상 종료 후     
- AfterThrowing 메소드에서 예외가 발생하면서 종료한 경우    


스프링 AOP의 핵심은 다음과 같다.   
- 스프링 AOP는 인터페이스 기반이다.  
- 스프링 AOP는 프록시 기반이다.  
- 스프링 AOP는 런타임 기반이다.    


# 용어 정리     
- Aspect 관점, 측면, 양상    
- Advisior 조언자, 고문  
- Adivice 조언, 충고  
- JoinPoint 결합점   
- Pointcut 자르는점  

#### 1. Pointcut - Aspect 적용 위치 지정자     
```@Before("execution(* runSomething())")``` 의 의미는 뭘까?  
지금 선언하고 있는 메소드를 ```* runSomething()```가 실행되기 전에 실행하라는 의미이다.   
여기서 ```public void before```는 횡단 관심사를 실행하는 메소드가 된다.   

결국 Pointcut이라고 하는 것은 횡단 관심사를 적용할 타깃 메소드를 선택하는 지시자(메소드 선택 필터)인 것이다.   
이를 줄여서 표현하면 "타깃 클래스의 타깃 메소드 지정자"라고 할 수 있다.   

스프링 AOP만 보자면 Aspect를 메소드에만 적용할 수 있으니 타깃 메소드 지정자라는 말이 틀리지 않다.   
다른 AOP 프레임워크에서는 메소드뿐만 아니라 속성 등에도 Aspect를 적용할 수 있기 때문에 그것까지 고려한다면   
Aspect 적용 위치 지정자(지시자)가 맞는 표현이다.   
Pointcut을 메소드 선정 알고리즘이라고도 한다.   

타깃 메소드 지정자에는 정규식과 AspectJ 표현식 등을 사용할 수 있다.   
```[접근제한자패턴] 리턴타입패턴 [패키지&클래스패턴.]메소드이름패턴(파라미터패턴) [throws 예외패턴]```   


#### 2. JoinPoint - 연결 가능한 지점    
Pointcut은 JoinPoint의 부분 집합이다.   
스프링 AOP는 인터페이스를 기반으로 한다고 설명했는데 그럼 인터페이스는 뭘까?  
인터페이스는 추상 메소드의 집합체이다. 그럼 삼단 논법에 의해 스프링 AOP는 메소드에만 적용 가능하다는 결론에 도달한다.   

JoinPoint란 "Aspect 적용이 가능한 모든 지점을 말한다."라고 결론 지을 수 있다.   

스프링 AOP에서 JoinPoint란 스프링 프레임워크가 관리하는 빈의 모든 메소드에 해당한다.    
이것이 광의의 JoinPoint다. 협의의 JoinPoint는 코드 상에서 확인할 수 있다.   

```romeo.runSomething()``` 메소드를 호출한 상태라면 JoinPoint는 romeo 객체의 runSomething() 메소드가 된다.   
JoinPoint 파라미터를 이용하면 실행 시점에 실제 호출된 메소드가 무엇인지, 실제 호출된 메소드를 소유한 객체가 무엇인지,   
또 호출된 메소드의 파라미터는 무엇인지 등의 정보를 확인할 수 있다.   

- 광의의 JoinPoint란 Aspect 적용이 가능한 모든 지점   
- 협의의 JoinPoint란 호출된 객체의 메소드  


#### 3. Advice - 조언? 언제, 무엇을   
Advice는 Pointcut에 적용할 로직, 즉 메소드를 의미한다. 여기에 언제라는 개념까지 포함하고 있다.   
Advice란 Pointcut에 언제, 무엇을 적용할지 정의한 메소드다.   


#### 4. Aspect - Advice의 집합체   
AOP에서 Aspect는 여러 개의 Advice와 여러 개의 Pointcut의 결합체를 의미하는 용어다.   
**Aspect = Advice들 + Pointcut들**  

Advice - 언제, 무엇을  
Pointcut - 어디에    
Aspect - 언제, 어디에, 무엇을   


#### 5. Advisor - 어디서, 언제, 무엇을   
Advisor은 다음과 같다.   
**Advisor = 한 개의 Adivice + 한 개의 Pointcut**  

Advisor는 스프링 AOP에서만 사용하는 용어이며 다른 AOP 프레임워크에서는 사용하지 않는다.   
또 스프링 버전이 올라가면서 이제는 쓰지 말라고 권고하는 기능이기도 하다.   
Aspect가 나왔기 때문에 하나의 Advice와 하나의 Pointcut만을 결합하는 Advisor를 사용할 필요가 없어졌기 때문이다.   


# POJO와 XML 기반 AOP   
@ 어노테이션 기반 - MyAspect.java가 스프링 프레임워크에 종속   
POJO & XML 기반 - 스프링 프레임워크에 종속되지 않음   

 