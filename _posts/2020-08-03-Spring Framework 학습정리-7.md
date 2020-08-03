---
title:  "Spring Framework 학습정리 - 7"
excerpt: "Ioc 컨테이너 7부 : MessageSource"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - IoC 컨테이너
  - MessageSource
  - messages.properties
last_modified_at: 2020-08-03 17:20:00

---

# IoC 컨테이너와 빈

## Ioc 컨테이너 7부 : MessageSource

### MessageSource

- `ApplicationContext`가 갖고있는 기능 중 하나이다.

- 국제화(i18n) 기능을 제공, 메세지를 다국화하는 인터페이스이다.

- 스프링 부트에서는 별다른 설정 없이 `messages.properties`를 이용하여 사용 할 수 있다. `default`가 왜 한글로 나오는 걸까?

  ```java
  // file : messages.properties
  greeting=Hello {0}
  
  // file : messages_en_CA.properties
  greeting=hey {0}
  
  // file : messages_ko_KR.properties
  greeting=안녕, {0}
  ```

  ```java
  @Component
  public class AppRunner implements ApplicationRunner {
  
      @Autowired
      MessageSource messageSource;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          System.out.println(messageSource.getClass());
          System.out.println(messageSource.getMessage("greeting", new String[]{"Henry"}, Locale.KOREA));
          System.out.println(messageSource.getMessage("greeting", new String[]{"Henry"}, Locale.CANADA));
          System.out.println(messageSource.getMessage("greeting", new String[]{"Henry"}, Locale.getDefault()));
      }
  }
  ```

  ```java
  // result
  class org.springframework.context.support.ResourceBundleMessageSource
  안녕, Henry
  hey Henry
  안녕, Henry
  ```

- `Reloading` 기능이 있는 메세지 소스 사용하기. 캐시 설정을 하였다.

  ```java
  @Bean
  public MessageSource messageSource() {
    var messageSource = new ReloadableResourceBundleMessageSource();
    messageSource.setBasename("classpath:/messages");
    messageSource.setDefaultEncoding("UTF-8");
    messageSource.setCacheSeconds(3);
    return messageSource;
  }
  ```

  