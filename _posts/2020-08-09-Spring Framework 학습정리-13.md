---
title:  "Spring Framework 학습정리 - 13"
excerpt: "데이터 바인딩 추상화 : Converter와 Formatter"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - Data Binding
  - Converter
  - Formatter
  - ConversionService
  - WebMvcTest
last_modified_at: 2020-08-09 18:00:00

---

# 데이터 바인딩

## 데이터 바인딩 추상화 : Converter와 Formatter

### 서론

- `Spring MVC`에서 아래와 같이 웹 컨트롤러 단에서 `Stirng`으로 들어오는 값이 어떻게 `Event`로 바인딩 될까?

  ```java
  @RestController
  public class EventController {
  
      @GetMapping("/event/{event}")
      public String getEvent(@PathVariable Event event) {
          System.out.println(event);
          return event.getId().toString();
      }
  }
  ```

- 내부적으로 데이터 바인딩이 있고, `Converter`, `Formatter`, `PropertyEditor`로 변환이 된다.



### Converter

- `S (source)` 타입을 `T (target)` 타입으로 변환할 수 있는 매우 일반적인 변환기이다.

- 이전의 `PropertyEditor`와 다르게  `Stateless`하여 `Thred Safe`하다. 그러므로 빈으로 등록해서 사용해도 괜찮다.

- `Controller`단에서 데이터를 받을 때 `Integer`, `long` 타입과 같이 `Spring`에 기본적으로 등록되어 있는 것들은 그냥 사용하면 된다. 하지만 스프링에 있지 타입 `Convert`의 경우에는 아래와 같이 커스텀으로 만들 수 있다.

  ```java
  public class EventConverter {
  
      @Component
      public static class StringToEventConverter implements Converter<String, Event> {
          @Override
          public Event convert(String source) {
              return new Event(Integer.parseInt(source));
          }
      }
  
      @Component
      public static class EventToStringConverter implements Converter<Event, String> {
          @Override
          public String convert(Event source) {
              return source.getId().toString();
          }
      }
  }
  ```

- `SpringBoot`가 아닌 `Spring MVC`에서는 `ConverterRegistry`에 등록해서 사용한다.



### Formatter

- `PropertyEditor`의 대체제이며, `Object`와 `String`의 변환을 담당한다.

- 문자열을 `Locale`에 따라 다국화하는 기능도 제공한다.

- 웹 애플리케이션에서는 `Converter`보다 `Formatter`사용을 권장한다.

  ```java
  @Component
  public class EventFormatter implements Formatter<Event> {
  
      @Override
      public Event parse(String text, Locale locale) throws ParseException {
          return new Event(Integer.parseInt(text));
      }
  
      @Override
      public String print(Event object, Locale locale) {
          return object.getId().toString();
      }
  }
  ```



### ConversionService

- `SpringBoot`가 `Fomatter`와 `Converter`가 빈으로 등록되어 있다면 따로 `WebConfig` 설정 없이 자동으로 찾아서 등록해준다.

- 웹 애플리케이션인 경우에 `DefaultFormattingConversionSerivce`를 상속하여 만든

  `WebConversionService`를 빈으로 등록해 준다.



### WebMvcTest

- `@WebMvcTest` : 계층형 테스트로 웹과 관련된 빈만 등록을 해주는 테스트이다.

- 주로 컨트롤러만 등록이 되어 사용하는데, `Converter`와 `Formatter`의 경우 빈으로 제대로 등록이 안되면 테스트가 깨질 수 있다.

- 테스트에 필요한 클래스를 아래와 같이 빈으로 명시적으로 등록하여 사용할 수 있다.

  ```java
  @ExtendWith(SpringExtension.class)
  @WebMvcTest({EventFormatter.class, EventController.class,
      EventConverter.StringToEventConverter.class})
  class EventControllerTest {
  
      @Autowired
      MockMvc mockMvc;
  
      @Test
      public void getTest() throws Exception {
          mockMvc.perform(get("/event/1"))
              .andExpect(status().isOk())
              .andExpect(content().string("1"));
      }
  }
  ```

