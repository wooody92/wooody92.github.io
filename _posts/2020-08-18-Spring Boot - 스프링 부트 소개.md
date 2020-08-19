---
title:  "Spring Boot - 스프링 부트 소개"
excerpt: "스프링 부트 시작하기 : 스프링 부트 소개"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
last_modified_at: 2020-08-18. 18:00:00

---

# 스프링 부트 시작하기

## 소개

### 강의정보

- 스프링 부트 개념과 활용
- 백기선

### 학습 목표

- 스프링 부트 핵심 원리를 이해한다.
  - 의존성 관리
  - 자동 설정
  - 내장 웹서버
  - 독립적으로 실행 가능한 JAR
- 스프링 부트를 활용하여 제공하는 주요 기능을 사용한다.
  - 스프링 부트 핵심 기능 : `SpringApplication`, `외부 설정`, `로깅`, `테스트`, `Spring-Boot-Devtools`
  - 각종 기술 연동 : `스프링 웹 MVC`, `스프링 데이터`, `스프링 시큐리티`, `REST 클라이언트`
- 스프링 부트 운영에 대해 이해한다.
  - 엔드포인트
  - 매트릭스
  - 모니터링



## 스프링 부트 소개

### 스프링 부트

- 제품(`production`) 수준의 스프링 기반 프로젝트를 만들 때 빠르게 만들 수 있게 도와준다.
- 스프링 프레임워크 설정에 대한 컨벤션과 3rd 파티에 대한 기본 설정도 제공해준다. (`톰캣 8080 포트`에 대한 설정 등)
- 스프링 부트는 기본 설정을 제공해주지만 사용자가 원하는 요구사항에 맞게 쉽고 빠르게 변경할 수 있는 장점이 있다.

### 스프링 부트 패키지 구조

- `Application`이 동작하는 메인 클래스는 `java` 디렉토리 아래 가장 상위의 `default` 패키지에 위치하는 게 좋다.

- `@SpringBootApplication` 내부에서 동작하는 컴포넌트 스캔의 범위 때문에 불필요한 패키지를 스캔하지 않도록 한다.

  ```java
  package dev.springboot.study;
  
  @SpringBootApplication
  public class Application {
  
      public static void main(String[] args) {
          SpringApplication.run(Application.class, args);
      }
  }
  ```



