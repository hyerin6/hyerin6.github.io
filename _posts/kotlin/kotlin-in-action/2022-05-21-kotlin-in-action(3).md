---
layout: post
title: "kotlin in action chap3"    
description: "Kotlin Part 1"  
date: 2022-05-21   
tags: [kotlin]    
comments: true   
share: true 
---    


# 3. 함수 정의와 호출   
### 코틀린에서 컬렉션 만들기 
```kotlin
val set = hashSetOf﴾1, 2, 3﴿ // HashSet
val list = arrayListOf﴾1, 2, 3﴿ // ArrayList
val map = hashMapOf﴾1 to "one", 2 to "two"﴿ // HashMap
```

코틀린에서 자체 컬렉션이 아니라 자바 컬렉션을 사용한다.   
표준 자바 컬렉션을 활용하면 자바 코드와 상호작용이 훨씬 더 쉽다.   
코틀린에서는 자바보다 더 많은 기능을 쓸 수 있다.   

추가 확장 함수 제공 예) `list.last()`, `set.max()`    

<br />
<br />

### 함수를 호출하기 쉽게 만들기   
여러 원소로 이뤄진 컬렉션의 모든 원소를 찍어보자.   

자바 컬렉션에는 디폴트 `toString` 구현이 들어있다.   
그러나 디폴트 `toString`의 출력 형식은 고정돼 있기 때문에 필요한 형식이 아닐 수 있다.   

```kotlin
val list = listOf(1, 2, 3)
println(list) // 출력 => [1, 2, 3]
```

만약 `(1; 2; 3)` 과 같이 출력하고 싶으면 어떻게 해야 할까?    

자바 프로젝트에 구아바나 아파치 커먼즈 같은 서드 파티 프로젝트를 추가하거나 직접 관련 로직을 구현해야 한다.   
코틀린에는 이런 요구 사항을 처리할 수 있는 함수가 표준 라이브러리에 이미 들어있다.   

<br />    

#### (1) 함수 호출 

다음 리스트의 `joinToString` 함수는 컬렉션의 원소를 `StringBuilder`의 뒤에 덧붙인다.    

```kotlin
fun <T> joinToString(
     collection: Collection<T>,
     separator: String,
     prefix: String,
     postfix: String
): String {
    ...
}
```

* 이름 없는 인자 

```kotlin
joinToString(collection, " ", " ", ".")
```


* 이름 붙인 인자 

```kotlin
joinToString(collection, separatpr = "",
         prefix = " ", postfix = ".")
```

인자 중 하나라도 이름을 명시하면, 그 뒤 에 오는 모든 인자는 이름을 명시해야 한다.   


<br />
<br />

#### (2) 디폴트 파라미터 값 
자바에서는 일부 클래스에서 오버로딩한 메서드가 너무 많아진다는 문제가 있다.   
예) `java.lang.Thread`  
 
코틀린에서는 함수 선언에서 파라미터의 디폴트 값을 지정할 수 있으므로 이런 오버로드 중 상당수를 피할 수 있다.   
디폴트 값을 사용해 `joinToString` 함수를 개선해보자.   


```kotlin
fun <T> joinToString(
     collection: Collection<T>,
     separator: String = ", ",
     prefix: String = "",
     postfix: String = ""
): String {
    ... 
}
```

```kotlin
joinToString(collection, ", ", "", "")
joinToString(collection)
joinToString(collection, "; ")
joinToString(collection,
             postfix = ";", prefix = "# ")
```


* 이름 없는 인자: 일부를 생략하면 뒷부분의 인자들 생략
* 이름 붙인 인자: 지정하고 싶은 인자를 이름을 붙여 지정

함수의 디폴트 파라미터 값은 함수를 호출하는 쪽이 아니라 함수 선언 쪽에서 지정된다.   
따라서 어떤 클래스 안에 정의된 함수의 디폴트 값을 바꾸고 그 클래스가 포함된 파일을 재컴파일하면 그 함수를 호출하는 코드 중에 값을 지정하지 않은 모든 인자는 자동으로 바뀐 디폴트 값을 적용받는다.    


