---
title:  "Spring Boot - 예외처리"
excerpt: "스프링 Web MVC : ExceptionHandler"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Web MVC
  - ExceptionHandler
  - RestControllerAdvice
last_modified_at: 2020-09-06. 15:30:00

---

<br>

# 스프링 부트 활용

## Spring Web MVC 9부 : ExceptionHandler

> 스프링 부트가 지원하는 스프링 MVC ExceptionHandler에 대해 알아보자.

### 스프링 @MVC 예외처리 방법

- `@RestControllerAdvice`, `@ExceptionHandler`

- 스프링 부트 앱을 실행하면 기본적으로 `BasicErrorController`에서 제공하는 에러 핸들러가 등록되어 있다. (화이트 라벨 페이지)

- 아래와 같이 컨트롤러 클래스 내부에 선언된 `@ExceptionHandler`는 해당 클래스에서 발생하는 예외만 처리할 수 있다.

- 애플리케이션 전역에서 발생하는 예외를 `@ExceptionHandler`로 처리하고 싶으면,  `@RestControllerAdvice`의 클래스를 생성하고 그 클래스에 `@ExceptionHandler`들을 정의하여 사용할 수 있다.

  ```java
  @RestController
  public class SampleController {
  
      @GetMapping("/hello")
      public String hello() {
          throw new SampleException();
      }
  
      @ExceptionHandler(SampleException.class)
      public ResponseEntity<AppError> sampleError(SampleException e) {
          AppError appError = new AppError();
          appError.setMessage("error.app.key");
          appError.setReason("error test");
          return ResponseEntity.ok(appError);
      }
  }
  ```

  ```java
  public class SampleException extends RuntimeException {
  }
  ```



### 스프링 부트가 제공하는 기본 예외 처리기

- `BaseErrorController` : `HTML`과 `JSON` 응답을 지원한다.

- `ErrorController`를 구현하여 커스터마이징 할 수 있다.

  ```java
  @Controller
  @RequestMapping("${server.error.path:${error.path:/error}}")
  public class BasicErrorController extends AbstractErrorController {
    ...
  }
  ```

  

### 커스텀 에러 페이지

- 상태 코드 값에 따라 원하는 페이지를 간단하게 보여 줄 수 있다.
- `resources/static/error` 디렉토리에 상태코드의 `.html`을 만들면 해당 상태코드에 따라 페이지를 보여준다.
  - `404.html`, `5xx.html`
- `ErrorViewResolver` 구현

