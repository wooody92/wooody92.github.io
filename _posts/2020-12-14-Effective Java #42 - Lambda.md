---
title:  "Effective Java #42 - Lambda"
excerpt: "Item 42 : 익명 클래스보다는 람다를 사용하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
last_modified_at: 2020-12-14. 22:00:00

---

<br>

# 람다와 스트림 (7장)

## #42 : 익명 클래스보다는 람다를 사용하라

> 함수형 프로그래밍의 지평을 열은 람다에 대해 알아보자. 익명 클래스보다 람다를 활용하고, 익명 클래스는 함수형 인터페이스가 아닌 타입의 인스턴스를 만들 떄만 사용한다.

### 요약

- **익명 클래스**를 **람다**로 변환할 경우 매개변수와 반환값의 경우 컴파일러가 문맥을 통해 타입을 추론한다.
  - 컴파일러가 타입 추론 시 대부분의 정보를 제네릭에서 얻는다. 그렇기에 제네릭의 사용이 중요하다.
- 타입을 명시해야 코드가 더 명확해지는 경우를 제외하고, 람다의 모든 매개변수 타입을 생략하자.
- 람다를 사용하면 코드가 간결하고 가독성이 높아진다. 그러나 람다는 이름이 없고 문서화도 할 수 없다.
- 따라서 코드 자체로 동작이 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 쓰지 말아야 한다.
  - 람다식은 적어도 세 줄 안에 끝내는 것이 좋다.
- 람다는 함수형 인터페이스에서만 쓰인다.
- 추상 클래스의 인스턴스를 만들 때 람다를 쓸 수 없으므로 익명 클래스를 사용해야 한다.
- 비슷하게 추상 메서드가 여러개인 인터페이스의 인스턴스를 만들 때도 익명 클래스를 쓸 수 있다.
- 또한, 람다는 자신을 참조할 수 없다. 람다에서의 this 키워드는 바깥 인스턴스를 가리킨다.
  - 익명 클래스에서의 this는 자기 자신을 가리킨다. 자신을 참조해야한다면 익명 클래스를 사용한다.
- 람다와 익명 클래스의 인스턴스를 직렬화하는 일은 극히 삼가야 한다.
  - 람다나 익명 클래스의 경우 구현별로(가상머신 별로) 직렬화 형태가 다르기 때문이다.
  - 직렬화해야만 하는 함수 객체의 경우(Comparator) private 정적 중첩 클래스(아이템 24)의 인스턴스를 사용한다. 



## References

- Effective Java 3/E - Joshua Bloch
- [JavaSquid Issue-42](https://github.com/java-squid/effective-java/issues/42)
