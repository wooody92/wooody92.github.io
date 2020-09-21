---
title:  "Effective Java #6 - 불필요한 인스턴스 생성막기"
excerpt: "Item 6 : 불필요한 객체 생성을 피하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
last_modified_at: 2020-09-22. 00:05:00

---

<br>

# 객체 생성과 파괴 (2장)

## #6 : 불필요한 객체 생성을 피하라

> 불필요한 객체 생성을 피하는 방법들에 대해 알아보자.

### 개요

- 동일한 기능의 객체를 매번 생성하기 보다는 객체 하나를 재사용하는 편이 나을 때가 많다.
- 즉, 같은 값을 가지는 인스턴스가 두 개 이상 만들어 지는 것은 불필요한 객체 생성이다.
- 불변(Immutable) 객체는 언제나 재사용 할 수 있고 빠르고 세련됐다.



### 문자열 객체 생성

- String은 대표적인 불변(Immutable) 클래스로 같은 문자열이라면 같은 주소값(레퍼런스)를 갖는다.

- 아래와 같이 동일한 문자열을 불필요하게 new String으로 새 객체로 만들 필요없이 주소값 참조로 처리한다.

  ```java
  @Test
  public void stringReference() {
    String a = new String("test");
    String b = "test";
    String c = "test";
  
    assertNotSame(a, b);
    assertNotSame(a, c);
    assertSame(b, c);
  }
  ```



### 재수용 빈도가 높고 생성비용이 비싼 객체

- 생성 비용이 비싼 객체인데 반복해서 필요하다면 캐싱 방식으로 재사용할 수 있다.

- 정규표현식을 이용하여 영 대소문자만 갖는 메서드를 만들때, String.matches는 정규표현식으로 문자열 형태를 확인하는 가장 쉬운방법이다.

- 그러나 내부에서 인스턴스의 생성 비용이 높은 Pattern 인스턴스가 한번 사용되고 G.C에 정리되어서 성능이 중요한 경우 반복해서 사용하기에는 적합하지 않다.

  - Pattern은 입력받은 정규표현식에 해당하는 유한 상태 머신(finite state machine)을 만들기 때문에 인스턴스 생성 비용이 높다.

  ```java
  // 권장하지 않음
  static boolean isAlphabet(String s) {
    return s.matches("[a-zA-Z]*$");
  }
  ```

- 이를 개선하기 위해 Pattern 인스턴스 클래스를 클래스 초기화 과정에서 직접 생성하여 캐싱해 두고 재사용 할 수 있다.

  ```java
  // 권장
  public class Alphabet {
  
      private static final Pattern ALPHABET = Pattern.compile("[a-zA-Z]*$");
  
      static boolean isAlphabet(String s) {
          return ALPHABET.matcher(s).matches();
      }
  }
  ```

  

### 같은 인스턴스를 대변하는 여러개의 인스턴스를 생성하지 않도록 - 어댑터

- 객체가 불변인 경우에는 재사용해도 안전함이 명백하다. 하지만 몇몇 경우에는 분명하지 않고, 오히려 반대로 보이는 경우가 있다.

- 어댑터의 경우가 그렇다. 어댑터는 인터페이스를 통해서 뒤에 있는 객체로 연결해주는 객체이므로 여러개 만들 필요가 없이 뒷 객체 하나당 하나의 어댑터만 만들면 충분하다.

- 아래 예시에서는 Set 인터페이스와 Map 인터페이스를 연결하는 KetSet 메서드를 어댑터라고 한다.

- keySet 메서드로 새로운 객체를 생성하는 것 같지만, 모두 동일한 Map의 인스턴스를 대변할 뿐이다. 아래 예시와 같이 생성한 set 객체를 변경하면 기존 map 객체도 변경된다.

- Array에서 깊은 복사, 얕은 복사와 비슷한 개념이다.

  ```java
  public class Item6 {
  
      public static void main(String[] args) {
          Map<String, String> user = new HashMap<>();
          user.put("henry", "hello");
          user.put("wooody92", "hi");
  
          Set<String> name1 = user.keySet();
          Set<String> name2 = user.keySet();
          name2.remove("wooody92");
  
          System.out.println(name1);
          System.out.println(name2);
          System.out.println(user);
      }
  }
  ```

  ```java
  // result
  [henry]
  [henry]
  {henry=hello}
  ```

  

### 프리머티브 타입 사용으로 불필요한 오토박싱을 피하자

- 오토박싱은 개발자가 primitive 타입과 박스 타입을 섞어 쓸 수 있게 해주고 박싱과 언박싱을 자동으로 해준다.

- 그러나 오토박싱이 primitive 타입과 그에 대응하는 박싱된 기본 타입의 구분을 흐려주지만, 완전히 없애주는 것은 아니다.

- 불필요한 오토박싱을 피하려면 박스 타입보다는 primitive 타입을 사용해야 한다.

- 아래 예시의 경우 Long 타입의 sum이 long 타입의 sum에 비해 10배가량 더 많은 시간이 소요됨을 볼 수 있다. 

  ```java
  public class Item6 {
  
      public static void main(String[] args) {
          long start = System.currentTimeMillis();
          long sum = 0l;
          for (int i = 0; i <= Integer.MAX_VALUE / 2; i++) {
              sum += i;
          }
          System.out.println(sum);
          System.out.println(System.currentTimeMillis() - start);
      }
  }
  ```

  ```java
  // result : Long sum = 0l;
  2936
  
  // result : long sum = 0l;
  281
  ```

  

## References

- Effective Java 3/E - Joshua Bloch
- [Java-squid 스터디 #6](https://github.com/java-squid/effective-java/issues/6)
- [https://github.com/keesun/study/blob/master/effective-java/item6.md](https://github.com/keesun/study/blob/master/effective-java/item6.md)
- [https://javabom.tistory.com/30](https://javabom.tistory.com/30)

