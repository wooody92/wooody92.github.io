---
title:  "Effective Java - Static Factory Method"
excerpt: "Item 1 : 생성자 대신 정정 팩토리 메서드를 고려하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
  - Static Factory Method
last_modified_at: 2020-09-16. 15:20:00

---

<br>

# 객체 생성과 파괴 (2장)

## #1 : 생성자 대신 정정 팩토리 메서드를 고려하라

> 정적 팩토리 메서드(Static Factory Method)에 대해 알아보자.

### 개요

- 이 책에서는 아래와 같이 개념을 정의한다.
- API를 사용하는 프로그램 작성자(사람)를 그 API의 사용자라고 한다.
- API를 사용하는 클래스(코드)는 그 API의 클라이언트라고 한다.



### Static Factory Method

- public 생성자를 사용하여 객체를 생성하는 전통적인 방법 외에도 `static factory method`를 이용하여 객체(인스턴스)를 생성 할 수있다.

  ```java
  public static Item1 of(String name, String email) {
    return Item1.builder()
      .name(name)
      .email(email)
      .build();
  }
  ```

- `static factory method`의 장점과 단점에 대해 알아보자.

  

### 장점 #1 - 이름을 가질 수 있다.

- 생성자와 다르게 이름을 지을 수 있다. 이름을 통해 파라미터 또는 반환될 객체의 특성을 쉽게 표현할 수 있다. 이는 더 읽기 좋은 코드를 작성하는데 도움이 된다.
- 생성자의 경우 같은 타입의 파라미터를 받는 생성자를 두 개 만들 수 없는 것과 같이 시그니처에 제약이 있다.
- 정적 팩토리 메서드에는 이러한 제약이 없고 각각의 차이가 잘 드러나도록 이름을 잘 지어주면 된다.



### 장점 #2 - 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

- 불변(immutable) 클래스의 경우 또는 매번 새로운 객체를 만들 필요가 없는 경우는 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있다.
- `Boolean.valueOf(boolean)`이 대표적인 예시이고, 같은 객체가 자주 요청되는 상황이라면 비용을 아끼고 성능을 올릴 수 있다.
- 반복되는 요청에 같은 객체를 반환하는 식으로 정적 팩토리 방식의 클래스는 인스턴스의 라이프사이클을 통제할 수 있다.
- 이를 통해 싱글톤(Singleton) 또는 인스턴스화 불가(noninstantiable)로 만들 수 있다.



### 장점 #3  - 반환 타입의 하위 타입 객체를 반환할 수 있다.

- 반환할 객체의 클래스를 자유롭게 선택할 수 있는 유연성이 있다.

- 이 유연성을 응용하면 구현 클래스를 공개하지 않고도 그 객체를 반환할 수 있어 API를 작게 유지할 수 있다.

- `java.util.Collections`가 대표적인 예시이며, 45개에 달하는 인터페이스의 구현체의 인스턴스를 제공하지만 그 구현체들은 전부 non-public이다.

- 즉 인터페이스 뒤에 감추어져 있고 public으로 제공해야 할 API를 줄이므로 인해 개발자가 API를 사용하기 위해 익혀야 할 개념적 부담까지 줄일 수 있었다.

- 아래와 같이 `TestInterface`에서 하위 타입 즉 인터페이스의 구현체인 `TestImplementsClass`의 인스턴스를 생성하여 사용할 수 있다.

  ```java
  public interface TestInterface {
  
      void print();
  
      static void getLowerType() {
          TestExtendClass testExtendClass = new TestExtendClass();
          testExtendClass.print();
      }
  }
  ```

  ```java
  // package-private으로 해당 구현체를 생성할 수 없다. 상위 타입인 인터페이스에서 생성하여 사용한다.
  class TestImplementsClass implements TestInterface {
  
      @Override
      public void print() {
          System.out.println("print test");
      }
  }
  ```

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          TestInterface.getLowerType();
      }
  }
  ```



### 장점 #4 - 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

- 반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다.
- `EnumSet` 클래스는 생성자 없이 public static 메서드, `allOf()`, `of()` 등을 제공한다.
- 그 내부에서는 enum 타입 원소의 갯수에 따라 `RegularEnumSet` 또는 `JumboEnumSet`으로 상황에 따라 다른 클래스의 객체를 반환한다.
- 개발자는 `EnumSet`이 넘겨주는 인스턴스가 어느 클래스의 인스턴스인지 알 필요가 없고 사용만 하면 된다.



### 장점 #5 - 정적 팩토리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

- 장점 3, 4와 비슷한 개념으로, 이러한 유연함은 `Service provider framework(서비스 프로바이더 프레임워크)`를 만드는 근본이 된다.
- 대표적인 서비스 프로바이더 프레임워크로 `JDBC`가 있다.
- 서비스 프로바이더 프레임워크에서 `provider`는 서비스의 구현체다. 그리고 이 구현체들은 클라이언트에 제공하는 역할을 프레임워크가 통제하여 클라이언트를 구현체로부터 분리해준다.
- 서비스 프로바이더 프레임워크는 3개의 핵심 컴포넌트로 이루어진다.
  - 구현체의 동작을 정의하는 서비스 인터페이스(`service interface`)
  - 프로바이더가 구현체를 등록할 때 사용하는 프로바이더 등록 API(aprovider registration API)
  - 클라이언트가 서비스의 인스턴스를 얻을 때 사용하는 서비스 접근 API(`serviece access API`)
- 클라이언트는 서비스 접근 API를 사용할 때 원하는 구현체의 조건을 명시할 수 있고, 이를 명시하지 않으면 기본 구현체를 반환하거나 지원하는 구현체들을 하나씩 돌아가며 반환한다.
- 부가적으로, 서비스 인터페이스의 인스턴스를 제공하는 `서비스 프로바이더 인터페이스`를 만들 수도 있는데, 그게 없는 경우에는 리플랙션을 사용해서 구현체를 만들어 준다.
- `JDBC`의 경우, `DriverManager.registerDriver()`가 `프로바이더 등록 API`. `DriverManager.getConnection()`이 `서비스 엑세스 API`. 그리고 `Driver`가 `서비스 프로바이더 인터페이스` 역할을 한다.



### 단점 #1 - 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩토리 메서드만 제공하면 하위 클래스를 만들 수 없다.

- 클래스가 `package-private`으로 보호되기 때문에 다른 패키지에서는 해당 클래스를 상속할 수 없다.
- 이러한 제약은 컴포지션을 사용하도록 하고 불변 타입으로 만들려면  이를 지켜야 하기 떄문에 다른 관점에서는 장점이라고도 할 수 있다.



### 단점 #2 - 정적 팩토리 메서드는 개발자가 사용할 때 찾기 어렵다.

- 생성자는 Javadoc 상단에 모아서 보여주지만 static 팩토리 메소드는 API 문서에서 특별히 다뤄주지 않는다.
- 따라서 클래스나 인터페이스 문서 상단에 팩토리 메소드에 대한 문서를 제공하는 것이 좋겠다.



### 핵심정리

- 정적 팩토리 메서드와 public 생성자는 각각의 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋다.
- 그래도 정적 팩토리를 사용하는 것이 유리한 경우가 더 많으므로 무작정 public 생성자를 사용하던 습관을 고치도록 하자.



## References

- Effective Java 3/E - Joshua Bloch
- [https://github.com/keesun/study/blob/master/effective-java/item1.md](https://github.com/keesun/study/blob/master/effective-java/item1.md)