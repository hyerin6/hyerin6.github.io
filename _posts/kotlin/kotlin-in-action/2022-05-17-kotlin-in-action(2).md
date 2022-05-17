---
layout: post
title: "kotlin in action chap2"    
description: "Kotlin Part 1"  
date: 2022-05-17   
tags: [kotlin]    
comments: true   
share: true 
---    


# 2. 코틀린 기초   
### 기본 요소: 함수와 변수   
#### (1) Hello, World! 
코틀린에서 함수 하나로 다음 프로그램을 만들 수 있다.   

```kotlin
fun main(args: Array<String>) {
    println("Hello, world!")
}
```

* 함수를 선언할 때 `fun` 키워드를 사용한다.     
* 파라미터 이름 뒤에 그 파라미터 타입을 쓴다. (변수를 선언할 때도 마찬가지)
* 함수를 최상위 수준에 정의할 수 있다. (자바와 달리) 꼭 클래스 안에 함수를 넣어야 할 필요가 없다. 
* 배열도 일반적인 클래스와 마찬가지다. 코틀린에는 자바와 달리 배열 처리를 위한 문법이 따로 존재하지 않는다. 
* `System.out,println` 대신 `println`라고 쓴다. 표준 자바 라이브러리 함수를 간결하게 사용할 수 있게 감싼 래퍼를 제공한다.  
* 세미콜론(;)을 붙이지 않아도 된다. 

<br />
<br />


#### (2) 함수 
위에서 아무런 값도 반환하지 않는 함수를 선언했다.   
의미 있는 결과를 반환하는 함수의 경우 반환 값의 타입을 어디에 지정해야 할까?     

```
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}
```

코틀린 `if`는 (값을 만들어내지 못하는) 문장이 아니고 결과를 만드는 식(expression) 이라는 점이 흥미롭다.    

위 `if` 식은 자바 3항 연산자로 작성한 `(a > b) ? a : b` 식과 비슷하다.   

함수를 좀 더 간결하게 표현할 수도 있다.   
함수 본문이 하나로만 이뤄져있는 경우 중괄호, return 을 제거하고 등호(=)를 붙이는 것이다.   

```kotlin
fun max(a: Int, b: Int): Int = if (a > b) a else b
```

여기서 반환 타입을 생략할 수 있는데 그 이유는 뭘까?   
코틀린은 정적 지정 언어이므로 컴파일 시점에모든 식의 타입을 지정해야 한다.  
실제로 모든 변수나 모든 식에는 타입이 있으며, 모든 함수는 반환 타입이 정해져야 한다.  
그러나 식이 본문인 경우 굳이 사용자가 반환 타입을 적지 않아도 컴파일러가 함수 본문 식을 분석해 식의 결과 타입을 함수 반환 타입으로 정해준다.   
이러한 기능을 타입 추론이라 부른다.  

<br />
<br />

#### (3) 변수 
코틀린에서는 타입 지정을 생략하는 경우가 흔하다.   
타입으로 변수 선언을 시작하면 타입을 생략할 경우 식과 변수 선언을 구별할 수 없다.    

```kotlin
val question = "삶, 우주, 그리고 모든 것에 대한 궁극적인 질문"

val answer = 42
```

위 예제에서 타입 표기를 생략했지만 원한다면 타입을 명시해도 된다.  

```kotlin
val answer: Int = 42
```

식이 본문인 함수에서와 마찬가지로 타입을 지정하지 않으면 컴파일러가 초기화 식을 분석해서 초기화 식의 타입을 변수 타입으로 지정한다.  

```kotlin
val yearsToCompute = 7.5e6 
```

부동소수점 상수를 사용한다면 변수 타입은 Double이 된다. 

<br />

초기화 식을 사용하지 않고 변수를 선언하려면 변수 타입을 반드시 명시해야 한다.   

```kotlin
val answer: Int
answer = 42
```

<br /> 

