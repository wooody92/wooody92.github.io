---
title:  "Effective Java #5 - 의존 객체 주입"
excerpt: "Item 5 : 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
  - 의존 객체 주입
  - Dependency Injection
last_modified_at: 2020-09-20. 12:20:00

---

<br>

# 객체 생성과 파괴 (2장)

## #5 : 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

> 의존 객체 주입 방식에 대해 알아보자.

### 개요

- 스프링에서 객체간 의존성을 연결할 때 `@Authowired`로 의존 객체를 주입하는 것과 같은 방식이다.
- 아래에서 의존 객체 주입을 적절하게 사용하는 방법에 대해 알아보자.



### 문제 #1

- 부적절한 정적 유틸리티 클래스로 구현한 예시이다. 유연하지 않고 테스트하기 어렵다.

- `private static final Lexicon dictionary = new KoreanDicationry();`와 같이 dictionary가 KoeanDitionary 객체로 고정되어 있어서 확장성이 없다.

- SpellChecker라는 클래스가 KoreanDictionary에만 한정된다. EnglishDictionary의 경우는 사용 못한다.

  ```java
  public class SpellChecker {
  
      private static final Lexicon dictionary = new KoreanDictionry();
  
      private SpellChecker() {
          // Noninstantiable
      }
  
      public static boolean isValid(String word) {
          throw new UnsupportedOperationException();
      }
  
      public static List<String> suggestions(String typo) {
          throw new UnsupportedOperationException();
      }
  }
  ```

  ```java
  interface Lexicon {}
  
  class KoreanDicationry implements Lexicon {}
  ```

- 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.



### 문제 #2

- 비슷한 방식으로 부적절한 싱글톤 구현의 예시이다. 유연하지 않고 테스트하기 어렵다.

  ```java
  public class SpellChecker {
  
      private final Lexicon dictionary = new KoreanDictionry();
  
      private SpellChecker() {
      }
  
      public static final SpellChecker INSTANCE = new SpellChecker() {
      };
  
      public boolean isValid(String word) {
          throw new UnsupportedOperationException();
      }
  
      public List<String> suggestions(String typo) {
          throw new UnsupportedOperationException();
      }
  }
  ```



### 해결책

- 의존 객체 주입 방법으로 문제를 해결 할 수 있다. 이 방식은 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식이다.

- 이 경우에는 어느 dictionary여도 상관없이 확장성 있게 구현했다.

- 자원과 의존 객체가 여러 개여도 문제 없이 동작한다.

- 불변을 보장하여 같은 자원을 사용하려는 여러 클라이언트가 문제없이 공유할 수 있다.

  ```java
  public class SpellChecker {
  
      private final Lexicon dictionary;
  
      public SpellChecker(Lexicon dictionary) {
          this.dictionary = Objects.requireNonNull(dictionary);
      }
  
      public boolean isValid(String word) {
          throw new UnsupportedOperationException();
      }
      
      public List<String> suggestions(String typo) {
          throw new UnsupportedOperationException();
      }
  
  }
  
  class Lexicon {}
  ```

- 아래와 같이 생성자에 자원 팩토리를 넘겨 줄 수 있다. 여기서 자원 팩토리란 자원을 계속 찍어내는 객체를 말한다.

  ```java
  Mosaic create(Supplier<? extends Tile> tileFactory) { ... }
  ```

  

### 핵심정리

- 클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다.
- 이 자원들을 클래스가 직접 만들게 해서도 안 된다. 대신 필요한 자원이나 그 자원을 만들어주는 팩토리를 생성자 (또는 정적 팩토리나 빌더에) 넘겨주자.
- 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해준다.



## References

- Effective Java 3/E - Joshua Bloch
- [Java-squid 스터디 #5](https://github.com/java-squid/effective-java/issues/5)
- [https://github.com/keesun/study/blob/master/effective-java/item5.md](https://github.com/keesun/study/blob/master/effective-java/item5.md)
- [https://javabom.tistory.com/73](https://javabom.tistory.com/73)

