---
title:  "Spring Boot - Spring-Boot-Devtools"
excerpt: "스프링 부트 활용 : Spring-Boot-Devtools"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring-Boot-Devtools
last_modified_at: 2020-09-01. 17:00:00

---

<br>

# 스프링 부트 활용

## Spring-Boot-Devtools

### 제공하는 주요 기능

- 스프링 부트가 제공하는 옵셔널한 툴이며, 의존성 추가로 사용 할 수 있다.

- 주요 기능

  ```java
  1. Property Defaults
  2. Automatic Restart
  3. Live Reload
  4. Global Settings
  5. Remote Applications
  ```



### Property Defaults

- `spring-boot-devtools` 의존성만 추가해도, 기본적으로 적용되는 `properties`들이 바뀌는게 있다.
- 주로 개발 시 편리성을(바뀐 것을 바로바로 확인 할 수 있는 등) 위해서 캐시 설정을 개발환경에 맞도록 변경한다.
- 개발 시점과 배포 시점에 다른 설정을 기본적으로 개발 단계에 맞춰 설정 해준다. 예를 들어 템플릿 엔진의 캐싱 기능을 개발 단계에서 자동으로 꺼주는 역할을 하고, 개발자는 설정 파일을 건드리지 않아도 된다.



### Automatic Restart

- 코드 수정 후(필드 값 변경 등) 저장을 하면, 클래스패스에 존재하는 파일의 변경을 감지하고, 자동으로 서버를 재시작 해준다. (`Restart`)
- 재시작 해주는 속도가 톰캣을 껏다 켜는 속도(`project run`)보다 훨씬 빠르다.
  - 왜 빠를까? 두가지의 클래스 로더를 사용하기 때문이다.
  - 라이브러리나 의존성 과 같이 바뀌지않는 클래스를 관리하는 `Base Class Loader`
  - 애플리케이션 관련 클래스를 읽는 `Restart Class Loader`



### Live Reload

- 서버를 재시작 해주는 기능은 `Automatic Restart`이고, 브라우저를 자동으로 리프레시 해 주는 기능은 `Live Reload`이다.



### Global Setting

- `~/.spring-boot-devtools.properties`
- 외부 설정 파일의 우선 순위 중 1순위 인 글로벌 설정 기능이다.

<br>

## References

- [https://velog.io/@bread_dd/Spring-Boot-Devtools](https://velog.io/@bread_dd/Spring-Boot-Devtools)
- 스프링 부트 개념과 활용 - 백기선 님