**변경 가능한 변수와 변경 불가능한 변수**       
변수 선언 시 사용하는 키워드 2가지가 있다.   
* `val` - 변경 불가능한 참조를 저장하는 변수  
  `val`로 선언된 변수는 일단 초기화하고 나면 재대입이 불가능하다. 
  자바로 말하자면 `final` 변수에 해당한다.   


* `var` - 변경 가능한 참조다. 이런 변수의 값은 바뀔 수 있다. 
  자바의 일반 변수에 해당한다.  


<br /> 

기본적으로는 모든 변수를 `val` 키워드를 사용해 불변 변수로 선언하고, 나중에 꼭 필요할 때에만 `var`로 변경한다.   
변경 불가능한 참조와 변경 불가능한 객체를 부수 효과가 없는 함수와 조합해 사용하면 코드가 함수형 코드에 가까워진다.   

`val` 변수는 블록을 실행할 때 정확히 한 번만 초기화돼야 한다.   
하지마 어떤 블록이 실행될 때 오직 한 초기화 문장만 실행됨을 컴파일러가 확인할 수 있다면 조건에 따라 `val`을 여러 값으로 초기화할 수도 있다.   

```kotlin
val message: String 
if(canPerformOperation()) {
    message = "Success"
    //  연산 수행
}
else {
    message = "Failed"
}
```

`val` 참조 자체는 불변일지라도 그 참조가 가리키는 객체의 내부 값은 변경될 수 있다는 사실을 기억하자.   
예를 들어 다음 코드도 올바른 코틀린 코드다.   

```kotlin
val languages = arrayListOf(Java") // 불변 참조를 선언한다. 
languages.add("Kotlin") // 참조가 가리키는 객체 내부를 변경한다.  
```


`var` 키워드를 사용하면 변수의 값을 변경할 수 있지만 변수의 타입은 고정돼 바뀌지 않는다.   
다음과 같은 코드는 컴파일할 수 없다.  

```kotlin
var answer = 42 
answer = "no answer" // Error: type mismatch 컴파일 오류 발생 
```

<br />
<br />

#### (4) 더 쉽게 문자열 형식 지정: 문자열 템플릿  
사람 이름을 사용해 환영 인사를 출력하는 코틀린 프로그램이다.  

```kotlin
fun main(args: Array<String>) {
    val name = if (args.size > 0) args[0] else "Kotlin"
    println("Hello, $name!") // 인자로 넘어온 값이 있으면 출력, 없으면 Kotlin 출력 
}
```

위 예제는 문자열 템플릿이라는 기능을 보여준다.    
`name` 이라는 변수를 선언하고 그 다음 줄에 있는 문자열 리터럴 안에서 그 변수를 사용했다.  
코틀린에서도 변수 앞에 `$`를 붙여 변수를 문자열 안에 사용할 수 있다.   
이 문자열 템플릿은 자바의 문자열 접합 연산(`+`)과 동일한 기능을 하지만 좀 더 간결하며,   

> 컴파일러는 각 식을 정적으로 (컴파일 시점에) 검사하기 때문에 존재하지 않는 변수를 문자열 템플릿 안에서 사용하면 컴파일 오류가 발생한다.   


`$` 문자열 템플릿 안에 사용할 수 있는 대상은 간단한 변수 이름만으로 한정되지 않는다.    
복잡한 식도 중괄호(`{}`)로 둘러싸서 문자열 템플릿 안에 넣을 수 있다.   

```kotlin
fun main(args: Array<String>) {
    if (args.size > 0) {
        println("Hello, ${args[0]}!") // ${} 구문 사용 
    }
}
```

중괄호로 둘러싼 식 안에서 큰 따옴표를 사용할 수도 있다.   

```kotlin
fun main(args: Array<String>) {
    println("Hello, ${if (args.size > 0) args[0] else "someone"}!")
}
```


<br />
<br />

### 클래스와 프로퍼티   

* 간단한 자바 클래스 

```java
public class Person {
    private final String name;
    
    . . .
}
```


