---
title:  "Effective Java #4 - private 생성자"
excerpt: "Item 4 : 인스턴스화를 막으려거든 private 생성자를 사용하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
  - private 생성자
last_modified_at: 2020-09-20. 12:10:00

---

<br>

# 객체 생성과 파괴 (2장)

## #4 : 인스턴스화를 막으려거든 private 생성자를 사용하라

> static 필드와 static 메서드만 갖고있는 유틸 클래스 생성 시 private으로 인스턴스화를 막는 방법에 대해 알아보자.

### 개요

- 정적 멤버만 갖고있는 유틸리티 클래스가 필요한 경우가 있다.
  - `Math`, `Arrays`, `Collections` 등
- 이러한 static 으로 사용하기 위한 유틸리티 클래스는 인스턴스로 만들어 쓰려고 사용하는 것이 아니므로 private 접근 제어자로 인스턴스화를 막아야 한다.



### 문제 #1

- static 멤버들을 갖고있는 유틸리티 클래스를 사용할 때 생성자를 명시하지 않는 경우 컴파일러에 의해 기존 생성자(public)가 만들어진다.

- 생성자가 만들어지면 아래와 같이 원하지 않는 인스턴스를 생성할 수 있다.

  ```java
  public class Utility {
  
      public static final String name = "Henry";
  }
  ```

  ```java
  @Test
  public void instanceTest() {
    Utility utility1 = new Utility();
    Utility utility2 = new Utility();
    assertEquals(false, utility1.equals(utility2));
  }
  ```



### 문제 #2

- 추상 클래스로 만드는 것으로 인스턴스화를 막을 수 없다. 추상 클래스 자체는 인스턴스로 만들수 없다.

- 하지만 추상 클래스를 상속하여 사용한다면 인스턴스를 생성 할 수 있고, 추상 클래스를 상속하여 사용하라는 오해의 소지를 남길 수 있다.

  ```java
  public abstract class AbstractUtility {
  
      public static final String name = "Henry";
  }
  ```

  ```java
  public class ExtendUtility extends AbstractUtility{
  }
  ```

  ```java
  @Test
  public void instanceTest2() {
    AbstractUtility utility1 = new ExtendUtility();
    AbstractUtility utility2 = new ExtendUtility();
    assertEquals(false, utility1.equals(utility2));
  }
  ```



### 해결책

- private 접근 제어자를 갖는 생성자를 명시적으로 작성하여 인스턴스화를 막을 수 있다.

- 명시적으로 생성자가 private이니 클래스 외부에서는 접근이 불가하다.

- 조금 더 나아가 클래스 내부에서도 생성자를 만들어서 인스턴스 생성을 막기위해 예외를 던져 모든 환경에서 인스턴스화 되는 것을 막을 수 있다.

  - `Math`, `Arrays`, `Collections` 등의 클래스들은 `private Math(){}` 정도로만 선언했다.

  ```java
  public class Utility {
  
      // instance 생성 방지
      private Utility() {
          throw new AssertionError();
      }
  
      public static final String name = "Henry";
  }
  ```

- 이 방식은 상속을 불가능하게 하는 효과도 있다.

- 모든 생성자는 하위 클래스에서 상위 클래스의 생성자를 호출하게 되는데, 상위 클래스에서 private으로 선언하면 하위 클래스에서 접근할 수 있는 방법이 사라진다.



## References

- Effective Java 3/E - Joshua Bloch
- [Java-squid 스터디 #4](https://github.com/java-squid/effective-java/issues/4)
- [https://github.com/keesun/study/blob/master/effective-java/item4.md](https://github.com/keesun/study/blob/master/effective-java/item4.md)
- [https://javabom.tistory.com/71](https://javabom.tistory.com/71)