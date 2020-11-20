---
layout: post
title: "ResponseEntity & Generic"      
description: "디프만 파이널 프로젝트"  
date: 2020-11-20
tags: [depromeet]
comments: true
share: true
--- 


이미지 저장, 주소 저장, 인기 반려동물과 탐색 정렬 기준 등   
회의해야 할 게 아직 많이 남았지만 간단하게 필요한 데이터와 관계를 정리해봤다.                 

![스크린샷 2020-11-17 오후 11 59 37](https://user-images.githubusercontent.com/33855307/99406407-312b7500-2931-11eb-8f82-499aa1f31823.png)    


<br />     


# ResponseEntity     

ResponseEntity는 HttpEntity를 상속받아 HttpHeader와 body를 가질 수 있다.        
status field를 가지기 때문에 상태 코드도 리턴해줘야 한다.   

```
new ResponseEntity<>("success", HttpStatus.OK); // 메시지(String)과 상태코드(200)를 리턴
new ResponseEntity<>(message, HttpStatus.INTERNAL_SERVER_ERROR); // 객체와 상태코드를 오류로 리턴         
new ResponseEntity(HttpStatus.OK);  // 상태코드(200)만 리턴   
new ResponseEntity(header, HttpStatus.OK);  // header와 상태코드(200) 리턴   
```


- 참고    
<https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/ResponseEntity.html#ResponseEntity-T-org.springframework.util.MultiValueMap-org.springframework.http.HttpStatus->  


<br />       


# Generic   

이번 프로젝트에서도 클라이언트에 ResponseEntity로 응답하는데       
제네릭이 사용되서 제네릭에 대해 공부해봐야 겠다는 생각이 들었고         
먼저 ResponseEntity도 같이 가볍게 정리해봤다. 😊😊     

제네릭을 검색해 본적이 많은데 프로그래밍에서 뜻이나 문법만 나와서   
이펙티브 자바에 제네릭을 왜 사용하는지, 장단점이나 주의할 내용이 있는지 찾아봤더니 역시 관련된 내용이 있었다.     
아직 2장을 읽는 중이라 몰랐는데 5장 전체가 제네릭에 관련된 내용이었다..     
먼저 제네릭 부분부터 읽어보자.   

<br />   

### 1. 이왕이면 제네릭 타입으로 만들라.     

다음은 단순한 스택 코드이다.   

```  
public class Stack {  
    private Object[] elements;    
        
    . . .  

}  
```   

이 클래스를 제네릭으로 바꾼다고 해도 클라이언트에는 아무런 해가 없다.   
오히려 위 코드는 클라이언트가 스택에서 꺼낸 객체를 형변환해야 하는데, 이때 런타임 오류가 날 위험이 있다.   

일반 클래스를 제네릭 클래스로 만드는 첫 단계는 클래스 선언에 타입 매개변수를 추가하는 일이다.   

```
public class Stack<E> {
    private E[] elements;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public stack() {
        elements = new E[DEFAULT_INITIAL_CAPACITY];
    }
}
```

이 상태에서 컴파일하면 다음과 같은 오류가 밟생한다.   

```
Stack.java:8: generic array creation 
    elements = new E[DEFAULT_INITIAL_CAPACITY];
                ^
```

E와 같은 실체화 불가 타입으로는 배열을 만들 수 없다.     
배열을 사용하는 코드를 제네릭으로 만들려 할 때 이 문제가 항상 발목을 잡을 것이다.   

해결책은 두 가지가 있다.     

**(1) 제네릭 배열 생성을 금지하는 제약을 대놓고 우회하는 방법**   
Object 배열을 생성한 다음 제네릭 배열로 형변환해보자.   
컴파일러는 오류 대신 경고를 내보낼 것이다. (일반적으로) 타입 안전하지 않다.   

```
Stack.java8: warning: [unchecked] unchecked cast 
found: Object[], required: E[]  
elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
              ^  
```        

컴파일러는 타입 안전한지 증명할 방법이 없지만 우리는 할 수 있다.   
따라서 이 비검사 형변환이 프로그램의 타입 안전성을 해치지 않음을 우리 스스로 확인해야 한다.   
배열 elements는 private 필드에 저장되고, 클라이언트로 반환되거나 다른 메서드에 전달되는 일은 전혀 없다.   
push 메서드를 통해 배열에 저장되는 원소의 타입은 항상 E다. 따라서 이 비검사 형변환은 확실히 안전하다.   

비검사 형변환이 안전함을 직접 증명했다면 범위를 좁혀 `@SuppressWarnings` 어노테이션으로 해당 경고를 숨긴다.   

```  
@SuppressWarnings("unckecked")
public Stack() {
    elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];  
}  
```   



**(2) elements 필드의 타입을 `E[]` 에서 `Object[]`로 바꾸는 방법**        
이렇게 하면 첫 번째와는 다른 오류가 발생한다.   

```
Stack.java:19: incompatible types 
found: Object, required: E
    E result = elements[--size];
``` 

배열이 반환한 원소를 E로 형변환하면 오류 대신 경고가 뜬다.       

E는 실체화 불가 타입이므로 컴파일러는 런타임에 이뤄지는 형변환이 안전한지 증명할 방법이 없다.   
이번에도 개발자가 직접 증명하고 경고를 숨길 수 있다.  
pop 메서드 전체에서 경고를 숨기지 않고 비검사 형변환을 수행하는 할당문에서만 숨기는 것이다.     

```  
public E pop() {  
    if(size == 0)   
        throw new EmptyStackException();
    
    @SuppressWarnings("unckecked") E result = (E) elements[--size];

    elements[size] = null; //  다 쓴 참조 해제  
    return result;
}
```   

첫 번째 방법은 가독성이 좋고 코드가 짧기 때문에 자주 사용되는 방법이지만     
(E가 Object가 아닌 한) 배열의 런타임 타입이 컴파일타임 타입과 달라 힙 오염(heap pollution)을 일으킨다.   
힙 오염이 맘에 걸리는 개발자는 두 번째 방법을 사용하기도 한다.   
그러나 두 번째 방법은 배열에서 원소를 읽을 때마다 어노테이션을 붙여줘야 한다.     


**정리**    
클라이언트에서 직접 형변환해야 하는 타입보다 제네릭 타입이 더 안전하고 쓰기 편하다.    
그러니 새로운 타입을 설계할 때는 형변환 없이도 사용할 수 있도록 하자.   
그렇게 하려면 제네릭 타입으로 만들어야 할 경우가 많다. 기존 타입 중 제네릭이있어야 하는 게 있다면 제네릭 타입으로 변경하자.   
기존 클라이언트에게 아무 영향을 주지 않으면서 새로운 사용자를 편하게 해줄 수 있다.   


<br />   


### 2. 이왕이면 제네릭 메서드로 만들라.        

클래스와 마찬가지로 메서드도 제네릭으로 만들 수 있다.        
매개변수화 타입을 받는 정적 유틸리티 메서드는 보통 제네릭이다.   

다음은 두 집합의 합집합을 반환하는 문제가 있는 메서드다.   

```  
public static Set union(Set s1, Set s2) {
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
}  
```   

컴파일은 되지만 경고가 두 개 발생한다.   

```
HashSet(Collection<? extends E>) as a member of raw type HashSet 
    Set result = new HashSet(s1);
                 ^

addAll(Collection<? extends E>) as a member of raw type Set
    result.addAll(s2);
                 ^ 
```

경고를 없애려면 이 메서드를 타입 안전하게 만들어야 한다.   
메서드 선언에서의 세 집합(입력 2개, 반환 1개)의 원소 타입을 타입 매개변수로 명시하고,   
메서드 안에서도 이 타입 매개변수만 사용하게 수정하면 된다.   
(타입 매개변수들을 선언하는) 타입 매개변수 목록은 메서드의 제한자와 반환 타입 사이에 온다.   

다음 코드에서 타입 매개변수 목록은 `<E>` 이고 반환 타입은 `Set<E>` 이다.   

```  
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```  

직접 형변환하지 않아도 어떤 오류나 경고 없이 컴파일된다.   




불변 객체를 여러 타입으로 활용할 수 있게 만들어야 할 때가 있다.   
제네릭은 런타임에 타입 정보가 소거되므로 하나의 객체를 어떤 타입으로든 매개변수화할 수 있다.   
하지만 이렇게 하려면 요청한 타입 매개변수에 맞게 매번 그 객체의 타입을 바꿔주는 정적 팩터리를 만들어야 한다.   
이 패턴은 싱글톤 팩터리라고 하며, `Collections.reverseOrder` 같은 함수 객체나 `Collections.emptySet` 같은 컬렉션용으로 사용된다.       

다음은 제네릭 싱글톤 팩터리 패턴의 예제 코드이다.    

```  
private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

@SuppressWarnings("unckecked")
public static <T> UnaryOperator<Object> identityFunction() {
    return (UnaryOperator<T>) IDENTITY_FN;
}
```   

`IDENTITY_FN`을 `UnaryOperator<T>`로 형변환하면 비검사 형변환 경고가 발생한다.      
`T`가 어떤 타입이든 `UnaryOperator<Object>`는 `UnaryOperator<T>`가 아니기 때문이다.     
하지만 항등함수한 입력 값을 수정 없이 그대로 반환하는 특별한 함수이므로, `T`가 어떤 타입이든 `UnaryOperator<T>`를 사용해도 타입 안전하다.     
이 사실을 알고 있기 때문에 `@SuppressWarnings("unckecked")` 어노테이션을 추가하면 오류나 경고 없이 컴파일 된다.          

자기 자신이 들어간 표현식을 사용하여 타입 매개변수의 허용 범위를 한정할 수 있다.   
재귀적 타입 한정(recursive type bound)이라는 개념이다.   
재귀적 타입 한정은 주로 타입의 자연적 순서를 정하는 Comparable 인터페이스와 함께 쓰인다.   

```  
public interface Comparable<T> {  
    int compareTo(T o);  
}
```    

여기서 타입 매개변수 `T`는 `Comparable<T>`를 구현한 타입이 비교할 수 있는 원소의 타입을 정의한다.   
실제로 거의 모든 타입은 자신과 같은 타입의 원소와만 비교할 수 있다.   

다음은 이 제약을 코드로 표현한 코드이다.   

```  
public static <E extends Comparable<E>> E max(Collection<E> c);  
```   

타입 한정인 `<E extends Comparable<E>>`는 "모든 타입 E는 자신과 비교할 수 있다."라고 읽을 수 있다.   
상호 비교 가능하다는 뜻이다.   



<br />          



### 3. 한정적 와일드카드를 사용해 API 유연성을 높여라.  

매개변수화 타입은 불공변(invariant)이다.    
즉 서로 다른 타입 Type1과 Type2가 있을 떄 List<Type1>은 List<Type2>의 하위 타입도 상위 타입도 아니다.     
예를들어 `List<String>`은 `List<Object>`의 하위 타입이 아니라는 뜻이다.  
`List<Object>`에는 어떤 객체든 넣을 수 있지만 `List<String>`에는 문자열만 넣을 수 있다.   
즉 `List<String>`은 `List<Object>`가 하는 일을 제대로 수행하지 못하니 하위 타입이 될 수 없다. (리스코프 치환 원칙에 어긋난다.)    

Stack의 public API를 추려보자.     

```  
public class Stack<E> {
    public Stack();
    public void push(E e);
    public E pop();
    public boolean isEmpty();
}
```     


여기에 일련의 원소를 스택에 넣는 메서드를 추가해야 한다고 해보자.   


- 와일드카드 타입을 사용하지 않는 pushAll 메서드 (결함있음)  

```  
public void pushAll(Iterable<E> src) {
    for(E e : src)
        push(e);
}
```

이 메서드는 깨끗하게 컴파일되지만 완벽하진 않다. Iterable src의 원소 타입이 스택의 원소 타입과 일치하면 잘 작동한다.   
하지만 `Stack<Number>`로 선언한 후 `pushAll(intVal)`을 호출하면 다음과 같은 오류 메시지가 뜬다. (intVal은 Integer타입이다.)      
 

```     
StackTest.java7: error: incompatible types: Iterable<Integer>
cannot be converted to Iterable<Number>
    numberStack.pushAll(integers);
                        ^
```   

Integer는 Number의 하위 타입이므로 잘 작동할 것 같지만 매개변수화 타입이 불공변이기 때문에 오류 메시지가 뜬다.   

이런 상황은 한정적 와일드카드 타입이라는 매개변수화 타입으로 해결할 수 있다.    
pushAll의 입력 매개변수 타입은 'E의 Iterable'이 아니라 'E의 하위 타입의 Iterable'이어야 하며,       
와일드카드 타입 `Iterable<? extends E>`가 위와 같은 뜻이 된다.    


```    
public void pushAll(Iterable<? extends E> src) {  
    . . .  
}
```    


이번에는 popAll 메서드를 구현하면서 `Stack<Number>`의 원소를 Object용 컬렉션은로 옮긴다고 가정해보자.     

```  
public void popAll(Collection<E> dst) {
     . . .
}

Stack<Number> numberStack = new Stack<>();
Collection<Object> objects - ...;
numberStack.popAll(objects);
```  

위 코드를 컴파일하면 "`Collection<Object>`는 `Collection<Number>`의 하위 타입이 아니다." 라는   
pushAll을 사용했을 때와 비슷한 오류가 발생한다.   
이번에는 pushAll의 입력 매개변수의 타입이 'E의 Collection'이 아니라 'E의 상위 타입의 Collection'이어야 한다.   
(모든 타입은 자기 자신의 상위 타입이다.) 
와일드카드 타입을 사용한 `Collection<? super E>`가 정확히 이런 의미이다.   

```   
public void popAll(Collection<? super E> dst) {
     . . .
}
```  


이제 Stack과 클라이언트 코드 모두 말끔히 컴파일된다.   
유연성을 극대화하려면 원소의 생상자나 소비자용 입력 매개변수에 와일드카드 타입을 사용하자.   

한편, 입력 매개변수가 생산자와 소비자 역할을 동시에 한다면 와일드카드 타입을 써도 좋을 게 없다.     
타입을 정확히 지정해야 하는 상황으로 이때는 와일드카드 타입을 쓰지 말아야 한다.    


<br />          

