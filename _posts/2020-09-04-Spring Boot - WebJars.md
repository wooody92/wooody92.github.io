---
title:  "Spring Boot - WebJar와 index 페이지"
excerpt: "스프링 Web MVC : WebJars, index page and Favicon"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Web MVC
  - WebJar
last_modified_at: 2020-09-04. 15:00:00

---

<br>

# 스프링 부트 활용

## Spring Web MVC 5부 : Web JAR

### WebJars

- 클라이언트에서 사용하는 웹 라이브러리를(`jquery`, `bootstrap`, `react.js`, `view.js` 등) JAR 파일안에 패키징 하는 것이다.

- JVM 기반의 웹 애플리케이션에서 클라이언트측 의존성을 손쉽게 관리할 수 있다.

- 이를 이용하면 템플릿을 사용해서 동적으로 컨텐츠를 생성할 때 혹은 정적 리소스에서도 `WebJar`에 있는 `CSS`나 `JS`를 참조 할 수 있다. 쉽게말해 `jquery`의존성 추가로 `jquery`를 사용 할 수 있다.

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Title</title>
  </head>
  <body>
  Hello Static Resource haha hoho
  
  <script src="/webjars/jquery/3.4.1/jquery.min.js"></script>
  <script>
    $(function () {
      alert("hello jquery")
    })
  </script>
  </body>
  </html>
  ```

<br>



## Spring Web MVC 5부 : index 페이지와 파비콘

### Welcome Page

- 기본 리소스 위치에서 `index.html` 또는 `index.template`을 찾아보고 있으면 제공한다.
- 없다면 404 화이트 라벨 에러 페이지를 띄운다. 이 페이지는 스프링 부트가 갖고있는 기본 에러 핸들러에서 만들어주는 뷰이다.

### 파비콘

- 웹 페이지 옆 작은 이모지
- `favicon.ico`에서 만들 수 있고, 기본 리소스 위치에 놓으면 된다.

### 기본 리소스 위치

- `classpath:/static`
- `classpath:/public`
- `classpath:/resources/`
- `classpath:/META-INF/resources`