---
title:  "Spring Boot - Spring Boot Actuator"
excerpt: "Spring Boot Actuator : JMX, HTTP 그리고 Spring-Boot-Admin"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Actuator
  - JMX
  - Endpoint
  - Spring Boot Admin
last_modified_at: 2020-10-03. 17:00:00

---

<br>

# 스프링 부트 운영

## Spring Boot Actuator 1부 : 소개

> 애플리케이션 운영 환경에서 유용한 기능을 제공하는 Spring Boot Actuator에 대해 알아보자.

### 개요

- 스프링 부트가 제공하는 엔드포인트와 메트릭스 그리고 그 데이터를 활용하는 모니터링 기능에 대해 학습한다.

- 스프링 부트는 액츄에이터라는 모듈을 제공하는데 이 기능을 사용하면 스프링 부트 운영 중에 우리가 볼 수 있는 유용한 정보를 엔드포인트를 통해 제공한다.

- 엔드포인트를 어떻게 사용하고, 어떤 정보들을 확인 할 수 있는지 알아본다.

- 의존성 추가하기

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
    <version>2.3.1.RELEASE</version>
  </dependency>
  ```

  

### 애플리케이션의 각종 정보를 확인 할 수 있는 Endpoints

- 액츄에이터는 다양한 엔드포인트를 제공해준다.

  - [https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

- JMX 또는 HTTP를 통해 접근이 가능하다.

  - HTTP의 경우 보안으로 기본 값이 대부분 공개여부(`exposed`)가 비활성화 되어있다.
  - 활성화(`enabled`)와 공개여부(`exposed`)를 따로 관리한다.

- 의존성을 추가하면 `shutdown`을 제외한 모든 엔드포인트들이 기본적으로 활성화 상태이다.

  - auditevents : 인증 관련 정보들
  - beans : 등록된 빈들
  - conditions : 어떤 자동설정이 어떤 조건에 의해 적용 됐는지 등
  - configprops : configuration properties 정보
  - httptrace : 최근 100개의 요청정보 등
  - info : 애플리케이션의 기본 정보
  - metrics : 애플리케이션의 핵심이 되는 정보들. 애플리케이션이 사용하고 있는 메모리와 CPU 사용량 등

- 프로퍼티 값으로 활성화 및 공개여부를 변경 할 수 있다.

  - `management.endpoints.enabled-by-default=false`
  - `management.endpoint.info.enabled=true`

- 의존성 추가 후 `HTTP`로 `/actuator` 엔드포인트로 접속시 응답결과

  ```json
  {
  	"_links": {
  		"self": {
  			"href": "http://localhost:8080/actuator",
  			"templated": false
  		},
  		"health-path": {
  			"href": "http://localhost:8080/actuator/health/{*path}",
  			"templated": true
  		},
  		"health": {
  			"href": "http://localhost:8080/actuator/health",
  			"templated": false
  		},
  		"info": {
  			"href": "http://localhost:8080/actuator/info",
  			"templated": false
  		}
  	}
  }
  ```

  

<br>

## Spring Boot Actuator 2부 : JMX와 HTTP

### JMX

- JMX로 노출되는 애플리케이션 정보를 확인 할 수 있는 방법으로 `JConsole`과 `VisualVM` 방법이 있다.

- 방식 1 - Jonsole 사용

  - [https://docs.oracle.com/javase/7/docs/technotes/guides/management/jconsole.html](https://docs.oracle.com/javase/7/docs/technotes/guides/management/jconsole.html)

  - 애플리케이션 실행 후 cmd에 jconsole을 입력하면 아래와 같이 애플리케이션 상태를 확인 할 수 있는 콘솔창이 제공된다.

  - 애플리케이션을 선택 후 connect 한다.

    <img width="895" alt="jconsole" src="https://user-images.githubusercontent.com/58318041/94985503-55561100-0592-11eb-9c50-55475a5c4993.png">

- 방식 2 - VisualVM 사용

  - 기존에 JVM에 포함되어있었으나 이제는 설치 후 실행해야 한다.
  - JConsole과 제공하는 정보는 거의 동일하나 플러그인 추가 기능이 있고, 정보를 더 보기 좋게 제공해준다.



### HTTP 사용

- properties에서 공개 옵션을 조정하여 사용한다.
  - `management.endpoints.web.exposure.include=*`
  - `management.endpoints.web.exposure.exclude=env,beans`
- 위 설정 후 동일하게 `/actuator` 엔드포인트로 접속시 응답이 전부 나타난다.
- 그러나 이런 정보들은 외부로 노출이 되면 안되기 때문에 스프링 시큐리티를 적용해서 어드민 유저만 접속 가능하도록 설정해줘야 한다.

<br>



## Spring Boot Actuator 3부: Spring-Boot-Admin

### 개요

- 스프링 부트 어드민은 스프링에서 제공하는 프로젝트가 아니고 제 3자가 오픈소스로 제공하는 애플리케이션이다.
- 우리가 설정한 스프링 액츄에이터 정보를 UI에서 확인할 수 있게 해주는 툴 및 애플리케이션이다.
- 스프링 부트 어드민을 위한 서버와 우리의 액츄에이터 정보가 있는 클라이언트 서버 두 대가 필요하다.



### 어드민 서버 설정

- 의존성을 추가한다.

  ```xml
  <dependency>
  	<groupId>de.codecentric</groupId>
  	<artifactId>spring-boot-admin-starter-server</artifactId>
  	<version>2.0.1</version>
  </dependency>
  ```

- `@SpringBootApplication`에 `@EnableAdminServer` 추가한다.

- 어드민 서버도 민감정보를 포함하기 때문에 스프링 시큐리티로 보안 설정을 해야 한다.

### 클라이언트 서버 설정

- 의존성을 추가한다.

  ```xml
  <dependency>
  	<groupId>de.codecentric</groupId>
  	<artifactId>spring-boot-admin-starter-client</artifactId>
  	<version>2.0.1</version>
  </dependency>
  ```

- properties에 이 클라이언트 애플리케이션이 접속할 서버의 주소를 적어준다.

  ```
  spring.boot.admin.client.url=http://localhost:8080 management.endpoints.web.exposure.include=*
  ```



### 스프링 부트 어드민 접속화면

- ![admin1](https://user-images.githubusercontent.com/58318041/94986364-2d1de080-0599-11eb-89cb-684d764f37b7.png)

  ![admin2](https://user-images.githubusercontent.com/58318041/94986375-37d87580-0599-11eb-906e-04d28bedaa7b.png)

