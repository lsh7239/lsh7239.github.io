---
layout: post
title: "Spring Proxy와 AOP 공부하기(1)"
categories:
  - spring
---

### Spring Proxy Pattern
스프링에서 Proxy 패턴을 쓰는 이유는 **핵심 업무 외에 공통 기능을 대신 수행하기 위해서**이다.
기존 프로젝트에서 특정 클래스가 호출되면 무조건 로그를 출력하도록 했었는데, 이를 Proxy 패턴으로 구현한다면 다음과 같이 구현된다.

> Proxy 가 수행될 대상 인터페이스이다. 이 인터페이스는 logic()이라는 클래스가 실행될 때 마다 data 내용을 로그로 출력하도록 한다.

```java
public interface TestService{
    public void logic(Data data);
}
```

> 실제 업무가 수행되는 클래스를 생성한다.

```java
@Service
public class TestLogic implements TestService{
    
    @Override
    public void logic(Data data){
     // logic 내용
    }
}
```

> Proxy 클래스를 생성한다. 이 Proxy 클래스에서 data 내용을 로그로 출력 하는 공통 기능을 수행한 후 실제 업무 로직을 수행할 수 있도록 까지 구현되어 있다.

```java
@Primary
@Component
public class ProxyTestLogic implements TestService{
    
    private TestService delegator;

    public ProxyTestLogic(TestService delegator){
        this.delegator = delegator;
    }

    @Override
    public void logic(Data data){
        System.out.println(data.toString()); // 공통 기능 수행
        delegator.logic(data); // 실제 업무 로직 수행 
    }
}
```
---

실제 비즈니스 로직 외에 공통 기능을 분리하는 컨셉이 **Spring AOP**이다. 공통 컴포넌트를 제작할 때, Spring AOP를 활용해서 작업했었는데, 
AOP가 어떤 원리로 동작하는지 자세하게 알지 못했다.

