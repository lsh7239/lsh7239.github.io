---
layout: post
title: "MSA - Spring Cloud (1)"
categories:
  - MSA
  - Spring Cloud
  - Config
---

## Spring Cloud Config
**Spring Cloud Config는 분산되어있는 여러 서비스의 설정을 관리할 수 있는 서버이다.**   

---
기존에는 분산된 서비스에 대한 설정과 관리가 필요했으며, 개발자가 서비스의 환경 별로 설정을 변경하는 데 많은 시간이 소요되었다.
Spring Cloud Config가 나타남으로서 이러한 문제를 해결할 수 있게 되었다. 마이크로서비스는 API 요청을 통해 설정 정보를 한꺼번에 받아올 수 있게 되었다. 
마이크로서비스가 시작할 때 Spring Cloud Config에서 관리하는 설정 정보를 요청하게 된다.
또한 Spring Cloud Config 서버를 통해 개발자들은 환경 별로 설정 분리가 가능해졌다.   
![texture theme preview](/img/spring_cloud_config/config01/config01.001.png)   
Git과 같은 형상관리 도구에 Microservice이 사용하는 설정들이 각 환경별로 정의되어있다. 

## Spring Cloud Config Client 구현 방법
Spring Cloud Config Server는 보통 각 프로젝트 혹은 환경 별로 구현되어 있을 것이다. 
개발하는 입장에서는 Config Server를 통해 어떤 방식으로 마이크로서비스가 사용하는 환경 설정을 읽어올 수 있는지가 중요하다.
프로젝트를 maven 기반으로 진행했기 때문에, 샘플 소스 또한 maven 기반으로 작성했다.

* pom.xml의 dependency에 config starter 라이브러리를 추가한다.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```
* src/main/resources/bootstrap.yml에 config server URL을 작성한다.

```yaml
spring:
  application:
    name: ${applicationName}
  profiles:
    active: ${activatedProperties}
  cloud:
    config:
      uri: http://localhost:8888/ #default config server url
```

* Git과 같은 repository 툴에 application-${profiles}.yml 파일을 작성한다.

```yaml
datasource:
  uri: jdbc:h2:~/test
  username: sa
  password:
  driver.class.name: org.h2.Driver
jpa:
  hibernate:
  ddl-auto: update
  database: h2
```