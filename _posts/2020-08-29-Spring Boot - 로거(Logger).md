---
title:  "Spring Boot - 로거(Logger)"
excerpt: "스프링 부트 활용 : 기본 로거 설정(로그레벨, 로그파일)"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Logging Facade
  - Logger
  - SLF4j
  - Log4J2
  - Logback
  - 로그레벨
  - 로그파일
last_modified_at: 2020-08-29. 18:00:00

---

# 스프링 부트 활용

## 로깅 1부 : 스프링 부트 기본 로거 설정

### 로깅 퍼사드(Logging Facade) vs 로거(Logger)

- `Logging Facade` : 실제 로그를 찍는 게 아니고, 로거 API를 추상화 한 인터페이스이다. - `commons logging`,  `SLF4j`
- `Logger` : 로깅 퍼사드의 구현체이다. - `JUL`, `Log4J2`, `Logback`
- 로깅 퍼사드는 의존성에 근거하여 로거를 선택해서 사용한다.
- 스프링 부트에서는 로깅 퍼사드로 기본적으로 `commons logging`을 사용하나 보통 `SLF4`j를 사용한다.
- 스프링 부트에서 기본적으로 찍어주는 로그 설정은 무엇있까?`SLF4j`의 구현체인 `Logback`이다.



### 로그 레벨 설정

- 로그레벨 설정에 따라 원하는 로그레벨까지 볼 수 있다.  `logging.level.root=debug`로 설정하면 `embedded container`, `Hibernate`, `Spring Boot`까지를 디버그 레벨로 바꾸어 보여준다. 

  ```java
  private Logger logger = LoggerFactory.getLogger(ApplicationRunner.class);
  ```

  ```java
  // application.properties
  logging.level.root=debug
  
  // package 단위로 로그레벨을 설정 할 수 있다.
  // logging.level.dev.springboot=debug
  ```

- 모든 메세지를 보고 싶다면, `logging.level.root=trace`로 설정 할 수 있다.



### 로그 파일

- 기본적으로 로그는 콘솔창에만 찍히는데, 파일로 남기고싶다면 아래와 같이 설정 할 수 있다.

  ```java
  // application.properties
  logging.file.name=my-logs
  logging.file.path=my-log-directory
  ```

- `logging.file.name=my-logs`로 설정하면 `my-logs`이라는 이름으로 로그가 저장된다.

- `logging.file.path=my-log-directory`로 설정하면 `my-log-directory` 디렉토리가 생성되고 `spring.log`라는 로그파일이 생성되어 로그가 저장된다.

- 기본적으로 10MB마다 롤링이되고 설정한 용량이 넘어가면 아카이빙된다. 물론 `logging.file.*`에서 원하는 값으로 설정 할 수 있다.



## 로깅 2부 : 커스터마이징

### 커스텀 로그 설정 파일 사용하기

- `Log4j2` : `log4j2-spring.xml`

- `Logback` : `logback-spring.xml`

  ```xml
  // logback-spring.xml
  <?xml version="1.0" encoding="UTF-8"?>
  <configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    <include resource="org/springframework/boot/logging/logback/console-appender.xml" />
    <root level="INFO">
      <appender-ref ref="CONSOLE" />
    </root>
    <logger name="dev.springboot" level="DEBUG"/>
  </configuration>
  ```