* 코틀린으로 변환한 Person 클래스 

```kotlin
class Person(val name: String)
```

이런 유형의 클래스(코드 없이 데이터만 저장하는 클래스)를 값 객체라 부른다.   


자바를 코틀린으로 변환한 결과, public 가시성 변경자가 사라졌다.   
코틀린의 기본 가시성은 public 이므로 이런 경우 변경자를 생략해도 된다.   

<br />
<br />

#### (1) 프로퍼티   
클래스라는 개념의 목적은 데이터를 캡슐화하고 캡슐화한 데이터를 다루는 코드를 한 주체 아래 가두는 것이다.   

자바에서 필드와 접근자를 프로퍼티라 부른다.   
코틀린은 프로퍼티 언어 기본 기능을 제공하며, 코틀린 프로퍼티는 자바의 필드와 접근자 메서드를 완전히 대신한다.   

클래스에서 프로퍼티를 선언할 때는 `val`, `var`을 사용한다.     
* `val` 선언한 프로퍼티는 읽기 전용 
    - 읽기 전용은 비공개 필드와 공개 Getter를 만든다.   
  

* `var` 선언한 프로퍼티는 변경 가능하다.   
    - 쓰기가 가능한 프로퍼티로 비공개 필드, 공개 Getter, 공개 Setter를 만든다. 





```kotlin
class Person {
    val name: String, 
    var isMarried: Boolean 
}
```



코틀린은 값을 저장하기 위한 비공개 필드와 그 필드에 값을 저장하기 위한 Setter,      
필드의 값을 읽기 위한 Getter로 이뤄진 간단한 디폴트 접근자 구현을 제공한다.       

코틀린은 코드를 보면 간결해 보이는데 클래스 정의 뒤에 자바 코드와 똑같은 구현이 숨어있다.   
Person에는 비공개 필드가 들어있고, 생성자가 그 필드를 초기화하며, Getter를 통해 그 비공개 필드에 접근한다.   


* `name`: `getName()`, `setName()`     
* `isMarried`   
    - Getter: (`is-`로 시작하면 `get-` 대신 `is-`로 시작한다.) `isMarried()`  
    - Setter: (Setter은 `is-`를 지우고 `set-`로 시작한다.) `setMarried()`  

```kotlin
val person = Person("Bob", true) // new 키워드를 사용하지 않고 생성자를 호출한다. 
println(person.name) // 프로퍼티 이름만 직접 사용해도 코틀린이 자동으로 getter를 호출해준다. 
println(person.isMarried)
```

<br />
<br />

#### (2) 커스텀 접근자 
프로퍼티 접근자를 직접 작성해보자.   

```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean 
      get() { // getter 선언 
        return height == width 
      }
}
```

`isSquare` 프로퍼티에 자체 값을 저장하는 필드가 필요 없다.   
이 프로퍼티에는 자체 구현을 제공하는 Getter만 존재한다.   
클라이언트가 프로퍼티에 접근할 때마다 Getter가 프로퍼티 값을 매번 다시 계산한다.   

```kotlin
val rectangle = Ractangle(41, 43)
println(rectangle.isSquare)
```

<br />
<br />

#### (3) 코틀린 소스코드 구조: 디렉터리와 패키지   
자바의 경우 모든 클래스를 패키지 단위로 관리한다.    
모든 코틀린 파일의 맨 앞에 package 문을 넣을 수 있다. 그러면 그 파일 안에 있는 모든 선언이 해당 패키지에 들어간다.   
같은 패키지에 속해 있다면 다른 파일에 정의한 선언일지라도 직접 사용할 수 있다.   
반면 다른 패키지에 정의한 선언을 사용하려면 임포트를 통해 선언을 불러와야 한다.    

```kotlin
package geometry.shapes // 패키지 선언 

import java.util.Random // 표준 자바 라이브러리 클래스 임포트 

class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean 
      get() = height == width 
}

fun createRandomRectangle(): Ractangle {
    val random = Random()
    return Ractangle(random.nextInt(), random.nextInt())
}
```

