---   
layout: post  
title: "추상 골격 구현 클래스 (Abstract Skeletal Implementation Class)"    
description: "effective java chap4"  
date: 2021-06-20        
tags: [effective-java, java]      
comments: true    
share: true
--- 

# 추상 골격 구현 클래스란?    
인터페이스의 장점과 추상 클래스의 장점을 결합   

자판기를 개발할 것이다.         
음료 자판기, 커피 자판기, 라면 자판기 등등 여러 타입이 있다.           


# Interface          

### 자판기 인터페이스   
```
public interface Ivending {
    void start();
    void chooseProduct();
    void stop();
    void process();
}
```

### 캔디 구현체    
```
public class CandyVending implements Ivending {
    @Override
    public void start() {
        System.out.println("Start Vending machine");
    }
    @Override
    public void chooseProduct() {
        System.out.println("Produce diiferent candies");
        System.out.println("Choose a type of candy");
        System.out.println("pay for candy");
        System.out.println("collect candy");
    }
    @Override
    public void stop() {
        System.out.println("Stop Vending machine");
    }
    @Override
    public void process() {
        start();
        chooseProduct();
        stop();
    }
}
```

### 음료수 자판기   
```
public class DrinkVending implements Ivending {
    @Override
    public void start() {
        System.out.println("Start Vending machine");
    }
    @Override
    public void chooseProduct() {
        System.out.println("Produce diiferent soft drinks");
        System.out.println("Choose a type of soft drinks");
        System.out.println("pay for drinks");
        System.out.println("collect drinks");
    }
    @Override
    public void stop() {
        System.out.println("stop Vending machine");
    }
    @Override
    public void process() {
        start();
        chooseProduct();
        stop();
    }
}
```

### main   
```
public class VendingManager {
    public static void main(String[] args) {
        Ivending candy = new CandyVending();
        Ivending drink = new DrinkVending();
        candy.process();
        drink.process();
    }
}
```

<br />    


`start()`, `stop()`, `process()`가 중복된다.           
그렇다고 유틸리티클래스에 코드를 넣으면 SRP                            
(단일 책임 원칙, 객체는 하나의 책임(역할)만을 가져야 한다.)가 깨지게 된다.                  
이처럼 인터페이스는 중복 코드를 만들게 될 수 있다.   

<br />  

# Abstract   
### 그렇다면 추상 클래스는? 
```
public abstract class AbstractVending {
    public void start() {
        System.out.println("Start Vending machine");
    }
    
    public abstract void chooseProduct();
    
    public void stop() {
        System.out.println("Stop Vending machine");
    }
    
    public void process() {
        start();
        chooseProduct();
        stop();
    }
}

public class CandyVending extends AbstractVending {
    @Override
    public void chooseProduct() {
        System.out.println("Produce diiferent candies");
        System.out.println("Choose a type of candy");
        System.out.println("pay for candy");
        System.out.println("collect candy");
    }
}

public class DrinkVending extends AbstractVending {
    @Override
    public void chooseProduct() {
        System.out.println("Produce diiferent soft drinks");
        System.out.println("Choose a type of soft drinks");
        System.out.println("pay for drinks");
        System.out.println("collect drinks");
    }
}

public class VendingManager {
    public static void main(String[] args) {
        AbstractVending candy =  new CandyVending();
        AbstractVending drink =  new DrinkVending();
        candy.process();
        System.out.println("*********************");
        drink.process();
    }
}
```

중복은 제거했지만 구현 클래스들은 다른 상속을 포기해야 한다.     

<br />  


# Abstract Interface Skeletal Implementation   

1. 인터페이스를 만든다.   
2. 인터페이스를 구현하는 추상 메서드를 만들어서 공통 메서드를 구현한다.   
3. 구현 클래스에 추상 클래스를 상속한 private 이너클래스 (Delegator 패턴)를 만든다. 
   그리고 그 클래스를 변수로 가지고 포워딩한다.   
   
<br />  


### 인터페이스    
```
public interface Ivending {
    void start();
    void chooseProduct();
    void stop();
    void process();
}
```   

### 인터페이스를 구현한 추상 클래스     
```
public abstract class AbstractVending implements Ivending {
    public void start() {
        System.out.println("Start Vending machine");
    }
    
    public abstract void chooseProduct();
    
    public void stop() {
        System.out.println("Stop Vending machine");
    }
    
    public void process() {
        start();
        chooseProduct();
        stop();
    }
}
```


### 구현 클래스에 추상 클래스를 상속한 private inner class 구현 & inner class 객체를 변수로 가지고 포워딩         

```
public class CandyVending  implements Ivending {
   
    private class AbstractVendingDelegator extends AbstractVending {
         @Override
         public void chooseProduct() {
            System.out.println("Produce diiferent candies");
            System.out.println("Choose a type of candy");
            System.out.println("pay for candy");
            System.out.println("collect candy");
         }
    }

    AbstractVendingDelegator delegator = new AbstractVendingDelegator();
    
    @Override
    public void start() {
        delegator.start();
    }
    
    @Override
    public void chooseProduct() {
        delegator.chooseProduct();
    }
    
    @Override
    public void stop() {
        delegator.stop();
    }
    
    @Override
    public void process() {
        delegator.process();
    }
}
```




인터페이스로 인한 중복을 추상클래스로 지우고 Delegator 패턴으로 추상 클래스 상속에 대한 단점을 지울수 있다.    


<br />    




<details>
<summary>필기</summary>
<div markdown="1">

![️이펙티브자바_210620_220447_2](https://user-images.githubusercontent.com/33855307/122675505-b0a95b80-d214-11eb-9080-da277f7577f3.jpg)

![️이펙티브자바_210620_220447_3](https://user-images.githubusercontent.com/33855307/122675509-b56e0f80-d214-11eb-9b5b-9ec8dedf3bf2.jpg)

![️이펙티브자바_210620_220447_4](https://user-images.githubusercontent.com/33855307/122675510-b69f3c80-d214-11eb-9b4f-69707d041b5c.jpg)

![️이펙티브자바_210620_220447_5](https://user-images.githubusercontent.com/33855307/122675512-b8690000-d214-11eb-846a-d87a4c5db04f.jpg)

</div>
</details>


