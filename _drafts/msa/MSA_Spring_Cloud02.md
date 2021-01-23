---
layout: post
title: "MSA - Spring Cloud (2)"
categories:
  - MSA
  - Spring Cloud
  - Service Registry
---

## Spring Cloud Eureka
**Spring Cloud Eureka는 분산되어있는 여러 서비스의 인스턴스를 관리한다.**   

---
마이크로서비스 환경은 서로 다른 서버에서 호스팅 되는 여러 서비스로 구축된다. 마이크로서비스는 호출 대상 서비스의 인스턴스 위치(IP, PORT)를 알아야지만 API 호출을 통해 원하는 로직을 수행할 수 있게 된다.
인스턴스 정보가 동적으로 변경될 수 있는 Auto Scaling 적용된 마이크로서비스일 경우에는 인스턴스 위치 정보를 매번 수정해야 하는 번거로움이 발생할 수 있다. 
Eureka와 Service Discovery가 마이크로서비스의 이름과 인스턴스의 위치를 관리하여 이러한 문제를 해결할 수 있도록 한다.   
![texture theme preview](/img/spring_cloud_config/eureka01/eureka01.001.png)  