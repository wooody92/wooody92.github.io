---
title:  "Spring Boot - 독립적으로 실행 가능한 JAR"
excerpt: "스프링 부트 원리 : JAR 파일 구조와 동작 원리"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Jar
last_modified_at: 2020-08-24. 15:20:00

---

# 스프링 부트 원리

## 독립적으로 실행 가능한 JAR

### JAR 파일 구조와 동작 원리 (스프링 부트의 전략)

- `cli`로 `mvn packgage(= ./gradlew build)`를 입력하면 실행 가능한 `JAR` 파일 하나가 생성된다.

- `JAR` 파일안에 내가 만든 애플리케이션 클래스와 `dependency`로 주입받은 라이브러리가 패키지로 구분되어 들어있다.

- 기본적으로 자바에는 내장 `JAR`를 로딩하는 표준적인 방법이 없다.

- 스프링 부트는 `JAR`안에 내장 `JAR`로 묶어놓고, 그것을 읽을 수 있는 로더(`org.springframework.boot.loader.jar.JarFile`)를 사용해서 내장 `JAR`를 읽는다.

- 메인 메서드를 갖고있는 클래스는 런처(`org.springframework.boot.loader.Launcher`)를 사용해서 실행한다. 

- `spring-boot-maven-plugin`이 `JAR` 파일에 아래와 같은 패키지 구조로 만들어준다.

  ![JAR](https://user-images.githubusercontent.com/58318041/91009581-d3caa500-e61b-11ea-8f3a-9031c64dc22b.png)

  - 기본적인 `JAR` 실행방식 : `MANIFEST.MF` -> `Main-Class: org.springframework.boot.loader.JarLauncher` 실행 -> `Launcher`가 내장 `JAR`를 읽는 `org.springframework.boot.loader.jar.JarFile` 실행

- 이 모든 방식은 스프링 부트가 독립적인 애플리케이션을 만들기 위한 전략이다.



## 스프링 부트 원리 정리

### 의존성 관리

- 의존성에 `spring-boot-starter-web`만 추가해도 기본적으로 필요한 많은 외부 라이브러리를 가져온다.
- 어떻게? `maven - parent - spring-boot-dependencies`에서 스프링부트가 사용하는 주요 라이브러리의 버전을 관리한다.

### 자동 설정

- `EnableAutoConfiguration`의 역할
- `ComponentScan`으로 빈 스캔 한 번 + `EnableAutoConfiguration`으로 스캔 한 번하여 총 두 번 스캔한다.

### 내장 웹 서버

- 스프링 부트 자체가 서버가 아니라 내장 서버(`tomcat`, `netty`, `jetty` 등)를 자동 설정을 사용하여 실행하는 것이다.

### 독립적으로 실행 가능한 JAR

- `JAR` 파일의 구조와 동작 원리를 이해한다.