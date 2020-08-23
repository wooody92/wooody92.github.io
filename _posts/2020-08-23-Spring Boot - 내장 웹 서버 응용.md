---
title:  "Spring Boot - 내장 웹 서버 응용"
excerpt: "스프링 부트 원리 : 서블릿 컨테이너 활용 & HTTPS, HTTP2 설정"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Tomcat
  - Undertow
  - Jetty
  - ServletContainer
last_modified_at: 2020-08-23. 19:00:00

---

# 스프링 부트 원리

## 내장 웹 서버 응용 1부 : 컨테이너와 포트

### Servlet Conatiner 사용해보기

- 내장 서블릿 컨테이너를 응용해서 사용하는 방법에 대해 알아보자.

- `Servlet` 기반의 `Web MVC Application`을 개발할 때 기본적으로 `spring-boot-starter-web`에 내장되어 있는 `Tomcat`을 쓴다. (자동 설정으로 불러온다.)

- 그러면 톰캣이 아닌 다른 서블릿 컨테이너를 사용하고 싶다면 어떻게 해야 할까?

  - `spring-boot-starter-web`에 내장 된 `Tomcat`을 `exclusion` 해주고 다른 서블릿 컨테이너 `dependency`를 추가한다.

  ```java
    <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
          <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
          </exclusion>
        </exclusions>
      </dependency>
  
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jetty</artifactId>
      </dependency>
  ```

- 기본적으로 `dependency`에 `Web` 관련 기술이 들어가있으면 스프링은 `Web Application`으로 만들려고 시도한다.

- 하지만 `Web Servlet Container`에 대한 의존성이 추가되어 있더라도 `properties`를 사용해서 웹 서버가 아닌 일반 서버로 실행 할 수 있다.

  ```java
  spring.main.web-application-type=none
  ```



### Port 설정해보기

- 웹 서버 포트 변경 가능하다.

  ```java
  // 특정 포트 지정
  server.port=7070
  
  // 사용 가능한 포트 중 랜덤 지정
  server.port=0
  ```

- 현재 실행중인 포트 가져올 수 있다.

  ```java
  /**
   * 서블릿 웹 서버가 생성이 되면 이벤트 리스너가 호출된다.
   * Override 한 메서드가 callback 된다
   */
  @Component
  public class PortListener implements ApplicationListener<ServletWebServerInitializedEvent> {
  
      @Override
      public void onApplicationEvent(
          ServletWebServerInitializedEvent servletWebServerInitializedEvent) {
          ServletWebServerApplicationContext applicationContext = servletWebServerInitializedEvent
              .getApplicationContext();
          System.out.println(">>> port : " + applicationContext.getWebServer().getPort());
      }
  }
  ```

  ```java
  // result
  
  2020-08-23 17:37:45.696  INFO 24135 --- [           main] o.e.jetty.server.AbstractConnector       : Started ServerConnector@2c104774{HTTP/1.1, (http/1.1)}{0.0.0.0:49175}
  2020-08-23 17:37:45.698  INFO 24135 --- [           main] o.s.b.web.embedded.jetty.JettyWebServer  : Jetty started on port(s) 49175 (http/1.1) with context path '/'
  >>> port : 49175
  ```

  

## 내장 웹 서버 응용 2부 : HTTPS와 HTTP2

### 개요

- 내장 웹서버에 `https`와 `http2`를 적용하는 방법을 살펴보자.

### HTTPS 설정하기 

- `key-store`를 만들어야 한다.

  ```
  keytool -genkey 
    -alias tomcat 
    -storetype PKCS12 
    -keyalg RSA 
    -keysize 2048 
    -keystore keystore.p12 
    -validity 4000
  ```

- `properties` 적용한다.

  ```java
  server.ssl.key-store: keystore.p12
  server.ssl.key-store-password: 123456
  server.ssl.keyStoreType: PKCS12
  server.ssl.keyAlias: tomca
  ```

- 스프링 부트의 톰캣이 사용하는 커넥터는 하나만 등록이 되는데, 그 커넥터에 `ssl`을 적용해서 앞으로 모든 요청은 `https`를 붙여서 요청하도록 만든다.

- 톰캣이 사용하는 커넥터를 자바 코드로 추가로 만들 수 있고, 포트별로 `http` 또는 `https` 원하는 요청으로 받도록 설정할 수 있다.

  ```
  curl -I -k --http2 https:localhost:8080
  ```

  

### HTTP2 설정하기

- `application.properties`에 아래와 같이 추가하면 되지만, 제약사항이 서버마다 설정 사항이 다르다.

  ```java
  server.http2.enable=true
  ```

  ```java
  1. HTTP/2 with Undertow
   - HTTPS만 적용이 되어있다면 다른 추가 설정을 하지 않아도 된다.
    
  2. HTTP/2 with Tomcat
   - Tomcat 8.5.x version에서는 설정이 복잡하여 사용하지 않는 것을 권장한다.
   - Tomcat 9.0.x version & JDK9 인 경우에는 추가 설정을 하지 않아도 된다.
  ```

  

