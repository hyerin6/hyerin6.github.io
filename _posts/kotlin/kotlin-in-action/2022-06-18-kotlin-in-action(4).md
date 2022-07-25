---
layout: post
title: "kotlin in action chap4"    
description: "Kotlin Part 1"  
date: 2022-06-18   
tags: [kotlin]    
comments: true   
share: true 
---    


# 4. 클래스, 객체, 인터페이스   
## 4.1 클래스 계층 정의 
### 4.1.1 인터페이스 

#### 인터페이스 선언 

```kotlin
interface Clickable {
    fun click()
}
```

click 이라는 추상 메서드가 있는 간단한 인터페이스를 선언했다. 

<br />    

#### 인터페이스 구현   

```kotlin
class Button : Clickable {
    override fun click() = println("I was cliked")
}
```

자바에서 `extends`와 `implements` 키워드를 사용하지만 코틀린에서는 클래스 이름 뒤에 콜론(`:`)을 붙이고 인터페이스와 클래스 이름을 적는 것으로 클래스 확장과 인터페이스 구현을 모두 처리한다.


* 자바와 동일하게 여러 개의 인터페이스 구현 가능, 클래스는 한 개만 상속 가능   
* `override` 수식어: 인터페이스나 상위 클래스의 메서드, 프로퍼티 재정의시 꼭 사용해야 한다.

> 자바와 달리 코틀린에서는 override 변경자를 꼭 사용해야 한다.   
> 실수로 상위 클래스 메서드를 오버라이드하는 경우를 방지해준다.     

<br />

#### 디폴트 메서드 
```kotlin
interface Clickable {
    fun click() // 일반 메서드 선언 
    fun showOff() = println("I'm clickable!") // 디폴트 구현이 있는 메서드 
}
```

<br />

#### 동일한 메서드를 구현하는 다른 인터페이스 정의 
상황) 클래스가 구현하는 두 상위 인터페이스에 `showOff()` 라는 동일한 이름의 메서드가 있다.   

어느 쪽 showOff 메서드가 선택될까? 어느 쪽도 선택되지 않는다.   
showOff 구현을 대체할 오버라이딩 메서드를 직접 제공하지 않으면 다음과 같은 컴파일 오류가 발생한다.   

```kotlin
The class 'Button' must 
override public open fun showOff() because it inherits
many implementations of it.
```
 

다음은 상속한 인터페이스의 메서드 구현을 호출하는 코드이다.  

```kotlin
class Button : Clickable, Focusable {
    override fun click() = println("I was clicked") 
    
    // 애매모호함을 없애기 위해 재정의함. 
    // 이름과 시그니처가 같은 멤버 메서드에 대해 둘 이상의 디폴트 구현이 있는 경우  
    // 인터페이스를 구현하는 하위 클래스에서 명시적으로 새로운 구현을 제공해야 한다. 
    override fun showOff() {
        // super<타입>으로 사용할 상위 타입 지정 
        super<Clickable>.showOff()
    }
} 
```

Button 클래스는 두 인터페이스를 구현한다.   
Button은 상속한 두 상위 타입의 `showOff()` 메서드를 호출하는 방식으로 showOff를 구현한다.   
상위 타입의 구현을 호출할 때 자바와 동일하게 `super`를 사용한다.    

> 코틀린의 디폴트 메서드는 자바의 정적 메서드로 구현 ﴾코틀린은 자바 1.6과 호환﴿  

<br />  
<br />  

### 4.1.2 open, final, abstract 변경자: 기본적으로 final    
자바에서 final로 명시적으로 상속을 금지하지 않는 모든 클래스를 다른 클래스가 상속할 수 있다.


* 클래스와 클래스의 멤버는 기본적으로 final
    - 상속을 허용하려면 클래스 앞에 open 수식어를 붙여야 함
    - 오버라이딩을 허용하고 싶은 메서드나 프로퍼티 앞에도 open 변경자를 붙여야 함 클래스를 abstract로 선언하면 추상 클래스


* 추상 클래스는 인스턴스화할 수 없음
    - 추상 멤버는 항상 open임 ﴾추상 멤버 앞에 open을 붙일 필요 없음﴿












