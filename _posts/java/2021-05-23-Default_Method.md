---
layout: post
title: "Java8의 Default Method"
categories:
  - java
---

## 인터페이스의 Default Method
자바 8에서는 인터페이스에서 디폴트 메소드를 허용하도록 했다. 
**기존 인터페이스를 확장해서 새로운 기능을 추가하기 위해서**이다. 기존 인터페이스의 이름과 추상 메소드의 변경 없이 디폴트 메소드만 추가할 수 있기 때문에 
이전에 개발한 구현 클래스를 그대로 사용할 수 있으면서 개발하는 클래스는 디폴트 메소드를 활용할 수 있다.

### 기존 인터페이스의 문제점
인터페이스에 새로운 메소드 추가 시, 인터페이스를 상속받아서 사용하고 있었던 클래스들이 모두 @Override 가 되지 않아 컴파일 시 오류가 발생할 수 있다.

이를 방지하기 위한 디폴트 메소드 작성 방법은 다음과 같다.
```java
public interface TestInterface {
    public void execute(); // 기존에 있었던 메소드
    public default void newExecute(){
        
    } // 신규 추가된 메소드
}
```

```java
public class TestClass implements TestInterface{
    @Override
    public void execute(){
        // 기존 구현 메소드
    }
    
    // 따로 newExecute() 메소드를 오버라이드하지 않아도 오류가 나지 않는다.
}
```
디폴트 메소드인 newExecute()는 TestInterface에 따로 정의된 것을 사용해도 되고, 필요애 따라 재정의할 수 있다.

### 인터페이스 다중 상속으로 인한 모호성 해결
다음과 같은 질문을 받았다.
- 디폴트 메소드가 정의되어있는 인터페이스를 여러개 상속받았을 때, 이때 디폴트 메소드 명칭이 모두 동일하다면 어떤 순서대로 동작하는가?

현재 업무에서 디폴트 메소드를 사용한 경험이 없었는데, 어떻게 동작하는지 궁금증이 생겨 테스트를 해보았다.

아래와 같이 인터페이스를 하나 더 생성한 후 TestClass에 추가로 implements를 하게 된다면 다음과 같이 오류가 발생한다.
```java
public interface TestInterface2 {
    public void execute(); // 기존에 있었던 메소드
    public default void newExecute(){
        // 신규 추가된 메소드
    } 
}
```

![texture theme preview](/img/java/defaultMethod01.png)

오류 내용은 디폴트 메소드가 다중으로 선언되어있어 모호하니, @Override 하여 모호함을 해결해야 한다. 

```java
public class TestClass implements TestInterface, TestInterface2 {

    // 필수
    @Override
    public void newExecute() {
        // logic
    }
}
```

#### 만약 모든 인터페이스의 디폴트 메소드를 실행하고 싶다면?
@Override 한 메소드 안에 다음과 같이 작성하면 인터페이스에 정의한 디폴트 메소드의 로직들이 수행된다.

```java
public class TestClass implements TestInterface, TestInterface2 {

    @Override
    public void newExecute() {

        TestInterface.super.newExecute();
        TestInterface2.super.newExecute();
    }
}
```

### Super Class 와 Abstract Class
그렇다면, 상위 클래스에서 동일한 명칭의 메소드가 있을 때는 어떻게 동작하게 될까?

아래와 같이 상위 클래스를 생성하고 TestClass에 extends 하였다. 
그 결과, 위에서 발생했던 모호함과 관련된 오류는 발생하지 않고 상위 클래스에 작성한 내용이 출력되었다.

```java
public class TestSuper {

    public void newExecute() {
        System.out.println("testSuper class test()");
    }
}
```
<br> ![texture theme preview](/img/java/defaultMethod02.png)

만약 일반 클래스가 아니라 abstract class 를 상속받았다면, 일반적으로 구현된 메소드는 일반 클래스와 동일하게 동작하였으나
abstract 메소드로 작성 시, 상속받은 클래스 메소드에 한해서 @Override 하라는 오류 메세지가 나왔다.
인터페이스의 디폴트 메소드와 관련된 오류 내용은 확인되지 않았다.

```java
public abstract class TestAbstract{

    public abstract void newExecute();
}
```
<br> ![texture theme preview](/img/java/defaultMethod03.png)


---
자바 8에서부터 stream, default method, LocalTime, LocalDate, Optional 등 새로운 기능들이 등장했다. 
해당 기능들이 기존에 어떤 문제 때문에 나타나게 되었는지 꼭 기억해야겠다.
