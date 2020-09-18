---
title:  "Effective Java #2 - Builder pattern"
excerpt: "Item 2 : 생성자에 매개변수가 많다면 빌더를 고려하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
  - 점층적 생성자 패턴
  - 자바빈즈 패턴
  - Builder pattern
last_modified_at: 2020-09-18. 02:20:00

---

<br>

# 객체 생성과 파괴 (2장)

## #2 : 생성자에 매개변수가 많다면 빌더를 고려하라

> 선택적 매개변수가 많은 경우 적절한 방식인 빌더(Builder) 패턴에 대해 알아보자.

### 개요

- 생성자와 정적 팩토리 메서드 방식에는 선택적 매개변수가 많을 때 적절하게 대응하기 어렵다는 단점이 있다.
- 여기서 선택적 매개변수는 객체 생성 시 있어도 되고 없어도 되는 필드값을 말한다.
- 이러한 경우 사용하게 되는 점층적 생성자 패턴, 자바빈즈 패턴 그리고 이 둘의 장점을 합친 빌더패턴에 대해 알아보자.



### # 생성자 - 점층적 생성자 패턴

- 점층적 생성자 패턴(telescoping constructor pattern) - 확장하기 어렵다.

- 필수 매개변수를 받는 생성자부터 선택적 매개변수를 하나씩 늘려가며 모든 생성자를 만드는 방식이다. 

  - 선택적 매개변수를 1개 받는 생성자, 2개받는 생성자, .. , 모든 매개변수를 받는 생성자

- 이 방식은 매개 변수가 많아질수록 생성자도 많아져서 클라이언트 코드를 작성하거나 읽기 어렵다.

  ```java
  public class TelescopingConstructor {
  
      private String name;
      private String email;
      private String option1;
      private String option2;
  
      public TelescopingConstructor(String name, String email) {
          this.name = name;
          this.email = email;
      }
  
      public TelescopingConstructor(String name, String email, String option1) {
          this.name = name;
          this.email = email;
          this.option1 = option1;
      }
  
      public TelescopingConstructor(String name, String email, String option1, String option2) {
          this.name = name;
          this.email = email;
          this.option1 = option1;
          this.option2 = option2;
      }
  }
  ```

  

### # Setter - 자바빈즈 패턴

- 자바빈즈 패턴(JavaBeans pattern) - 일관성이 깨지고 불변(`immutable`)으로 만들 수 없다. (`setter`가 갖는 단점들)

- 매개변수가 없는 생성자로 객체 생성 후 `setter`를 이용하여 필드값을 설정하는 방식이다.

- 자바빈즈 패턴에서는 객체 하나를 만들려면 `setter` 메서드를 계속 호출해야 하고, 그 과정에서 객체가 완전히 생성되기 전까지는 일관성(consistency)이 무너진 상태에 놓이게 된다.

- 자바빈즈 패턴에서는 클래스를 불변으로 만들 수 없다.

  ```java
  @Setter
  public class JavaBeans {
  
      private String name;
      private String email;
      private String option1;
      private String option2;
  
      public JavaBeans() {}
  }
  ```

  ```java
  public class Item2 {
  
      public void sample() {
          JavaBeans javaBeans= new JavaBeans();
          javaBeans.setName("henry");
          javaBeans.setEmail("henry@mail.com");
          javaBeans.setOption1("option1");
          javaBeans.setOption2("option2");
      }
  }
  ```



### # 빌더패턴 - 1

- 빌더 패턴(Builder pattern) - 점층적 생성자 패턴의 안정성과 자바빈즈 패턴의 가독성의 장점을 갖는다.

- 빌더의 `setter` 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출 할 수 있다. 이러한 방식을 `method chaining` 또는 `fluent API` 라고 한다.

  ```java
  Item2 item2 = new Builder("henry", "henry@mail.com")
          .option1("option1")
          .option2("option2")
          .build();
  ```

- 아래는 정적 멤버 클래스(static member class)를 이용하여 빌더 패턴을 작성한 예시이다. 보통 롬복으로 사용하는 `@Builder`의 구조가 아래처럼 되어있다.

  ```java
  public class Item2 {
  
      private final String name;
      private final String email;
      private final String option1;
      private final String option2;
  
      public static class Builder {
  
          private final String name;
          private final String email;
          private String option1 = "";
          private String option2 = "";
  
          public Builder(String name, String email) {
              this.name = name;
              this.email = email;
          }
  
          public Builder option1(String option1) {
              this.option1 = option1;
              return this;
          }
  
          public Builder option2(String option2) {
              this.option2 = option2;
              return this;
          }
  
          public Item2 build() {
              return new Item2(this);
          }
      }
  
      private Item2(Builder builder) {
          this.name = builder.name;
          this.email = builder.email;
          this.option1 = builder.option1;
          this.option2 = builder.option2;
      }
  }
  ```



### # 빌더패턴 - 2

- 빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다.
- 추상 클래스(`abstract class`)로 추상 빌더 클래스(`abstract static class Builder<T extends Builder<T>>`)를 만든다.
- 그리고 이를 상속(`extends`)하는 구현체 클래스(`concrete class`)의 구현체 빌더(`@Override` 사용)를 이용하여 계층적 구조로 빌더 패턴을 사용 할 수 있다.
- 빌더 패턴은 상당히 유연하다. 빌더 하나로 여러 객체를 순회하면서 만들 수 있고, 빌더에 넘기는 매개변수에 따라 다른 객체를 만들 수도 있다.
-  빌더 패턴의 단점으로는 매개변수가 적을 경우 오히려 코드가 장황해 질 수 있다. 보통 매개변수가 4개 이상의 경우 사용하는 것을 권장한다.



### 핵심정리

- 생성자나 정적 팩토리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 것이 더 낫다.
- 빌더는 점층적 생성자 패턴보다 클라이언트 코드를 읽고 쓰기가 더욱 간결하고, 자바빈즈 패턴보다 훨씬 안전하다.



## References

- Effective Java 3/E - Joshua Bloch
- [Java-squid 스터디 #2](https://github.com/java-squid/effective-java/issues/2)
- [https://github.com/keesun/study/blob/master/effective-java/item2.md](https://github.com/keesun/study/blob/master/effective-java/item2.md)
- [https://javabom.tistory.com/67](https://javabom.tistory.com/67)