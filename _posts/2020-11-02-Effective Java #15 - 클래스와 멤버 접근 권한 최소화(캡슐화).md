---
title:  "Effective Java #15 - 클래스와 멤버 접근 권한 최소화(캡슐화)"
excerpt: "Item 15 : 클래스와 멤버의 접근 권한을 최소화하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
last_modified_at: 2020-11-02. 17:00:00

---

<br>

# 클래스와 인터페이스 (4장)

## #15 : 클래스와 멤버의 접근 권한을 최소화하라

> 잘 설계된 컴포넌트는 클래스 내부 데이터와 내부 구현 정보를 외부 컴포넌트로부터 잘 숨겨 캡슐화한다.



### 잘 설계된 컴포넌트란?

- 캡슐화가 얼마나 잘 되어 있는지
- 노출되는 API와 실제 구현이 얼마나 잘 분리되어 있는지
- 메세지를 주고받는 두 컴포넌트가 서로의 내부 동작을 신경쓰지 않는지



### 캡슐화(정보 은닉)의 장점

- 여러 컴포넌트를 병렬로 개발할 수 있기 때문에(독립적이기에) 개발 속도가 빨라진다.
- 각 컴포넌트를 더 빨리 파악하여 디버깅 할 수 있고, 다른 컴포넌트로 교체 부담도 적기 때문에 시스템 관리 비용을 낮춘다.
- 외부와의 결합도가 적은 독립적인 컴포넌트라면 재사용성이 높다.
- 전체 시스템이 완성되지 않아도 개별 컴포넌트 동작을 검증할 수 있기 떄문에 큰 규모의 시스템 개발의 난이도를 낮춘다.
- 독립되어 있는 컴포넌트만 최적화하면 되기 떄문에 성능 최적화에도 도움을 준다.



### 캡슐화는 어떻게 할까

- 기본 원칙은 모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다.
- 기본적으로 불필요한 public 사용은 지양한다.
- private : 멤버를 선언한 톱레벨 클래스에서만 접근할 수 있다.
- package-private : 멤버가 소속된 패키지 안의 모든 클래스에서 접근할 수 있다.
  - default
- protected : package-private을 포함하고, 하위클래스에서도 접근 가능하다.
- public : 모든 곳에서 접근할 수 있다.



### 주의할 점

- 테스트를 위해서 private 접근제어자를 package-private까지 푸는 것은 괜찮으나 그 이상은 안된다.
- public 클래스에서 멤버(필드값) 접근제어자를 package-private에서 protected로 바꾸면 해당 멤버변수에 접근할 수 있는 대상 범위가 넓어진다. (사실상 공개 API)
- public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다.
- public 가변 필드를 갖는 클래스는 일반적으로 스레드 세이프하지 않다.
- 클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근 제어자 메서드를 제공해서는 안된다.



### 핵심 정리

- public 사용을 최소로 하여 클래스를 설계하자.
- public 클래스는 public static final 필드 외에는 어떤 public 필드도 가져서는 안된다.
- public static final 필드가 참조하는 객체가 불변인지 확인하자.



## References

- [자바봄 아이템 15](https://javabom.tistory.com/16)
- Effective Java 3/E - Joshua Bloch

