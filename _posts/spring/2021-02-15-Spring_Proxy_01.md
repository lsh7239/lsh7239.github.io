---
layout: post
title: "Spring Proxy와 AOP 공부하기(1)"
categories:
  - spring
---

## Proxy Pattern
**Proxy 패턴은 클라이언트가 사용하려고 하는 인터페이스를 구현한 구현체가 클라이언트의 요청을 대신 위임받아 부가적인 기능을 수행한 뒤, 
클라이언트의 요청을 실제 핵심 기능을 수행하는 구현체로 전달해주는 것이다.**

![texture theme preview](/img/spring/proxy01.png)   
* proxy : 실제 대상인 것 처럼 클라이언트의 요청을 위임받아 처리하는 구현체
* target : proxy를 통해 최종적으로 요청을 받아 로직을 수행하는 구현체

Proxy 패턴을 쓰는 이유는 **핵심 업무 외에 공통 기능을 대신 수행하기 위해서**가 주 목적이라고 생각한다.
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

## proxy 생성 방법
### 다이나믹 프록시
위의 방법으로 proxy 객체를 일일히 구현한다면, 인터페이스의 오퍼레이션이 증가할수록 양이 많아진다는 것과 코드가 중복된다는 단점을 가진다.
이러한 문제를 해결하기 위해 JDK의 다이나믹 프록시를 통해 프록시를 생성할 수 있다.

* InvocationHandler를 상속받은 클래스를 구현한다. 

해당 클래스는 invoke 메소드를 오버라이딩 받게 되는데, 이 부분에서 부가적인 기능을 수행한 후 타겟 클래스의 메소드를 호출하면 된다.
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class LogHandler implements InvocationHandler {

    private Logger log = LoggerFactory.getLogger(LogHandler.class);

    private Object target;
    public LogHandler(Object target){
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log.info(String.format("method : %s",method.getName()));
        Object ret = method.invoke(target,args);
        return ret;
    }
}
```
아래와 같이 Proxy.newProxyInstance() 메소드를 호출하여 다이나믹 프록시를 생성할 수 있다.
```java
public class ProxyTest {

    @Test
    public void proxyTest(){
        BoardService boardService = (BoardService) Proxy.newProxyInstance(getClass().getClassLoader(),
                new Class[]{BoardService.class},
                new LogHandler(new BoardLogic()));
        boardService.registerBoard(new Board(1l,"title","sehee",new Timestamp(System.currentTimeMillis())));
    }
}
```
수행 결과를 확인해보면 boardService.registerBoard() 메소드를 수행했는데 LogHandler에서 출력한 로그를 확인할 수 있다.
![texture theme preview](/img/spring/proxy02.png)  

### ProxyFactoryBean
JDK 다이나믹 프록시를 통해 프록시 객체를 생성하게 되면, 타겟 클래스를 지정해주어야 하는 단점이 있다. 스프링은 이러한 문제를 해결하기 위해 
ProxyFactoryBean을 제공한다.

* MethodInterceptor를 상속받은 클래스를 구현한다.

아래 예시에서 기존과 다르게 target 오브젝트를 명시할 필요가 없어졌다.
```java
import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class LogAdvice implements MethodInterceptor {

    private Logger log = LoggerFactory.getLogger(LogAdvice.class);

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        log.info(String.format("method : %s",invocation.getMethod().getName()));
        return invocation.proceed();
    }
}
```
Target에 구현체를 넣고, addAdvice 메소드에 위에서 구현한 LogAdvice 생성자를 넣으면 ProxyFactoryBean 클래스를 통해 프록시 객체를 생성할 수 있다.
```java
public class ProxyTest {

    @Test
    public void proxyFactoryBeanTest(){
        ProxyFactoryBean pfBean = new ProxyFactoryBean();
        pfBean.setTarget(new BoardLogic());
        pfBean.addAdvice(new LogAdvice());

        BoardService boardService = (BoardService) pfBean.getObject();
        boardService.registerBoard(new Board(1l,"title","sehee",new Timestamp(System.currentTimeMillis())));
    }
}
```
수행 결과 LogAdvice서 출력한 로그를 확인할 수 있다.
![texture theme preview](/img/spring/proxy03.png)  

### 용어 정리
> Advice : target 오브젝트에 적용하는 부가 기능을 담은 오브젝트

> PointCut : 메소드 선정 알고리즘

> Advisor : PointCut + Advice

**프록시는 클라이언트로부터 요청을 받으면 먼저 포인트컷으로 가서 해당 부가기능을 사용할 메소드인지 먼저 확인한다. 
적용 대상 메소드라면 어드바이스를 호출한다.**

실제 비즈니스 로직 외에 공통 기능을 분리하는 컨셉이 **Spring AOP**이다. 공통 컴포넌트를 제작할 때, Spring AOP를 활용해서 작업했었는데, 
AOP가 어떤 원리로 동작하는지 자세하게 알지 못했다.

