---
layout: post
title: "[Kotlin] Backing Fields"    
description: "Backing Fields"  
date: 2022-09-11   
tags: [kotlin]
categories: [kotlin]
comments: true   
share: true 
---    
<br />

## `field` 키워드 
프로퍼티에 재정의된 Setter를 보면 field 키워드를 볼 수 있다. 


* field 키워드 사용   

```kotlin
var quantity: Int = 0
    set(value) {
        if (value > 0) {
            field = value
        }
    }
```

<br />  


## Backing Fields
코틀린에서 Backing Field가 필요할 때 알아서 제공된다.     
또한 Backing Field는 접근자 내에서 `field` 키워드로 참조할 수 있다.   
Setter 내에서 field 라고 선언된 부분이 Backing Field를 참조하고 있는 상태이다.    
<br />   

Q. Backing Field란?  
A. java field 이다.   


<br /> 

* 코틀린 클래스 

```kotlin
class Coffee {
    var coffee = "아이스 아메리카노"
}
```

<br />  

* 디컴파일된 코틀린 클래스 

```java 
public final class Coffee {
   private String coffee;

   public final String getCoffee() {
      return this.coffee;
   }

   public final void setCoffee(String var1) {
      this.coffee = var1;
   }
}
```

coffee 프로퍼티를 가진 Coffee 클래스를 디컴파일하면 위와 같은 java 클래스를 추출할 수 있다.      
default getter, setter 가 generate 되었고 coffee 필드를 포함한다.    


이 coffee 필드를 코틀린에서 Backing Field 라고 부른다.   


default getter, setter를 사용한 경우 모든 필드가 Backing Field로 생성된다.   

<br />


## `field` 를 사용하지 않으면?  

* 코틀린 코드    

```kotlin
class Test {
    var size = 0;
    var isEmpty
        get() = size == 0
        set(value) {
            size = size * 2
        }
}
```


isEmpty 프로퍼티의 getter, setter를 재정의 하였다.         
getter, setter 가 size의 결과에 따라 변경된다.        
<br />  

 
* 디컴파일된 코드 

```java 
public final class Test {
   private int size;

   public final int getSize() {
      return this.size;
   }

   public final void setSize(int var1) {
      this.size = var1;
   }

   public final boolean isEmpty() {
      return this.size == 0;
   }

   public final void setEmpty(boolean value) {
      this.size *= 2;
   }
}
```


디컴파일 해보면 이전과는 달리 isEmpty 필드가 사라졌다.   
`isEmpty()`, `setEmpty(boolean value)` 모두 size에 의해 결정되기 때문에 isEmpty 필드가 필요 없다고 판단되었기 때문이다.      


이 경우는 Backing Field가 size 하나뿐이게 된다.



<br /> 
 

## Backing Field 가 필요한 이유 

field 키워드는 Backing Field를 참조한다.   
field 키워드가 없으면 다음과 같은 일이 발생한다.

코틀린에서 접근자를 통해 프로퍼티에 접근하는 경우 `foo.bar`와 같은 dot notation을 사용한다.        
접근자를 통해 프로퍼티의 값을 세팅하는 경우도 마찬가지이다.

```kotlin
foo.bar = "a" 
```

위와 같은 코드는 필드에 값을 할당하는 것이 아니고, 실제로는 setter call을 실행하게 된다.   



    
field 키워드 대신 `this.size = value` 를 사용하였다.     
이는 setter call을 사용하게 되고, 디컴파일해보면 recursive 하게 setter call 이 이루어진다는 것을 알 수 있다.      
<br />  

* 코틀린 클래스 

```kotlin
class Test {
    var size: Int = 0
    set(value) {
        if (value >= 0) this.size = value
    }
}
```

<br /> 

* 디컴파일된 코드 

```java 
class Test {
    private int size = 0;
    public void setSize(int value) {
        if (value >= 0) setSize(value);
    }
    public int getSize() {
        return setSize;
    }
}
```


실제로 디컴파일하면 위와 같은 setter를 볼 수 있고, 무한 재귀에 빠질 수 있다.     

이러한 이유에 의해 field 키워드를 사용해 Backing Field에 접근하여 값을 변경하는 것이다.  