> `@JvmOverloads`     
> 맨 마지막 파라미터로부터 파라미터를 하니씩 생략한 오버로딩한 자바 메서드 추가  


<br />
<br />

#### (3) 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티   
객체지향 언어인 자바에는 모든 코드를 클래스의 메서드로 작성해야 한다.  
그러나 실무에서 어느 한 클래스에 포함시키기 어려운 코드가 많이 생긴다.   

다양한 정적 메서드를 모아두는 역할만 담당하며, 특별한 상태나 인스턴스 메서드는 없는 클래스가 생겨난다.   
JDK의 `Collections`가 전형적인 예다.   

코틀린에서는 이런 무의미한 클래스가 필요 없다.   
대신 함수를 직접 소스파일의 최상위 수준, 모든 다른 클래스의 밖에 위치시키면 된다.   

```kotlin
// join.kt
 package chap03
// 특정 클래스나 오브젝트에 속하지 않은 // 최상위 함수
fun <T> joinToString(
         collection: Collection<T>,
         separator: String = ", ",
         prefix: String = "",
         postfix: String = ""
 ): String { ... }
```

```kotlin
import chap03.*
fun main(args: Array<String>) {
// 최상위 함수 사용 joinToString(arrayList""Of(3, 4, 5),
                             prefix = "#")
```

JVM이 클래스 안에 들어있는 코드만 실행할 수 있기 때문에 컴파일러는 이 파일을 컴파일할 때 새로운 클래스를 정의해준다.   
코틀린만 사용하는 경우 그냥 그런 클래스가 생긴다는 사실만 기억하면 된다.   

코틀린 컴파일러가 생성하는 클래스의 이름은 최상위 함수가 들어있던 코틀린 소스 파일의 이름과 대응한다.   
코틀린 파일의 모든 최상위 함수는 이 클래스의 정적인 메서드가 된다.   

따라서 자바에서 호출은 `JoinKT.joinToString﴾....﴿` 쉽다.   

<br />    
<br />    


**최상위 프로퍼티**      
함수와 마찬가지로 프로퍼티도 파일의 최상위 수준에 놓을 수 있다.        

```kotlin
// join.kt
var opCount = 0 // 최상위 프로퍼티를 선언 
fun performOperation() { 
    opCount++ // 최상위 프로퍼티 값 변경  
}

// 자바의 getter
val WINDOW_LINE_SEPARATOR = "\r\n" // JoinKt.getWINDOW_LINE_SEPARATOR()

// const 수식어: 자바의 public static final
const val UNIX_LINE_SEPARATOR = "\n" // JoinKt.UNIX_LINE_SEPARATOR
```

기본적으로 최상위 프로퍼티도 다른 모든 프로퍼티처럼 접근자 메서드를 통해 자바 코드에 노출된다.    
겉으론 상수처럼 보이는데 실제로는 Getter를 사용해야 한다면 자연스럽지 않다.   
더 자연스럽게 사용하려면 상수를 `public static final` 필드로 컴파일해야 한다.  
`const` 변경자를 추가하면 프로퍼티를 `public static final` 필드로 컴파일하게 만들 수 있다.   
(단, 원시 타입과 String 타입의 프로퍼티만 const로 지정할 수 있음)

<br />
<br />

### 메서드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티   
기존 자바 API를 재작성하지 않고도 코틀린이 제공하는 여러 편리한 기능을 사용할 수 있다.   
확장 함수가 그런 역할을 해준다.   

개념적으로 확장 함수는 어떤 클래스의 멤버 메서드인 것처럼 호출할 수 있지만 그 클래스의 밖에서 선언된 함수다.   

```kotlin
fun String.lastChar(): Char = this.get(this.length ‐ 1) 
    ^수신객체타입                ^수신객체
println("Kotlin".lastChar())
```

* 클래스 이름: 수신 객체 타입(`String`, receiver type)  
* 확장 함수가 호출되는 대상 값(객체): 수신 객체(`this`, receiver object)  


일반 메서드의 본문에서 this를 사용할 때와 마찬가지로 확장 함수 본문에서도 this를 쓸 수 있다.   
또한 일반 메서드와 마찬가지로 this를 생략할 수 있다.   

