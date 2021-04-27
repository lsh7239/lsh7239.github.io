---
layout: post
title: "Spring의 DispatcherServlet과 Spring Boot에서 동작 과정"
categories:
  - spring
---

## Spring MVC
정보 Model, 화면 출력 로직 View, 제어 로직 Controller 세 가지로 프레젠테이션 계층을 분리하고 
이 세 가지 요소가 서로 협력해서 하나의 웹 요청을 처리하도록 한다.

### Front Controller
MVC 아키텍처는 프론트 컨트롤러 패턴과 함께 사용된다. 
- 프론트 컨트롤러 패턴? 중앙집중형 컨트롤러를 프레젠테이션 계층의 가장 앞에 두어서 서버로 들어오는 모든 요청을 먼저 받아서 처리하도록 한다.
- 클라이언트가 보낸 요청을 받아 공통적인 작업을 먼저 수행한 후에 적절한 세부 컨트롤러로 작업을 위임해주고, 클라이언트에게 보낼 뷰를 선택해서 최종 결과를 생성하는 작업 수행
- 예외 발생 시 일관된 방식으로 처리

### DispatcherServlet
- Servlet Container가 생성하고 관리하는 오브젝트
- MVC 아키텍처로 구성된 프레젠테이션 계층을 만들 수 있도록 설계

![texture theme preview](/img/spring/dispatcher01.jpg)

#### 1. DispatcherServlet의 HTTP 요청 접수
- 클라이언트로부터 HTTP 프로토콜을 통해 들어오는 요청을 서블릿 컨테이너에서 일차적으로 받고, 
이 HTTP 요청이 DispatcherServlet에 할당된 것이라면 HTTP 요청을 전달해준다.

#### 2. DispatcherServlet에서 컨트롤러로 HTTP 요청 위임
- HandlerMapping에 URL과 요청 정보를 기준으로 어떤 컨트롤러를 사용할 것인지 전달한다.
- DispatcherServlet은 오브젝트 어댑터 패턴을 사용해서 특정 컨트롤러를 호출한다.
- 오브젝트 어댑터 패턴이란?

#### 3. 컨트롤러의 모델 생성과 정보 등록
- 컨트롤러가 서비스 레이어 계층과 레파지토리 레이어 계층의 메소드를 호출함으로써 리턴하고자하는 모델을 생성한다.

#### 4. 컨트롤러 결과 리턴 : Model & View
- JSP를 사용 시, ModelAndView에서 JSP의 view 이름을 가져가서 생성하도록 했다.
- 뷰 작업을 통한 최종 결과물은 HttpServletResponse 안에 담긴다.
- JSON 도 view에 속한다.

#### 5. HTTP 응답 돌려주기
- 후처리기에서 후속 작업 진행 후 HttpServletResponse 에 담긴 최종 결과를 서블릿 컨테이너에 돌려준다.
- 서블릿 컨테이너는 HttpServletResponse에 담긴 정보를 HTTP 응답으로 만들어서 클라이언트에 전송한다.

### Spring Boot가 API 요청을 받는 과정
