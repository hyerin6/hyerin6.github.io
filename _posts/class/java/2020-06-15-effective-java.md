---
layout: post
title: "이펙티브 자바 (1)"       
description: "객체 생성과 파괴"
date: 2020-06-15
tags: [java]
comments: true
share: true
---   

객체를 만들어야 할 때와 만들지 말아야 할 때를 구분하는 법      
올바른 객체 생성 방법과 불필요한 생성을 피하는 방법     
제때 파괴됨을 보장하고 파괴 전에 수행해야 할 정리 작업을 관리하는 법    

---

# 1. 생성자 대신 정적 팩토리 메서드를 고려하라.     

클라이언트가 클래스의 인스턴스를 얻는 전통적인 수단은 public 생성자다.     
클래스는 생성자와 별도로 정적 팩토리 메서드를 제공할 수 있다.   
그 클래스의 인스턴스를 반환하는 단순한 정적 메서드이다.   

<br/>     


이 방식에는 장점과 단점이 모두 존재한다.   
먼저 장점 다섯가지를 알아보자.   


- 이름을 가질 수 있다.    

생성자에 넘기는 매개변수와 생성자 자체만으로 반환될 객테의 특성을 제대로 설명하지 못한다.   
반면 정적 팩토리 메서드는 이름만 잘 지으면 반환될 객테의 특성을 쉽게 묘사할 수 있다.   

<br/>     

- 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.   

이 덕분에 불변 클래스(immutable class)는 인스턴스를 미리 만들어 놓거나   
새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객테 생성을 피할 수 있다.   

따라서 같은 객체가 자주 요청되는 상황이라면 성능을 상당히 끌어올려 준다.    
정적 팩토리 방식의 클래스는 언제 어느 인스턴스를 살아 있게 할지를 철저히 통제할 수 있고   
이를 인스턴스 통제 클래스라고 한다.   

인스턴스를 통제하는 이유는 뭘까?    
인스턴스를 통제하면 클래스를 싱글톤으로 만들 수도 있고 인스턴스화 불가로 만들 수도 있다.   
또한 불변 값 클래스에서 동치인 인스턴스가 단 하나뿐임을 보장할 수 있다.    

<br/>     

- 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.    

이는 반환 객체의 클래스를 자유롭게 선택할 수 있게 하는 '엄청난 유연성'을 선물한다.     

<br/>     

- 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.   

반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다.   
심지어 다음 릴리스에서는 또 다른 클래스의 객체를 반환해도 된다.     

<br/>     

- 정적 팩토리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.     

이런 유연함은 서비스 제공자 프레임워크를 만드는 근간이 된다.     
클라이언트에 제공하는 역할을 프레임워크가 통제하여, 클라이언트를 구현체로부터 분리해준다.   

서비스 제공자 프레임워크는 3개의 핵심 컴포넌트로 이루어진다.   
서비스 인터페이스, 제공자 등록 API, 서비스 접근 API   
 
3개의 핵심 컴포넌트와 더불어 종종 서비스 제공자 인터페이스라는 네 번째 컴포넌트가 쓰이기도 한다.   
이 컴포넌트는 서비스 인터페이스의 인스턴스를 생성하는 팩토리 객체를 설명해준다.   
서비스 제공자 인터페이스가 없다면 각 구현체를 인스턴스로 만들 때 리플렉션을 사용해야 한다.   

의존 객체 주입도 강력한 서비스 제공자라고 생각할 수 있다.   

<br/>     

단점을 알아보자.    


- 상속을 하려면 public 이나 protected 생성자가 필요하니 정적 팩토리 메서드만 제공하면   
하위 클래스를 만들 수 없다.   

컬렉션 프레임워크의 유틸리티 구현 클래스들은 상속할 수 없다는 이야기다.     
어찌 보면 이 제약은 상속보다는 컴포지션을 사용하도록 유도하고     
불변 타입으로 만들려면 이 제약을 지켜야 한다는 점에서 오히려 장점으로 받아들일 수도 있다.     