```kotlin
package strings 

fun String.lastChar(): Char = get(length - 1) // 수신 객체 멤버에 this 없이 접근할 수 있음 
```


확장 함수 안에서 수신 객체의 메서드나 프로퍼티 사용 가능하나             
확장 함수가 캡슐화를 깨지는 않는다.           
➡️ private 멤버나 protected 멤버만 접근 불가   

확장 함수보다 멤버 함수가 우선한다.    

<br />
<br />

#### (1) import와 확장 함수 

* 확장 함수를 사용하려면 임포트해야 한다.  

```kotlin
import strings.lastchar
val c = "Kotlin".lastChar()
```


* `as`로 확장 함수를 다른 이름으로 임포트 가능﴾이름이 충돌할 경우 주로 사용﴿  

```kotlin
import strings.lastChar as last
val c = "Kotlin".last()
```


<br />
<br />

#### (2) 자바에서 확장 함수 호출    
내부적으로 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메서드다.      
그래서 확장 함수를 호출해도 다른 어댑터 객체나 실행 시점 부가 비용이 들지 않는다.      

이런 설계로 자바에서 확장 함수를 사용하기도 편하다.

```kotlin
// strings.kt
fun Collecion<String>.join(separator: String, prefix: String, postfix: String)
listOf("1", "2", "3").join(",", "#", "")
 
List<String> strs = ...
StringsKt.join(strs, ",", "#", ""); // 수신 객체가 첫 번째 인자 
```


<br />
<br />

#### (3) 확장 프로퍼티
확장 프로퍼티를 사용하면 기존 클래스 객체에 대한 프로퍼티 형식의 구문으로 사용할 수 있는 API를 추가할 수 있다.    

```kotlin
val String.first: Char
     get() = get(0)
var StringBuilder.lastChar: Char
    get() = get(length ‐ 1)
    set(ch: Char) {
        this.setCharAt(length ‐ 1, ch)
    }
```


* 상태를 저장할 방법이 없지만, 프로퍼티 문법으로 더 짧게 코드를 작성할 수 있는 편리함    
* 지원 필드가 없으므로   
    - 최소한 Getter는 정의﴾기본 게터 구현 제공 불가﴿  
    - 초기화 코드 쓸 수 없음  


<br />
<br />


### 코드 다듬기: 로컬 함수와 확장   
메서드 추출 리팩토링을 적용해서 긴 메서드를 부분부분 나눠서 각 부분을 재활용할 수 있다.    
그러나 클래스 안에 작은 메서드가 많아지고 각 메서드 사이의 관계를 파악하기 힘들어 코드를 이해하기 더 어려워질 수도 있다.    
별도의 내부 클래스 안에 넣으면 깔끔해 보이긴 하지만, 그에 따른 불필요한 준비 코드가 늘어난다.   

코틀린에서는 함수에서 추출한 함수를 원 함수 내부에 중첩시킬 수 있다.   
(= 코드 중복을 로컬 함수를 통해 제거하자!)    

```kotlin
fun saveUser(user: User) {
  if(user.name.isEmpty()) {
      . . .
  }
  
  if(user.address.isEmpty()) {
      . . .
  }
}
```

이런 경우 검증 코드를 로컬 함수로 분리하면 중복을 없애고 코드 구조를 깔끔하게 유지할 수 있다.   

```kotlin
fun saveUser(user: User) {
  fun validate(value: String, fieldName: String) {
    if(value.isEmpty()) {
      throw IllegalArgumentException(
        "Can't save user ${user.id}: empty $filedName" // 바깥 함수의 파라미터에 직접 접근할 수 있다. 
      )
    }
  }
}
```


위 예제 코드를 더 개선하고 싶다면 검증 로직을 User 클래스를 확장한 함수로 만들 수도 있다.   

```kotlin
fun User.validateBeforeSave() {
  fun validate(value: String, fieldName: String) {
      . . .
  }
}

fun saveUser(user: User) {
  user.validateBeforeSave() // 확장 함수  호출 
}
```


