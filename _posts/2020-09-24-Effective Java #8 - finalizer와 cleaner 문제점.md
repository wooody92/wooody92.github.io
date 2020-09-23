---
title:  "Effective Java #8 - finalizer와 cleaner 문제점"
excerpt: "Item 8 : finalizer와 cleaner 사용을 피하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
  - finalizer
  - cleaner
  - Garbage Collector
last_modified_at: 2020-09-24. 00:10:00

---

<br>

# 객체 생성과 파괴 (2장)

## #8 : finalizer와 cleaner 사용을 피하라

> finalizer와 cleaner에 대해 알아보자.
>
> finalizer 또는 cleaner는 가비지컬렉터에 의해 객체가 소멸되기 전에 실행되는 메서드로 스레드의 우선순위가 낮아 성능상 많은 문제를 야기 할 수 있다.

### 개요

- 자바에서 객체소멸은 가비지컬렉터가 담당하고, 비메모리자원회수는 try-with-resources, try-finally로 해결한다.
- 따라서 프로그래머에게 객체소멸을 위한 어떤 작업을 요구하지 않지만, 다음의 소멸자를 가지고있기는 하다.
  - Finalizer, Cleaner



### Finalizer

- finalizer는 Object 클래스에 정의된 오버로딩 가능한 메서드이며 G.C에 의해 객체가 파괴되기 직전에 실행되는 특징을 갖고 있다.

  ```java
  public class Object {
  
      protected void finalize() throws Throwable {
      }
  }
  ```

- finalizer는 예측할 수 없고, 상황에 따라 위험하고 일반적으로 불필요하다.

- 오동작, 낮은 성능, 이식성 등이 문제의 원인이 된다.



### Cleaner

- Java 9에서는 fianlizer가 deprecated 됐고 cleaner가 새로 생겼다.
- cleaner는 별도의 쓰레드를 사용해서 finalizer보다는 덜 위험하지만, 여전히 예측불가에 느리고 불필요하다.



### 단점 #1

- 언제 실행 될 지 알 수 없다. 어떤 객체가 더이상 필요 없어진 시점에 그 즉시 finalizer 또는 cleaner가 바로 실행되지 않을 수도 있다. 그 사이에 시간이 얼마나 걸릴지는 아무도 모른다.
- 따라서 타이밍이 중요한 작업을 절대로 finalizer나 cleaner에서 하면 안된다. (e.g - 파일닫기)
- 예를 들어, 파일 리소스를 반납하는 작업을 그 안에서 처리한다면, 실제로 그 파일 리소스 처리가 언제 될지 알 수 없고, 자원 반납이 안되서 더이상 새로운 파일을 열 수 없는 상황이 발생할 수도 있다.

### 단점 #2

- finalizers는 인스턴스 반납을 지연 시킬 수 있다.
- finalizer 스레드는 다른 애플리케이션 스레드보다 우선순위가 낮기 때문에 이에 의존하면 시스템 전체에 문제를 일으킬 수 있다.
  - G.C 실행하기 전에 finalizer를 실행 해야하는데 우선순위가 낮아서 실행되지 않고 쌓이다가 OutOfMemoryException 발생
- cleaner는 자신을 수행할 스레드를 제어할 수는 있지만, 역시나 가비지컬렉터에 의존하기 때문에 여전히 사용하지 않는 편이 좋다.

### 단점 #3

- 상태를 영구적으로 수정하는 작업에서는 절대 finalizer나 cleaner에 의존해서는 안된다.
- 예를 들어 데이터베이스 같은 공유 자원의 영구 락 해제를 finalizer나 cleaner에 맡겨 놓으면 분산 시스템 전체가 서서히 멈출 수 있다.
- `System.gc`나 `System.runFinalization`으로 G.C를 강제적으로 실행해도 finalizer나 cleaner가 실행 될 확률은 높여주나 실행을 보장해주진 않는다. (또는 `Runtime.runFinalizersOnExit` )

### 단점 #4

- finalizer 동작 중 예외가 발생해면 무시되고 처리할 작업이 남아있어도 경고조차 출력하지 않고 종료된다.
- 즉, 객체가 훼손될 수 있고 다른 스레드에서 해당 객체에 접근해 문제가 생길 수 있다.
- cleaner는 스레드를 통제하기 때문에 위의 문제는 발생하지 않는다.

### 단점 #5

- G.C의 효율을 떨어트리기 때문에 심각한 성능문제도 초래한다.
- 아래는 `AutoCloseable` 객체 생성후 자원 반납까지 걸리는 시간예제이다.
  - try-with-resource : 12ns
  - finalizer : 550ns
  - cleaner : 66ns

### 단점 #6

- Finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수 있다.
- 생성자나 직렬화 과정에서 예외가 발생하면, 이 생성되다 만 객체에서 악의적인 하위 클래스의 finalizer가 수행될 수 있게 된다.
- 이 finalizer는 정적 필드에 자신의 레퍼런스를 할당하여 G.C가 수집하지 못하게 막을 수 있다.
- 해결책으로는 finalizer를 final로 선언하여 오버라이딩하지 못하도록 막는다.



### 자원을 반납하는 해결책

- 자원 반납이 필요한 클래스에 `AutoCloseable` 인터페이스를 구현해주고, 클라이언트에서 인스턴스를 다 쓰고 나면 `close` 메서드를 호출하면 된다.

- 일반적으로 예외가 발생해도 제대로 종료되도록 `try-with-resouces`를 사용해야 한다. (Item9에서 사용법을 익히자)

- 추가로 `close` 메소드는 현재 인스턴스의 상태가 이미 종료된 상태인지 확인하고, 이미 반납이 끝난 상태에서 `close`가 다시 호출됐다면 `IllegalStateException`을 던져야 한다.

  ```java
  public class Item8 implements AutoCloseable{
  
      @Override
      public void close() throws Exception {  
      }
  }
  ```

  

### finalizer와 cleaner의 사용처

- 안전망 역할로 자원을 반납하고자 하는 경우
  - 자원의 소유자가 `close` 메서드를 호출하지 않는 것에 대비한 안전망 역할이다.
  - finalizer나 cleaner가 호출되리라는 보장은 없지만, 클라이언트가 하지 않은 자원 회수를 늦게라도 해주는게 안 하는 것보다는 낫다.
- 네이티브 리소스를 정리해야 하는 경우
  - 네이티브 객체는 일반적인 객체가 아니라서 G.C가 그 존재를 모른다.
  - 성능이 중요하지않고 자원 또한 중요한 자원이 아니라면 사용 할 수 있다.
- 두 경우에도 성능저하를 가져올 수 있고 썩 좋은 방법은 아니니 `close`메서드를 사용하도록 하자.



### 핵심정리

- cleaner(Java 8까지는 finalizer)는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자.
- 물론 이런 경우라도 불확실성과 성능 저하에 주의해야 한다.



### 스터디 저장소

- [java-squid / effective-java / issue #8](https://github.com/java-squid/effective-java/issues/8)



## References

- Effective Java 3/E - Joshua Bloch
- [https://github.com/keesun/study/blob/master/effective-java/item8.md](https://github.com/keesun/study/blob/master/effective-java/item8.md)
- [https://javabom.tistory.com/74](https://javabom.tistory.com/74)