<br/>     

- 정적 팩토리 메서드는 프로그래머가 찾기 어렵다.   

생성자처럼 API 설명에 명확히 드러나지 않으니    
사용자는 정적 팩토리 메서드 방식 클래스를 인스턴스화할 방법을 알아내야 한다.     



<br/>     

**핵심 정리**      

정적 팩토리 메서드와 public 생성자는 각각의 쓰임새가 있으니         
상대적인 장단점을 이해하고 사용하는 것이 좋다.       
그렇다고 하더라도 정적 팩토리를 사용하는 게 유리한 경우가 더 많으므로     
무작정 public 생성자를 제공하는 습관은 좋지 않다.       

<br/>     

# 2. 생성자에 매개변수가 많다면 빌더를 고려하라.   

자바빈즈 패턴(setter 메서드 호출)에서는 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고,     
객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.     
일관성이 무너지는 문제 때문에 자바빈즈 패턴에서는 클래스를 불변으로 만들 수 없으며       
스레드 안전성을 얻으려면 프로그래머가 추가 작업을 해줘야만 한다.                

이러한 단점을 완화하고자 생성이 끝난 객테를 수동으로 얼리고 얼리기 전에 사용할 수 없도록 하기도 한다.   
그러나 `freeze` 메소드를 확실히 호출했는지 컴파일러가 보증할 방법이 없어서 런타임 오류에 취약하다.   

세 번째 대안은 점층적 생성자 패턴의 안전성과 자바빈즈 패턴의 가독성을 겸비한 빌더 패턴이다.      

클라이언트는 필요한 객체를 직접 만드는 대신 필수 매개변수만으로 생성자(혹은 정적 팩토리)를 호출해 빌더 객체를 얻는다.   
그런 다음 빌더 객체가 제공하는 일종의 setter 메서드들로 원하는 선택 매개변수들을 설정한다.  

빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다.   

빌더 패턴에 장점만 있는 것은 아니다. 객체를 만들려면 그에 앞서 빌더부터 만들어야 한다.   
빌더 생성 비용이 크지는 않지만 성능에 민감한 상황에서는 문제가 될 수 있다.   


<br/>     

**핵심 정리**    

생성자나 정적 팩토리가 처리해야 할 매개변수가 많으면 빌더 패턴을 선택하는 게 낫다.   

<br/>     


# 3. private 생성자나 열거 타입으로 싱글톤임을 보증하라.   

싱글톤이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.     
그런데 클래스를 싱글톤으로 만들면 이를 사용하는 클라리언트를 테스트하기 어려워질 수 있다.     

싱글톤을 만드는 방식은 보통 둘 중에 하나이다.   
두 방식 모두 생성자는 private로 감춰두고 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련해둔다.      

첫 번째 방법으로는 public이나 protected 생성자가 없다면 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.   

그러나 권한이 있는 클라이언트는 리플렉션 API인 AccessibleObject.setAccessible을 사용해   
private 생성자를 호출할 수 있다.   
이러한 공격을 방어하려면 생성자를 수정하여 두 번째 객체가 생성되려고 할 때 예외를 던지게 하면 된다.     

싱글톤을 만드는 두 번재 방법에서는 정적 팩토리 메서드를 public static 멤버로 제공한다.   
항상 같은 객체를 반환하므로 제2의 인스턴스는 만들어지지 않는다. (리플렉션을 통한 예외는 똑같이 적용된다.)  

public 필드 방식은 다음과 같은 장점이 있다.          
- 해당 클래스가 싱글톤임이 API에 명백히 드러나다.            
 
정적 팩토리 방식의 장점         
- API를 바꾸지 않고도 팩토리 메서드가 호출하는 스레드별로 다른 인스턴스를 넘겨주게 할 수 있다.           
- 정적 팩토리를 제네릭 싱글톤 팩토리로 만들 수 있다.     
- 정적 팩토리의 메서드 참조를 공급자로 사용할 수 있다는 점이다.     

