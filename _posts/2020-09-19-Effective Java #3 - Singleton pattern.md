---
title:  "Effective Java #3 - Singleton pattern"
excerpt: "Item 3 : private 생성자나 열거 타입으로 싱글턴임을 보증하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
  - Singleton pattern
  - Serialization
last_modified_at: 2020-09-19. 01:40:00

---

<br>

# 객체 생성과 파괴 (2장)

## #3 : private 생성자나 열거 타입으로 싱글턴임을 보증하라

> 인스턴스를 오직 하나만 갖을 수 있는 클래스인 싱글턴에 대해 알아보자.

### 개요

- 클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기 어렵다.
- 왜? 하나의 자원을 공유하기 때문에 프로덕션 DB에 사용되는 싱글턴 클래스라면 인스턴스가 하나기 때문에 가짜(mock) 인스턴스 구현으로 대체 할 수 없기 때문이다.
- 아래에서 싱글턴 객체를 만드는 방식에 대해 알아보자.



### 방법 #1 - public static final 필드 방식의 싱글턴

- 싱글턴 인스턴스를 public static final 필드로 만들고 생성자의 접근제어자를 private 으로 한다.

- 이 방식의 장점은 해당 클래스가 싱글턴임이 명백히 드러나고 간결하다는 점이다.

  ```java
  public class Singleton1 {
  
      public static final Singleton1 INSTANCE = new Singleton1();
  
      private Singleton1() {}
  }
  ```

  ```java
  @Test
  public void singletonTest1() {
    Singleton1 singleton1 = Singleton1.INSTANCE;
    Singleton1 singleton2 = Singleton1.INSTANCE;
    assertEquals(singleton1, singleton2); // success
  }
  ```

- 예외로 리플렉션 API를 사용해서 새로운 인스턴스를 생성할 수 있다.

  ```java
  @Test
  public void singletonTest1()
    throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
    Singleton1 singleton1 = Singleton1.INSTANCE;
    Singleton1 singleton2 = Singleton1.INSTANCE;
  
    Constructor<Singleton1> constructor = (Constructor<Singleton1>) singleton2.getClass()
      .getDeclaredConstructor();
    constructor.setAccessible(true);
    Singleton1 singleton3 = constructor.newInstance();
  
    assertEquals(singleton2, singleton3); // fail
  }
  ```

  ```java
  // result
  Expected :dev.effective.java.chapter1.item3.Singleton1@2979f78c
  Actual   :dev.effective.java.chapter1.item3.Singleton1@58e64074
  ```

- 해결 방법으로는 싱글턴 생성자에 예외를 생성하여 새로운 인스턴스를 생성자하지 못하도록 막는 방법이 있다.

  ```java
  private Singleton1() {
    if (INSTANCE != null) {
      throw new RuntimeException("THIS IS SINGLETON CLASS");
    }
  }
  ```



### 방법 #2 - static factory 방식의 싱글턴

- `Singleton2.getInstance()`는 항상 같은 객체의 참조를 반환하므로 다른 인스턴스가 만들어지지 않는다.
- 이 방식은 아래 세가지 장점이 있다.
  - 팩토리 메서드만 수정하면 언제든 싱글턴이 아니게 바꿀 수 있다.
  - 정적 팩토리를 제네릭 싱글턴 팩토리로 만들어서 타입에 유연하게 대처할 수 있다.
  - 정적 팩토리 메서드 참조를 공급자(supplier)로 사용할 수 있다.



### 방법 #3 - 열거 타입 방식의 싱글턴

- 대부분의 상황에서 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.
- public 필드 방식과 비슷하지만 더욱 간결하고 추가적인 노력없이 직렬화(`Serialization`) 할 수 있다.
- 복잡한 직렬화 상황이나 리플렉션 공격에서도 또 다른 인스턴스가 생기는 것을 막아준다.



### 직렬화 (Serialization)

- 방법 #1과 방법 #2의 경우 직렬화에 사용한다면 역직렬화 시 같은 타입의 인스턴스가 여러개 생길 수 있다.

- 이러한 문제를 해결하기 위해 모든 인스턴스 필드에 `transient`를추가하고 `readResolve` 메서드를 아래와 같이 구현하면 된다.

- transient란 해당 데이터(객체 등)을 직렬화할 때 제외하고 싶은 경우 선언하는 키워드이다. 패스워드와 같은 보안정보가 직렬화되는 것을 막을 때 사용할 수 있다.

  ```java
  private Object readResolve() {
    return INSTANCE;
  }
  ```

  

## References

- Effective Java 3/E - Joshua Bloch
- [Java-squid 스터디 #3](https://github.com/java-squid/effective-java/issues/3)
- [https://github.com/keesun/study/blob/master/effective-java/item3.md](https://github.com/keesun/study/blob/master/effective-java/item2.md)
- [https://javabom.tistory.com/67](https://javabom.tistory.com/67)

