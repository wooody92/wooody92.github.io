---
title:  "Spring Boot - AutoConfigure"
excerpt: "스프링 부트 원리 : 자동 설정 만들기"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - AutoConfigure
  - ConfigurationProperties
last_modified_at: 2020-08-20. 18:00:00

---

# 스프링 부트 원리

## 자동 설정 만들기 1부 : Starter와 AutoConfigure

### AutoConfigure로 자동설정 만들기

- `Xxx-Spring-Boot-Autoconfigure` 모듈 : 자동 설정

- `Xxx-Spring-Boot-Starter` 모듈 : 필요한 의존성 정의

- 구현 방법

  1. 프로젝트 생성 후 의존성 추가

     ```java
     <dependencies>
        <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-autoconfigure</artifactId> </dependency>
     <dependency>
     <groupId>org.springframework.boot</groupId> <artifactId>spring-boot-autoconfigure-processor</artifactId> <optional>true</optional>
        </dependency>
     </dependencies>
     <dependencyManagement>
        <dependencies>
     <dependency> <groupId>org.springframework.boot</groupId> <artifactId>spring-boot-dependencies</artifactId> <version>2.0.3.RELEASE</version> <type>pom</type>
     <scope>import</scope>
            </dependency>
        </dependencies>
     </dependencyManagement>
     ```

  2. 자동 설정으로 만들고 싶은 빈에 대해 `@Configuration` 파일 작성

     ```java
     @Configuration
     public class MyBeanConfiguration {
     
         @Bean
         public MyBean myBean() {
             MyBean mybean = new MyBean();
             mybean.setName("henry");
             mybean.setAge(29);
             return mybean;
         }
     }
     ```

  3. `src/main/resource/META-INF`에 `spring.factories `파일 만들기

  4. `spring.factories`에 설정 파일 추가

     ```java
     // dev.springboot.study.myConfiguration = 내가 만든 설정 파일
     org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
       dev.springboot.study.myConfiguration
     ```

  5. `mvn install` 혹은 `Maven - Lisfcycle - install` 더블클릭

     - 해당 프로젝트 빌드하여 `.jar` 파일 생성후, 다른 `Maven` 프로젝트에서도 의존성을 가져다 쓸수있도록 로컬 `Maven` 저장소에 설치하는 것을 의미한다.

- 이제 새롭게 생성한 `Maven` 프로젝트에서 위 단계를 통해 만든 `Maven` 프로젝트의 의존성을 주입하면, `MyBean`이라는 빈은 `@EnableAutoConfiguration`에 의해 자동생성되어 별다른 설정없이도 사용 할 수 있다.

- 단, 새로 생성한 프로젝트에서 `MyBean`을 새로 만들어 빈을 등록해도 자동생성으로 만든 `MyBean` 으로 덮어씌워지는 문제가 발생한다.

  - `@ComponentScan` 후 `@EnableAutoConfiguration` 으로 빈을 등록하기 때문이다.



## 자동 설정 만들기 2부 : @ConfigurationProperties

### @ConditionalOnMissingBean (덮어쓰기 방지하기)

- 조건부로 빈을 생성한다. `@ComponentScan`으로 빈이 등록되어 있으면 자동생성으로 빈을 등록하여 덮어쓰지 않는다.

  ```java
  @Configuration
  public class myConfiguration {
  
      @Bean
      @ConditionalOnMissingBean
      public MyBean myBean() {
          MyBean mybean = new MyBean();
          return mybean;
      }
  }
  ```

### application.properties로 자동 생성 빈의 값 설정하기 (빈 재정의 수고 덜기)

- `AutoConfiguration` 설정을 위한 프로젝트

  ```java
  @Getter @Setter
  public class MyBean {
  
      String name;
      int age;
  }
  ```

  ```java
  @ConfigurationProperties("MyBean")
  @Getter @Setter
  public class MyBeanProperties {
  
      String name;
      int age;
  }
  ```

  ```java
  @Configuration
  @EnableConfigurationProperties(MyBeanProperties.class)
  public class MyBeanConfiguration {
  
      @Bean
      @ConditionalOnMissingBean
      public MyBean myBean(MyBeanProperties properties) {
          MyBean mybean = new MyBean();
          mybean.setName(properties.getName());
          mybean.setAge(properties.getAge());
          return mybean;
      }
  }
  ```

- 위에서 설정한 프로젝트의 `dependancy`를 통해 `AutoConfiguration`를 사용하는 프로젝트

  ```java
  // application.properties
  myBean.name = "henry"
  myBean.age = "29"
  ```

