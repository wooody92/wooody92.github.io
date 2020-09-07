---
title:  "Spring Boot - HATEOAS"
excerpt: "스프링 Web MVC : Spring HATEOAS"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Web MVC
  - HATEOAS
last_modified_at: 2020-09-07. 17:30:00

---

<br>

# 스프링 부트 활용

## Spring Web MVC 10부 : Spring HATEOAS

> 스프링 부트가 지원하는 Spring HATEOAS 설정과 HATEOAS에 대해 알아보자.

### HATEOAS (**H**ypermedia **A**s **T**he **E**ngine **O**f **A** pplication **S**tate)

- REST API를 만들 때 클라이언트에게 리소스에 대한 정보 및 리소스와 연관된 링크 정보를 제공하기 위해 사용한다.

  - 서버 : 현재 리소스와 연관된 링크 정보를 클라이언트에게 제공
  - 클라이언트 :  연관된 링크 정보를 바탕으로 리소스에 접근한다.
  - 연관된 링크 정보 : `HREF(Hypertext REFerences)`, `Relation`

- 스프링 HATEOAS는 `HATEOAS`를 구현하기 위해 편리한 기능들을 제공하는 라이브러리이다.

- `EntityModel`를 이용하여 동적으로 해당 API에 대한 `URL`링크를 만들 수 있다. 진정한 REST API, 고급 API를 만들 수 있는 방법이라고 한다.

  ```java
  @RestController
  public class SampleController {
  
      @GetMapping("/hello/{data}")
      public  EntityModel<Hello> hello(@PathVariable String data) {
          Hello hello = new Hello();
          hello.setPrefix("Hey,");
          hello.setName("Henry");
  
          // EntityModel = 우리가 제공할 리소스 + 링크 정보
          EntityModel<Hello> helloEntityModel = new EntityModel<>(hello);
          helloEntityModel.add(linkTo(methodOn(SampleController.class).hello(data)).withSelfRel());
          return helloEntityModel;
      }
  }
  ```

  ```java
  @RunWith(SpringRunner.class)
  @WebMvcTest(SampleController.class)
  public class SampleControllerTest {
  
      @Autowired
      MockMvc mockMvc;
  
      @Test
      public void hello() throws Exception {
          mockMvc.perform(get("/hello/data"))
              .andDo(print())
              .andExpect(status().isOk())
              .andExpect(jsonPath("$._links.self").exists());
      }
  }
  ```

  ```java
  // result
  // request url : http://localhost:8080/hello/totheworld
  
  {
    "prefix": "Hey,",
    "name": "Henry",
    "_links": {
      "self": {
        "href": "http://localhost:8080/hello/totheworld"
      }
    }
  }
  ```



### 설정

- `spring-boot-starter-hateoas` 의존성을 추가해야 한다. 
- 의존성을 추가하면, 스프링 부트에서 `ObjectMapper`와 `LinkDiscover`을 제공한다.
- `ObjectMapper` : 우리가 제공하는 리소스를 `JSON` 변환할 때 사용하는 인터페이스이다. `spring.jackson.*`에서 지원하고. `spring-boot-starter-web`만 추가해도 다 해준다.

