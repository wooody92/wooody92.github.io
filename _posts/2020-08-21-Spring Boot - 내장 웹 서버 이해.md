---
title:  "Spring Boot - 내장 웹 서버 이해"
excerpt: "스프링 부트 원리 : Tomcat, Servlet and ServletContainer"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - AutoConfigure
  - Tomcat
  - Servlet
  - ServletContainer
last_modified_at: 2020-08-21. 22:00:00

---

# 스프링 부트 원리

## 내장 웹 서버 이해

### 내장 서블릿 컨테이너

- 스프링 부트는 서버가 아니고 내장 서블릿 컨테이너를 쉽게 사용할 수 있게 해주는 툴이다.

- 서버 : `tomcat`, `netty` 등

- 스프링을 사용하지 않고 자바 코드로 톰캣 객체를 만들어서 서버를 만들 수 있다.

  ```java
  // 순서
  1. Tomcat 객체 생성
  2. Port 설정
  3. Tomcat에 Context 추가
  4. Servlet 만들기
  5. Tomcat에 Servlet 추가
  6. Context에 Servlet 맵핑
  7. Tomcat 실행 및 대기
  ```

  ```java
  public class Application {
  
      public static void main(String[] args) throws LifecycleException {
          Tomcat tomcat = new Tomcat();
          tomcat.setPort(8080);
  
          // context 만들기
          Context context = tomcat.addContext("/", "/");
  
          // servlet 만들기
          HttpServlet servlet = new HttpServlet() {
              @Override
              protected void doGet(HttpServletRequest req, HttpServletResponse resp)
                  throws IOException {
                  PrintWriter writer = resp.getWriter();
                  writer.println("<html><head><title>");
                  writer.println("Hey, Tomcat");
                  writer.println("</title></head>");
                  writer.println("<body><h1>Hello Tomcat</h1></body>");
                  writer.println("</html>");
              }
          };
  
          // 만든 servlet을 servletName으로 이름지어 contextPath가 "/"인 tomcat에 등록
          String servletName = "HelloServlet";
          tomcat.addServlet("/", servletName, servlet);
  
          // 특정 url 요청이(/hello) 들어오면 만든 servlet을 보여준다.
          context.addServletMappingDecoded("/hello", servletName);
  
          tomcat.start();
          tomcat.getServer().await();
      }
  }
  ```

- 내장 톰캣(`EmbeddedTomcat`)과 서블릿 컨테이너(`ServletContainer`) 그리고 서블릿(`Servlet`)도 자동 설정(`AutoConfiguration`)의 일부이다.

- 위 과정들을 상세하고 유연하게 해주는 것이 스프링 부트의 자동 설정 기능이다.

  ```java
  // 자동 설정으로 톰캣이 만들어지고, 내장 서블릿 컨테이너가 만들어진다. (서블릿 웹 서버 생성)
  org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration
  
  // DispatcherServlet 만들고 서블릿 컨테이너에 등록한다.
  org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration
  ```

  