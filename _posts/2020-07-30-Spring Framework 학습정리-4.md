---
title:  "Spring Framework 학습정리 - 4"
excerpt: "IoC 컨테이너 4부 : @Component와 컴포넌트 스캔"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - IoC 컨테이너
  - @Component
  - Component Scan
last_modified_at: 2020-07-30 15:50:00

---

# IoC 컨테이너와 빈

## Ioc 컨테이너 4부 : @Component와 컴포넌트 스캔

### 컴포넌트 스캔 주요 기능 (범위와 필터)

- `@ComponentScan`을 갖고있는 `Configuration`부터 스캔을 시작한다.

- `@ComponentScan`이 포함되어 있는 `package`까지만 스캔한다. 그러므로 컴포넌트 스캔 범위를 확인하여야 한다.

  ```java
  // dev.spring.framework.springstudy를 포함한 하위 디렉토리만 스캔한다.
  package dev.spring.framework.springstudy;
  
  @SpringBootApplication
  public class SpringStudyApplication {
  
  	public static void main(String[] args) {
  		SpringApplication.run(SpringStudyApplication.class, args);
  	}
  
  }
  ```

- 컴포넌트 스캔을 한다고 모든 어노테이션을 처리해서 빈을 등록해주는 것은 아니고 `@Filter`를 이용하여 걸러낸다. 



### @Component

- `@Repository`, `@Service`, `@Controller`, `@Configuration`



### 동작원리

- `@ComponentScan`은 스캔할 패키지와 애노테이션에 대한 정보를 갖는다.
- 실제 스캐닝은 `ConfigurationClassPostProcessor`라는 `BeanFactoryPostProcessor`에 의해 처리된다.
- 3부에서 언급된 `@Autowired`의 동작원리 인 `BeanPostProcessor`와 유사하지만 실행되는 시점이 다르다.
- 다른 모든 빈들을(`Autowired` 또는  `Bean`과 같이 우리가 등록해주는 빈) 등록하기 이전에 컴포넌트 스캔을 해서 구현해준다.



### Functional 을 사용한 빈 등록

```java
public static void main(String[] args) {
        new SpringApplicationBuilder()
						.sources(Demospring51Application.class)
						.initializers((ApplicationContextInitializer<GenericApplicationContext>) applicationContext -> {
  					applicationContext.registerBean(MyBean.class);
            })
						.run(args);
}
```

