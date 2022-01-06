---
title: "스프링 예외 발생 위치와 처리 방법"
categories:
  - Spring 
tags:
  - spring 
toc: true
author_profile: true
toc_sticky: true
---



# 스프링 처리 과정 



![KakaoTalk_Photo_2021-08-17-23-00-42](https://user-images.githubusercontent.com/33855307/129752405-32d58181-4260-43ce-aa8d-2ece9164f964.jpeg)


스프링의 처리 과정을 보면 예외가 발생하는 부분은 크게 두 가지로 나눌 수 있다.    

1. DispatcherServlet 내에서 발생하는 예외 (Controller, Service, Repository 등) 
2. DispatcherServlet 전의 서블릿(Filter)에서 발생하는 예외   


<br />
<br />


# DispatcherServlet 내에서 발생한 예외   



DispatcherServlet 내에서 발생하는 예외는 HandlerExceptionResolver를 사용한 예외 전략으로 내부에서 자체적으로 해결할 수 있다.

`HandlerExceptionResolver` 사용은 다음 링크 참고

<https://jaehun2841.github.io/2018/08/30/2018-08-25-spring-mvc-handle-exception/#handlerexceptionresolver%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%B2%98%EB%A6%AC>  



<br />
<br />


# DispatchherServlet 전에 발생한 예외 



DispatcherServlet 전의 서블릿(Filter)에서 발생하는 예외는 `HandlerExceptionResolver`의 처리를 받을 수 없다. 

 Filter에서 예외가 발생하면 Web Application 레벨에서 처리를 해줘야 한다.

<br />

해결 방법은 다음과 같다. 

1. web.xml 에서 error-page 등록, 사용자에게 에러 응답 
2. Filter 내부 예외 처리를 위한 Filter 생성, try-catch문을 사용하여 예외 처리 
3. Filter 내부 try-catch문에서 발생한 예외를  HandlerExceptionResolver를 빈으로 주입받아 `@ExceptionHandler`에서 처리

<br />

# HandlerExceptionResolver의 예외 처리 방법 

 

* Controller Level: @ExceptionHandler

* Global Level: @ControllerAdvice

* Method Level: try/catch


<br />
<br />

### (1) Controller Level: `@ExceptionHandler` 

스프링에서 Controller에서 발생하는 예외를 공통적으로 처리할 수 있는 기능을 제공한다.

`@ExceptionHandler`애노테이션을 통해 Controller의 메서드에서 throw된 Exception에 대한 공통적인 처리를 할 수 있다.

TestController내에서 발생하는 TestException에 대해서 예외가 발생하면 `controllerExceptionHandler`메서드에서 모두 처리해준다.
<br />


Controller 메서드 내의 하위 서비스 (Service, Repository등등)에서 예외가 발생하더라도, 

중간에 처리하지 않는 이상 Controller단까지 예외가 던져지게 되고 `@ExceptionHandler`가 예외를 처리하게 된다.

Checked Exception, Runtime Exception 상관 없이 Controller까지 예외를 throw하면 처리가 가능하다.



```
@RestController
public class TestController {

    // 예외 핸들러
    @ExceptionHandler(value = TestException.class)
    public String controllerExceptionHandler(Exception e) {
        log.error(e.getMessage());
        return "/error/404";
    }

    @GetMapping("hello1")
    public String hello1() {
        throw new TestException("hello1 에러 "); // 예외 발생
    }

    @GetMapping("hello2")
    public String hello2() {
        throw new TestException("hello2 에러 "); // 예외 발생
    }
}
```





<br />
<br />


### (2) Global Level: `@ControllerAdvice` 

하나의 Controller가 아닌 여러 Controller에서 발생하는 예외를 처리하려면 `@ControllerAdvice`를 사용해야 한다.

`@ControllerAdvice`는 모든 Controller에서 발생하는 예외를 처리할 수 있게 해주는 애노테이션이다.  

DispatcherServlet에서 발생하는 예외를 전역적으로 처리해준다. 

DispatcherServlet에서 발생하는 예외만 처리할 수 있고 Filter에서 발생하는 예외는 따로 처리를 하지 않으면 처리가 불가능하다. 



Q. Controller의 `@ExceptionHandler`와 ControllerAdvice의 `@ExceptionHandler`중 높은 우선순위는? 

A. Controller의 `@ExceptionHandler`가 먼저다. 




<br />
<br />



### (3) Method Level: try/catch

생략