정적 팩토리 방식의 장점들이 필요없다면 public 필드 방식이 좋다.       


싱글톤 클래스를 직렬화하려면 단순히 Serializable을 구현한다고 선언하는 것만으로는 부족하다.   
모든 인스턴스 필드를 일시적이라고 선언하고 readResolve 메서드를 제공해야 한다.   
이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어진다.   



```java   
// 싱글톤임을 보장해주는 readResolve 메서드   
private Object readResolve() {
    return INSTANCE;
}
```



싱글톤을 만드는 세 번째 방법은 원소가 하나인 열거 타입을 선언ㅇ하는 것이다.    
public 필드 방식과 비슷하지만 더 간결하고 추가 노력 없이 직렬화할 수 있다.        
심지어 리플렉션 공격이나 직렬화 상황에서 제2의 인스턴스가 생기는 것을 완벽하게 막아준다.          
개부분 상황에서 원소가 하나뿐인 열거 타입이 싱글톤을 만드는 가장 좋은 방법이다.        
단, 만들려는 싱글톤이 Enum 외의 클래스를 상속해야 한다면 불가능하다.      

<br/>     

# 4. 인스턴스화를 막으려거든 private 생성자를 사용하라.   

정적 멤버만 담은 유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한게 아니다.      
하지만 생성자를 명시하지 않으면 컴파일러가 자동으로 기본 생성자를 만들어준다.      
의도치 않게 인스턴스화할 수 있게 된 클래스가 종종 목격된다.   

추상 클래스로 만드는 것으로는 인스턴스화를 막을 수 없다.   
하위 클래스를 만들어 인스턴스화하면 그만이다.   

컴파일러가 기본 생성자를 만드는 경우는 오직 명시된 생성자가 없을 때뿐이니 private 생성자를 추가하면 클래스의 인스턴스화를 막을 수 있다.   

이 방식은 상속을 불가능하게 하는 효과도 있다.   
모든 생성자는 명시적이든 묵시적이든 상위 클래스의 생성자를 호출하게 되는데, 이를 private으로 선언했으니   
하위 클래스가 상위 클래스의 생성자에 접근할 길이 막혀버린다.   


<br/>     

# 5. 자원을 직접 명시하지 말고 의존 객체를 주입하라.      

사용하는 자원에 따라 동작이 달라지는 클래스에 정적 유틸리티 클래스나 싱글톤 방식이 적합하지는 않다.     
클래스가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원을 사용해야 한다.   
이 조건을 만족하는 간단한 패턴이 있다. 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식이다.   
이는 의존 객체 주입의 한 형태이다.   

의존 객체 주입 패턴은 불변을 보장하여 (같은 자원을 사용하려는) 여러 클라이언트가   
의존 객체들을 안심하고 공유할 수 있기도 하다.   

<br/>     

**핵심 정리**  

클래스가 내부적으로 하나 이상의 자원에 의존하고 그 자원이 클래스 동작에 영향을 준다면    
싱글톤과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다.   
이 자원들을 클래스가 직접 만들게 해서도 안된다.   
대신 필요한 자원을 생성자에 (혹은 정적 팩토리나 빌더에) 넘겨주자.     
의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 개선해준다.   



<br/>     



# 6. 불필요한 객체 생성을 피하라.    

```java
String s = "hello";
```

위 코드는 같은 가상 머신 안에서 이와 똑같은 문자열 리터럴을 사용하는 모든 코드가 같은 객체를 재사용함이 보장된다.      


생성자 대신 정적 팩토리 메서드를 제공하는 불변 클래스에서는 정적 팩토리 메서드를 사용해 불필요한 객테 생성을 피할 수 있다.   
예를들어, `Boolean(String)` 생성자 대신 `Boolean.valueOf(String)` 팩토리 메서드를 사용하는 것이 좋다.    

생성자는 호출할 때마다 새로운 객체를 만들지만 팩토리 메서드는 전혀 그렇지 않다.   




<br/>  