패키지 이름 뒤에 `.*`를 추가하면 패키지 안의 모든 선언을 임포트할 수 있다.    
이런 스타 임포트를 사용하면 패키지 안에 있는 모든 클래스뿐 아니라 최상위에 정의된 함수나 프로퍼티까지 모두 불러온다는 것을 유의해야 한다.   


* 자바에서는 디렉토리 구조가 패키지 구조를 그대로 따라야 한다.   

```
geometry
├── example 
│   └── Main
├── shapes
│   ├── Rectangle
│   └── RactangleUtil
```


* 코틀린은 패키지 구조와 디렉토리 구조가 맞아 떨어질 필요는 없다. 

```
geometry
├── example.kt → geometry.example 패키지  
├── shapes.kt → geometry.shapes 패키지 
```

하지만 대부분 자바와 같이 패키지별로 디렉토리를 구성한다.    

<br />
<br />

### 선택 표현과 처리: enum과 when 
when은 자바의 switch를 대치하되 훨씬 더 강력하다.   
<br />

#### (1) enum 클래스 정의 
색상을 표현하는 enum을 하나 정의하자. 

```kotlin
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}
```


자바 `enum` → 코틀린 `enum class`   

<br />

프로퍼티와 메서드가 있는 enum 클래스는 다음과 같이 정의한다.  

```kotlin
enum class Color (
    val r: Int, val g: Int, val b: Int
) {
    RED(255, 0, 0), ORANGE(255, 165, 0), ... ; // 여기 반드시 세미콜론을 사용해야 한다. 
    
    fun rgb() = (r * 256 + g) * 256 + b // enum 클래스 안에서 메서드를 정의한다. 
}
```

enum에서도 일반적인 클래스와 마찬가지로 생성자와 프로퍼티를 선언한다.      

<br />
<br />

#### (2) when으로 enum 클래스 다루기   
if와 마찬가지로 when도 값을 만들어내는 식이다.   
따라서 본문인 함수에 when을 바로 사용할 수 있다.   

```kotlin
// 함수 반환 값으로 when 식을 직접 사용한다. 
fun getMnemonic(color: Color) = 
    when (color) { // 색이 특정 enum 상수와 같을 때 그 상수에 대응하는 문자열을 돌려준다. 
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        . . .
    }
```

* 자바와 달리 분기의 끝에 break를 넣지 않아도 된다.      
* 한 분기에 여러 값을 사용하려면 콤마 `,`로 구분하면 된다.     

<br />
<br />

#### (3) when과 임의의 객체를 함께 사용   
분기 조건에 상수만 사용할 수 있는 자바와 달리 코틀린은 임의의 객체를 허용한다.   

```kotlin
fun mix(c1: Color, c2: Color) = 
    when(setOf(c1, c2)) {
        setOf(RED, YELLOW) -> ORANGE
        . . .
        else -> throw Exception("Dirty color") // 매치되는 분기 조건이 없으면 이 문장을 실행한다. 
    }
```

<br />
<br />

#### (4) 인자 없는 when 사용 
위에서 본 코드는 비효율적이다.    
호출될 때마다 함수 인자로 주어진 두 색이 when의 분기 조건에 있는 다른 두 색과 같은지 비교하기 위해 여러 Set 인스턴스를 생성한다.   
이 함수가 아주 자주 호출된다면 불필요한 가비지 객체가 늘어나는 것을 방지하기 위해 함수를 고쳐 쓰는 편이 낫다.   
성능 향상을 위해 인자가 없는 when을 사용할 때도 있다.  

<br />
<br />

#### (5) 스마트 캐스트: 타입 검사와 타입 캐스트를 조합 
함수가 받을 산술식에서는 오직 두 수를 더하는 연산만 가능하다.   
우선 식을 인코딩하는 방법을 생각해야 한다.   






































