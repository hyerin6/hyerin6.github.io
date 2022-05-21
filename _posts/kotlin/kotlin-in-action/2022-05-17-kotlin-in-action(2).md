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

```kotlin
interface Expr 
class Num(val value: Int) : Expr // (1)
clas Sum(val left: Expr, val right: Expr) : Expr // (2)
```

(1) value라는 프로퍼티만 존재하는 단순한 클래스로 Expr 인터페이스를 구현한다.     

(2) Expr 타입의 객체라면 어떤 것이나 Sum 연산자의 인자가 될 수 있다.  
따라서 Num이나 다른 Sum이 인자로 올 수 있다.   

<br />  

Sum은 Expr의 왼쪽과 오른쪽 인자에 대한 참조를 left와 right 프로퍼티로 저장한다.   
이 예제에서 left와 right는 각각 Num이나 Sum일 수 있다.      
`(1 + 2) + 4` 라는 식을 저장하면 `Sum(Sum(Num(1), Num(2), Num(4)))` 라는 구조의 객체가 생긴다.    
<br />  

Expr 인터페이스에는 두 가지 구현 클래스가 존재한다.     
따라서 식을 평가하려면 두 가지 경우를 고려해야 한다.   

* 어떤 식이 수라면 그 값을 반환한다.  
* 어떤 식이 합계라면 좌항과 우항의 값을 계산한 다음에 그 두 값을 합한 값을 반환한다.   

<br />  

코틀린에서는 `is`를 사용해 변수 타입을 검사한다.   
`is` 검사는 자바의 `instanceof`와 비슷하다.     


자바에서는     
어떤 변수 타입을 `instanceof`로 확인 
→ 그 타입에 속한 멤버에 접근하기 위해 명시적 변수 타입을 캐스팅   
→ 이런 멤버 접근을 여러 번 수행해야 한다면 변수에 따로 캐스팅한 결과를 저장한 후 사용  

코틀린에서는 개발자 대신 컴파일러가 캐스팅을 해준다.      
어떤 변수가 원하는 타입인지 일단 `is`로 검사       
→ 이후 컴파일러가 캐스팅을 수행한다. 이를 스마트 캐스트라고 부른다.   

```kotlin
if (e is sum) {
  return eval(e.right) + eval(e.left)
}
```

> IDE에서 배경색으로 스마트 캐스트를 표시해준다.   

스마트 캐스트는 `is`로 변수에 든 값의 타입을 검사한 다음에 그 값이 바뀔 수 없는 경우에만 작동한다.

원하는 타입으로 명시적으로 타입 캐스팅하려면 `as` 키워드를 사용한다.   

```kotlin
val n = e as Num
```

<br /> 
<br /> 

#### (6) 리팩토링: if를 when으로 변경   
코틀린의 `if (a > b) a else b`는 자바의 `a > b ? a : b` 처럼 작동한다.   
코틀린에서는 if가 값을 만들어내기 때문에 자바와 달리 3항 연산자가 따로 없다.   
이런 특성을 사용하면 eval 함수에서 return 문과 중괄호를 없애고 if 식을 본문으로 사용해 더 간단하게 만들 수 있다.   


```kotlin
fun eval(e: Expr): Int =  
  if(e is Num) {
    e.value
  } else if (e is Sum) {
    eval(e.right) + eval(e.left)
  } else {
    throw IllegalArgumentException("Unknown expression")
  }
```

if 분기에 블록을 사용하는 경우 그 블록의 마지막 식이 그 분기의 결과 값이다.   

위 코드는 when을 사용해 다듬어 볼 수 있다.  


```kotlin
fun eval(e: Expr): Int =  
  when (e) {
    is Num ->
      e.value 
    is Sum ->
      eval(e.right) + eval(e.left)
    else ->
      throw IllegalArgumentException("Unknown expression")
  }
```

when 식을 값 동등성 검사가 아닌 다른 기능에도 쓸 수 있다.   
if 예제와 마찬가지로 타입을 검사하고 나면 스마트 캐스트가 이뤄진다.   
따라서 Num이나 Sum의 멤버에 접근할 때 변수를 강제로 캐스팅할 필요가 없다. 

<br />
<br />

#### (7) if와 when의 분기에서 블록 사용 
if나 when 모두 분기에 블록을 사용할 수 있다.   
이 경우 블록의 마지막 문장이 블록 전체의 결과가 된다.   

```kotlin
fun evalWithLogging(e: Expr): Int = 
  when (e) {
    is Num -> {
      println("num: ${e.value}")
      e.value
    }
    
    . . .
    
  }
```

`is Num` 블록의 마지막이 `e.value` 식이므로 e의 타입이 Num이며 `e.value`가 반환된다.   

블록의 마지막 식이 블록의 결과라는 규칙은 블록이 값을 만들어내야 하는 경우 항상 성립하는데    
이 규칙은 함수에 대해서는 성립하지 않는다.   

식이 본문인 함수는 블록을 본문으로 가질 수 없고 블록이 본문인 함수는 내부에 return문이 반드시 있어야 한다.    

<br />
<br />

