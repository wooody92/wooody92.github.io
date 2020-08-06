---
title:  "Spring Framework 학습정리 - 10"
excerpt: "Resource 추상화"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - Resource
  - ApplicationContext
  - WebApplicationContext
  - ServletContextResource
  - ClassPathResource
last_modified_at: 2020-08-06 17:00:00

---

# Resource / Validation

## Resource 추상화

### 서론

- 이전까지는 `ApllicationContext`가 단순한 `BeanFactory`가 아니고 다른 부가기능들을 갖고 있는 것을 학습했다.
- 이번에는 `Spring Reference`에서 많은 양을 차지하는 추상화 중 일부분을 학습할 것이다.

### Resource 특징

- `java.net.URL`을 추상화 한 것이다.
- 스프링 내부에서 많이 사용하는 인터페이스이다.

### 추상화 한 이유

- 클래스패스 기준으로 리소스를 가져오는 기능이 없었다.
- 기존 방식은 구현이 복잡하고 편의성 메서드가 부족하여, `Resource` 관련 클래스를 만들어 통일시켰다.
- 주요 메서드 : `getInputStream()`, `exist()`, `isOpen()`, `getDescription()` : 전체 경로 포함한 파일 이름 또는 실제 URL

### 구현체

- `UrlResource` : `java.net.URL` 참고, 기본으로 지원하는 프로토콜 `http`, `https`, `ftp`, `file`, `jar`.
- `ClassPathResource` : 지원하는 접두어 `classpath:`
- `FileSystemResource`
- `ServletContextResource` : 웹 애플리케이션 루트에서 상대 경로로 리소스 찾는다.

### 리소스 읽어오기

- `Resource`의 타입은 `location` 문자열과 `ApllicationContext` 타입에 따라 결정 된다.
  - `ClassPathXmlApplicationContext` -> `ClassPathResource`
  - `FileSystemXmlApplicationContext` -> `FileSystemResource`
  - `WebApplicationContext` -> `ServletContextResource`
- `ApplicationContext`는 대부분의 경우에 `WebApplicationContext`를 사용하기 떄문에 `ServletContext`를 사용한다. 하지만 리소스가 어디서 오는지 접두어를 명시적으로 적어주는 것이 좋다.
- `ApplicationContext`의 타입에 상관없이 리소스 타입을 강제하려면 `java.net.URL` 접두어(+ classpath:)중 하나를 사용할 수 있다.
  - `classpath:me/whiteship/config.xml` -> `ClassPathResource`
  - `file:///some/resource/path/config.xml` -> `FileSystemResource`

### 예시

- 스프링 부트 기반의 애플리케이션을 작성할 때, 클래스패스 기준으로 많은 리소스를 사용하므로 `prefix`로 `classpath:`를 명시적으로 사용하는 것을 권장한다. 그냥 리소스의 이름만 쓰면 `ServletContextResource`로 `resolving` 된다.

  ```java
  @Component
  public class AppRunner implements ApplicationRunner {
  
      @Autowired
      ApplicationContext resourceLoader;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          // WebApplicationContext
          System.out.println(resourceLoader.getClass());
  
          // ClassPathResource
          Resource resource = resourceLoader.getResource("classpath:test.txt");
          System.out.println(resource.getClass());
          System.out.println(resource.exists());
          System.out.println(resource.getDescription());
  
          // ServletContextResource
          Resource resource2 = resourceLoader.getResource("test.txt");
          System.out.println(resource2.getClass());
          System.out.println(resource2.exists());
          System.out.println(resource2.getDescription());
      }
  }
  ```

  ```java
  // result
  class org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext
  class org.springframework.core.io.ClassPathResource
  true
  class path resource [test.txt]
  class org.springframework.web.context.support.ServletContextResource
  false
  ServletContext resource [/test.txt]
  ```

