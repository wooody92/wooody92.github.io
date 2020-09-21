---
title:  "Spring #6 - Environment, Profile, Property"
excerpt: "Ioc 컨테이너 6부 : Environment - 프로파일과 프로퍼티"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - IoC 컨테이너
  - Environment
  - Profile
  - Property
last_modified_at: 2020-08-02 18:50:00

---

# IoC 컨테이너와 빈

## Ioc 컨테이너 6부 : Environment 1부 프로파일

### Environment

- `ApplicationContext` 는 빈 팩토리 기능만 하는 것이 아니고 다른 기능들도 갖고있는데 `EnvironmentCapable`가 그 중 하나이다.
- `EnvironmentCapable`는 `profile`과 `property`의 두가지 기능을 제공하는 인터페이스이다.



### Profile

- 특정 환경에서만 빈이 등록되도록 설정하여 사용하는 기능

  - 프로덕션 환경과 테스트 환경을 나누어 사용 할 수 있다.
  - 테스트 환경에서는 A라는 빈을 사용하고, 배포 환경에서는 B라는 빈을 쓰고 싶은 경우 .
  - 모니터링 용도로 쓰는 빈은 테스트 할 때는 필요가 없고 배포할 때만 등록이 필요한 경우.

- 빈들의 그룹이라고 말할 수 있으며, 따로 설정해주지 않으면 `default`이다.

- 아래와 같이 클래스 혹은 메서드에  `@Profile` 설정하면 `"test"`환경에서만 빈이 등록된다. 환경설정을 하지 않고 해당 빈을 쓰려하면 빈을 찾지못해 에러가 발생한다.

  ```java
  @Configuration
  @Profile("test")
  public class TestConfiguration {
  
      @Bean
      public StudyRepository studyRepository() {
          return new TestStudyRepository();
      }
  }
  ```

  ```java
  @Component
  public class AppRunner implements ApplicationRunner {
  
      @Autowired
      ApplicationContext ctx;
  
      @Autowired
      StudyRepository studyRepository;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          Environment environment = ctx.getEnvironment();
          System.out.println(Arrays.toString(environment.getActiveProfiles()));
          System.out.println(Arrays.toString(environment.getDefaultProfiles()));
      }
  }
  ```

  ```java
  // test profile 등록 전
  Exception in thread "task-2" org.springframework.beans.factory.BeanCreationNotAllowedException: Error creating bean with name 'springApplicationAdminRegistrar': Singleton bean creation not allowed while singletons of this factory are in destruction (Do not request a bean from a BeanFactory in a destroy method implementation!)
  
  
  // test profile 등록 후
  [test]
  [default]
  ```

- 아래 두가지 방법 중 하나를 선택하여 `profile` 환경설정을 할 수 있다.

  <img width="972" alt="스크린샷 2020-08-02 오후 6 19 25" src="https://user-images.githubusercontent.com/58318041/89119948-05ed5900-d4ed-11ea-88b6-fc99d9b52b48.png">



## Ioc 컨테이너 6부 : Environment 2부 프로퍼티

### Property

- 외부 환경변수를 가져 올 수 있는 설정 값이며, `Environment`를 통해 프로퍼티 소스 설정 및 프로퍼티 값을 가져올 수 있다.

- `@PropertySource("classpath:/my.properties")`와 같이 `Environment`를 통해 프로퍼티를 추가 할 수 있다.

- 기본 프로퍼티 소스는 `application.properties`이기 때문에 아래와 같은 방식으로 프로퍼티 값을 가져올 수 있다.

  ```java
  @Component
  public class AppRunner implements ApplicationRunner {
  
      @Autowired
      ApplicationContext ctx;
  
      // 방법 1
      @Value("${app.name}")
      String appName;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          Environment environment = ctx.getEnvironment();
          // 방법 1
          System.out.println(appName);
          // 방법 2
          System.out.println(environment.getProperty("app.name"));
      }
  }
  ```

  