### 대상을 이터레이션: while과 for 루프   
코틀린 특성 중 자바와 가장 비슷한 것이 이터레이션이다.   
코틀린 while 루프는 자바와 동일하고, for는 자바의 for-each 루프에 해당하는 형태만 존재한다.   
<br />

#### (1) 범위와 수열   

```kotlin
for (i in 1..100) {
  println(i) 
} 
```

<br />

#### (2) 리스트, 맵에 대한 이터레이션 

* List

```kotlin
val list = arrayListOf("10", "11", "1001")
for ((idx, ele) in list.withIndex()) {
    println("$idx: $ele")
}
```


* Map 

```kotlin
val binbaryReps = TreeMap<Char, String>()

. . .

for ( (key, value) in binbaryReps) { // 맵에 대한 이터레이션
    println("$key = $value")
}
```

<br />

#### (3) in으로 컬렉션이나 범위의 원소 검사 

`in` 연산자를 사용해 어떤 값이 범위에 속하는지 검사할 수 있다.  
반대로 `!in`을 사용하면 어떤 값이 범위에 속하지 않는지 검사할 수 있다.  


```kotlin
// 'a' <= c && c <= 'z'로 변환된다.  
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'

fun isNotDigit(c: Char) = c !in '0'..'9'

fun recognize(c: Char) = when(c) {
  in '0'..'9' ‐> "It's digit!"
  in 'a'..'z', in 'A'..'Z' ‐> "It's a letter" // 여러 범위 조건을 함께 사용해도 된다.    
  else ‐> "I don't know."
}
println("Kotlin" in "Java".."Scala") // Comparable 구현 클래스
```


```kotlin
println("Kotlin" in setOf("Java", "Scaka", "Kotlin"))
```

<br />
<br />

### 코틀린 예외 처리 
코틀린 예외처리는 자바나 다른 언어의 예외 처리와 비슷하다.    

함수에서 오류가 발생하면 예외를 던질 수 있고 함수를 호출하는 쪽에서 그 예외를 잡아 처리할 수 있다.   
발생한 예외를 함수 호출 단에서 처리하지 않으면 함수 호출 스택을 거슬러 올라가면서 예외를 처리하는 부분이 나올 때까지 예외를 다시 던진다.   

```kotlin
if(percentage !in 0..100) {
  throw IllegalArgumentException(
    "A percentage value must be between 0 and 100: $percentage")
}
```

예외 인스턴스를 만들 때도 `new`를 붙일 필요가 없다.    
자바와 달리 코틀린의 throw는 식이므로 다른 식에 포함될 수도 있다.   


```kotlin
val percentage = 
  if(number in 0..100)
    number
  else 
    throw IllegalArgumentException( // throw는 식이다.   
      "A percentage value must be between 0 and 100: $percentage")
```

<br />
<br />

#### (1) try, catch, finally  
자바와 마찬가지로 예외를 처리하려면 try, catch, finally 절을 함께 사용한다. 

```kotlin
 fun readNumber(reader: BufferedReader): Int? {
     try {
         val line = reader.readLine()
         return Integer.parseInt(line)
     } catch (e: NumberFormatException) {
         return null
     } finally {
       reader.close()
     }
}
```

* 함수가 던질 수 있는 예외를 명시할 필요가 없다.   
* 예외 타입을 `:`의 오른쪽에 쓴다.   
* finally는 자바와 똑같이 작동한다. 


자바 코드와 큰 차이점은 `throws` 절이 코드에 없다는 점이다.     
자바에서 함수를 작성할 때 함수 선언 뒤에 `IOException throws`를 붙여야 한다.     
그 이유는 IOException은 체크 예외이고 자바에서 체크 예외를 명시적으로 처리해야 하기 때문이다.   

다른 최신 JVM 언어와 마찬가지로 코틀린도 체크 예외와 언체크 예외를 구별하지 않는다.   
코틀린에서는 함수가 던지는 예외를 지정하지 않고 발생한 예외를 잡아내도 되고 잡아내지 않아도 된다.   

<br />
<br />

#### (2) try를 식으로 사용 
자바와 코틀린의 중요한 차이가 하나 더 있다.  

```kotlin
fun readNumber(reader: BufferedReader) {
  val number = try {
     Integer.parseInt(reader.readLine()) // 이 식의 값이 try 식의 값이 된다. 
  } catch (e: NumberFormatException) {
     return 
  }
  println(number)
}
```

코틀린의 `try` 키워드는 `if`나 `when`과 마찬가지로 식이다.   
따라서 try 값을 변수에 대입할 수 있다.   

> if와 달리 try의 본문은 반드시 중괄호(`{}`)로 둘러싸야 한다.   
> 다른 문장과 마찬가지로 try의 본문도 내부에 여러 문장이 있으면 마지막 식의 값이 전체 결과 값이다.   


```kotlin
fun readNumber(reader: BufferedReader) {
  val number = try {
     Integer.parseInt(reader.readLine()) // 예외가 발생하지 않으면 이 값을 사용 
  } catch (e: NumberFormatException) {
     null // 예외가 발생하면 null 값을 사용한다. 
  }
  println(number)
}
```

