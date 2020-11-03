---
title:  "Effective Java #16 - 접근자 메서드 사용(getter)"
excerpt: "Item 16 : public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
last_modified_at: 2020-11-02. 17:10:00

---

<br>

# 클래스와 인터페이스 (4장)

## #16 : public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

> 필드값을 private으로 설정하고 getter를 사용하자.



### 핵심 정리

- public 클래스에서는 절대 가변 필드를 직접 노출해서는 안된다. (불변 필드값에 대해서도 직접 노출을 권장하지 않는다.)
- 필드값을 private으로 설정하고 접근자(getter)를 사용하도록 하자.
- 하지만 package-private 클래스 또는 private 중첩 클래스에서는 필드를 노출하는 편이 나을 때도 있다.



## References

- Effective Java 3/E - Joshua Bloch

