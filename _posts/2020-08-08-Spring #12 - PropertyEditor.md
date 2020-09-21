---
title:  "Spring #12 - PropertyEditor"
excerpt: "데이터 바인딩 추상화 : PropertyEditor"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - Data Binding
  - PropertyEditor
last_modified_at: 2020-08-08 18:00:00

---

# 데이터 바인딩

## 데이터 바인딩 추상화 : PropertyEditor

### 데이터 바인딩

- 데이터 바인딩이란 프로퍼티의 값을 타겟 객체에 설정하는 기능 자체를 말한다. 즉, 사용자가 입력한 값을 애플리케이션 도메인 객체에 동적으로 할당하는 기능이다.
- 왜 바인딩이 필요할까? 사용자가 입력한 값은 대부분 문자열이다. 이를 객체가 갖고있는 프로퍼티 타입에 맞춰서 변환해주는 기능이다. (`int`, `long`, `DateTime` 혹은 도메인 객체 자체 등)

### PropertyEditor

- 스프링 3.0 이전까지 `DataBinder`가 변환 작업시 사용하던 **고전적인 방식의 인터페이스**이다.

- `PropertyEditor`의 구현체들은 `value`가 서로 다른 쓰레드에 공유가 된다. 즉, `stateful`이고 `Thred Safe`하지 않으므로, 여러 쓰레드에 공유해서 쓰면 안된다.

- 즉, 그냥 빈으로 등록해서 쓰는 것은 위험하고 `InitBinder`를 이용하여 컨트롤러에서 사용할 바인더를 등록하여 사용하는 것이 권장된다.

  ```java
  @RestController
  public class EventController {
  
      @InitBinder
      public void init(WebDataBinder webDataBinder) {
          webDataBinder.registerCustomEditor(Event.class, new EventEditor());
      }
  
      @GetMapping("/event/{event}")
      public String getEvent(@PathVariable Event event) {
          System.out.println(event);
          return event.getId().toString();
      }
  }
  ```

  ```java
  public class EventEditor extends PropertyEditorSupport {
  
      @Override
      public String getAsText() {
          Event event = (Event) getValue();
          return event.getId().toString();
      }
  
      @Override
      public void setAsText(String text) throws IllegalArgumentException {
          setValue(new Event(Integer.parseInt(text)));
      }
  }
  ```

  ```java
  @ExtendWith(SpringExtension.class)
  @WebMvcTest
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

