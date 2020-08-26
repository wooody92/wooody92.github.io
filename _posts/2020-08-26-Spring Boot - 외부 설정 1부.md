---
title:  "Spring Boot - 외부 설정 1부"
excerpt: "스프링 부트 활용 : 프로퍼티(property) 우선순위"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - 프로퍼티 우선순위
  - property
  - application.properties
last_modified_at: 2020-08-26. 17:20:00

---

# 스프링 부트 활용

## 외부 설정 1부

### 외부 설정 파일

- 애플리케이션에서 사용하는 여러 설정값을 애플리케이션 밖 또는 안에 정의할 수있는 기능이다.

- `application.properties` : 스프링 부트가 앱을 구동할때 자동으로 로딩하는 파일 컨벤션이다.

- `application.properties`에서 정의 한 값을 `@Value`를 통해 가져올 수 있다.

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Value("${henry.fullName}")
      private String name;
  
      @Value("${henry.age}")
      private int age;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          System.out.println("================");
          System.out.println(name);
          System.out.println(age);
          System.out.println("================");
      }
  }
  ```

  ```java
  // application.properties
  // ${random.int} : 랜덤값 설정 (RandomValuePropertySource)
  // 플레이스 홀더
  henry.name = wooody92
  henry.age = ${random.int} 
  henry.fullName = ${henry.name} Yoon
  ```

  ```java
  // result
  
  ================
  wooody92 Yoon
  -645322863
  ================
  ```

- 다양한 방법으로 프로퍼티를 사용 할 수 있기 때문에 우선순위가 존재한다.



### 프로퍼티 우선 순위

1. 유저 홈 디렉토리에 있는 spring-boot-dev-tools.properties

2. **테스트에 있는 @TestPropertySource**

   - 오버라이드 문제가 있기 때문에 `test - application.properties`를 삭제하고 `custom.properties`를 만들어 테스트 할 수 있다.

     ```java
     @RunWith(SpringRunner.class)
     @TestPropertySource(locations = "classpath:/test.properties")
     @SpringBootTest
     class ApplicationTest {
     
         @Autowired
         Environment environment;
     
         @Test
         public void contextLoad() throws Exception {
             assertThat(environment.getProperty("henry.name"))
                 .isEqualTo("henry4");
         }
     }
     ```

     ```java
     // test - resources - application.properties
     henry.name = henry4
     ```

   

3. **@SpringBootTest 애노테이션의 properties 애트리뷰트**

   - `main` 디렉토리 빌드하면 `main - application.properties`를 포함하여 `classpth`에 추가된다.

   - 그 다음으로 동일하게 `test` 디렉토리가 빌드되며 `test - application.properties`가 `classpath`에 추가되며 `main - application.properties` 파일을 오버라이딩한다.

   - 특정 프로퍼티를 `main`에서는 설정하고 `test`에서는 설정하지 않았다면 `application.properties`이 오버라이드 될때 파일 자체를 덮어쓰기 때문에 `test`에서는 찾을 수 없다.

     ```java
     @RunWith(SpringRunner.class)
     @TestPropertySource(properties = "henry.name=henry3")
     @SpringBootTest(properties = "henry.name=henry2")
     class ApplicationTest {
     
         @Autowired
         Environment environment;
     
         @Test
         public void contextLoad() throws Exception {
             assertThat(environment.getProperty("henry.name"))
                 .isEqualTo("henry3");
         }
     }
     ```

     ```java
     // 1순위 (예제 기준) 
     @TestPropertySource(properties = "henry.name=henry3")
     
     // 2순위
     @SpringBootTest(properties = "henry.name=henry2")
     
     // 3순위 : main - resources - application.properties
     henry.name = henry1
     henry.age = ${random.int}
     
     // 4순위 : main - resources - application.properties
     henry.name = wooody92
     henry.age = ${random.int}
     ```

     

4. **커맨드 라인 아규먼트**

   ```
   java -jar target/myapplication-0.0.1-SNAPSHOT.jar --henry.name=wooody92
   ```

5. SPRING_APPLICATION_JSON (환경 변수 또는 시스템 프로티) 에 들어있는

   프로퍼티

6. ServletConfig 파라미터

7. ServletContext 파라미터

8. java:comp/env JNDI 애트리뷰트

9. System.getProperties() 자바 시스템 프로퍼티

10. OS 환경 변수

11. RandomValuePropertySource

12. **JAR 밖에 있는 특정 프로파일용 application properties**

13. **JAR 안에 있는 특정 프로파일용 application properties**

14. **JAR 밖에 있는 application properties**

15. **JAR 안에 있는 application properties**

16. @PropertySource

17. 기본 프로퍼티 (SpringApplication.setDefaultProperties)



### application.properties 우선 순위

- 위치에 따라 높은게 낮은걸 덮어 쓴다.

  ```java
  1. file:./config/
  2. file:./
  3. classpath:/config/
  4. classpath:/
  ```

