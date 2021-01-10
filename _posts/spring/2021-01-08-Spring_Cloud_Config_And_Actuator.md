---
layout: post
title: "Spring Cloud Config 정보를 서비스 중단 없이 반영하기"
categories:
  - Spring Cloud Config
  - Spring Actuator
  - Spring Boot
---
### @RefreshScope와 Actuator를 활용하기

---
현재 회사에서 구동되고 있는 마이크로서비스들은 Spring Cloud Config를 통해 git에서 각 어플리케이션 설정 정보를 부트가 실행될 때 설정 정보를 관리하는 빈 객체에 해당 정보들을 가지고 있도록 한다.
종종 기존 설정 정보를 변경해야 하는 경우가 발생한다. 

설정 정보를 변경한다고 해서 이러한 정보를 가지고 있는 빈 객체가 바로 변경되지 않는다. 
설정 정보를 바꾸기 위해서는 서비스를 재시작하는 것이 가장 간단한 방법이지만, 업무 특성 상 모든 서비스의 인스턴스를 동시에 재시작 할 수 없었다. 
그래서 업무에서는 설정 정보 변경이 있을 시, 해당 서비스를 롤링-배포 방식으로 서비스 인스턴스가 무중단되면서 변경된 설정정보를 읽어올 수 있도록 했다.

하지만 불편함을 느껴 **서비스 무배포, 무중단 환경에서 변경된 설정 정보를 읽어올 수 있도록 하는 방식**을 찾아보았다.

'클라우드 네이티브 자바' 책을 참고한 결과, 두 가지 방식이 존재한다.

1. @RefreshScope
2. Spring Cloud Bus

이 중, **@RefreshScope**를 활용해보았다.

---
@RefreshScope 방식은 서비스의 특정 REST API를 호출하여 새로운 설정 정보를 읽어오고, 기존 설정 정보를 가지고 있는 빈 객체를 재생성한다.

#### 라이브러리 의존성 추가
> 가장 먼저 Spring Actuator 의존성을 추가했다. 샘플 프로젝트는 maven으로 진행했다. Actuator는 서비스의 현재 상태(DB status, Thread)를 API 호출을 통해서 확인할 수 있다.

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

#### application.yml에 Actuator Endpoint 지정
> 위에서 말했듯 Actuator는 서비스의 REST API를 호출하는 형태이다. 이 REST API 중 'refresh'를 호출하면 @RefreshScope가 적용되어있는 클래스에 새로운 설정 정보를 읽을 수 있도록 한다.
> application.yml에 Actuator Endpoint를 지정해야 한다.

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "refresh" #혹은 "*"
```

#### 변경하고자 하는 클래스에 @RefreshScope 설정
> 테스트를 위해 @Value 어노테이션을 단 필드를 선언했다. 

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.example.board.domain.spec.BoardService;
import com.example.board.entity.Board;

@RestController
@RequestMapping(value="board")
@RefreshScope
public class BoardResource {
	
	private BoardService boardService;

	@Value("${datasource.username}")
	private String username;

	@Value("${datasource.password}")
	private String password;
	
	public BoardResource(BoardService boardService) {
		this.boardService = boardService;
	}

	@GetMapping
	public String refreshScope(){
		return String.format("[%s : %s]",username,password);
	}
...
```

