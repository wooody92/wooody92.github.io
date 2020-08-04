---
title:  "Spring Framework 학습정리 - 9"
excerpt: "Ioc 컨테이너 9부 : ResourceLoader"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - IoC 컨테이너
  - ResourceLoader
last_modified_at: 2020-08-04 17:20:00

---

# IoC 컨테이너와 빈

## Ioc 컨테이너 9부 : ResourceLoader

### ResourceLoader

- 리소스를 읽어오는 기능을 제공하는 인터페이스이다.

- `resources` 디렉토리 안에 `test.txt` 파일을 추가하고 해당 내용을 읽어오는 예제.

  ```java
  @Component
  public class AppRunner implements ApplicationRunner {
  
      @Autowired
      ResourceLoader resourceLoader;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          Resource resource = resourceLoader.getResource("classpath:test.txt");
          System.out.println(resource.exists());
          System.out.println(resource.getDescription());
  
          // 해당 경로의 파일 내용 읽어오기. Java11 부터 정상 동작한다.
          System.out.println(Files.readString(Path.of(resource.getURI())));
      }
  }
  ```

  