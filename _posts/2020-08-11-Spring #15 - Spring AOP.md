---
title:  "Spring #15 - Spring AOP"
excerpt: "스프링 AOP : 개념소개"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - Spring AOP
last_modified_at: 2020-08-11 16:00:00

---

# Spring AOP

## 스프링 AOP : 개념소개

### 한줄 요약

- `AOP`란 흩어져있는 여러 관심사(기능)를 `Aspect`로 모듈화하여 하나로 모으는 기법이다. 주요 개념으로는 `Aspect`, `Advice`, `Target`, `Pointcut`, `Join point`가 있으며, 구현체로는 자바에서는 `AspectJ`와 `Spring AOP`가 있다. 적용 방법으로는 `AspectJ`를 이용하여 컴파일, 로드타임에서 적용할 수 있고, `Spring AOP`를 이용하여 런타임 시점에서 적용 할 수 있다.



### AOP (Aspect-Oriented Programming)

- `OOP`를 보완하는 수단으로 흩어진 `Aspect`를 모듈화하여 더욱 `OOP`답게 만드는 프로그래밍 기법이다.

- `Concern` : 여러 클래스 또는 메서드에 걸쳐 나타나는 비슷한 코드, 필드, 메서드, 기능 등을 말한다.

  - `Transaction`, `logging` - 해당 메서드 실행 시간 확인 등

- 흩어져있는 공통적으로 필요한 기능을 `Aspect`로 만들어 재사용 가능하게 모듈화하여 만들어 사용한다. `Aspect` 내에서 그 기능을(`Advice`) 어느곳에(`target`) 적용해야하는지 알려준다.

  ![AOP](https://user-images.githubusercontent.com/58318041/89865647-14bcc580-dbe9-11ea-9b73-8104dd306d95.png)



### AOP 주요 개념

- `Aspect` : 흩어진 관심사(`Crosscutting Concerns`, 기능들)를 하나로 묶어 관리하기 위하여 모듈화 한 것이다.
- `Advice` : `Aspect` 모듈화 내부에 있는 해야할 일들(기능들)을 말한다.
- `Target` : 어떤 클래스에 적용을 할 것인지, 적용이 되는 대상을 말한다.
- `Pointcut` : `target`에서 어디, 어느 시점에 적용해야 하는지를 말한다.
  - `A`라는 클래스에 `B`라는 메서드를 호출할때 이 기능을 적용해라 하는게 포인트 컷이다.
- `Join point` : 조인 포인트를 여러 합류 지점이다. 스펙 또는 개념적인 것에 가깝다.
  - 메서드 실행 시점이라던가 메서드 실행할 때 이 어드바이스를 끼워 넣어어라 할 때 그 끼어드는 지점들을 말한다. 생성자를 호출했을때 필드 접근하기 전 등등 여러 합류점이 있다.



### AOP 구현체

- 자바
  - `AspectJ` : 다양한 `Join point`와 기능을 제공한다.
  - `Spring AOP` : `AspectJ`에 비해 국한적으로 기능을 제공해준다.



### AOP 적용방법

- 컴파일
  - `AspectJ`
  - 컴파일 과정에서 자바 파일을 클래스 파일로 만들 때 `Aspect`를 추가하여 바이트 코드를 만든다.
  - 애초에 컴파일 시 적용하므로 로드 타임과 런타임에 아무런 성능적인 부하가 없지만, 별도의 컴파일을 한번 더 해야하는 단점이 있다.
- 로드 타임
  - `AspectJ`
  - 컴파일은 일반적으로 실행되며, `Target` 클래스 파일을 로딩하는 시점에 `Aspect`를 끼워서 넣는다. (`load time weaving`)
  -  `AspectJ`의 다양한 문법을 사용할 수 있는 장점이 있지만, 클래스를 로딩하는 시점에 약간의 부하가 생길 수 있고 로드 타임 위버를 설정 해줘야 한다는 단점이 있다.
- **런타임**
  - `Spring AOP`
  - `A`라는 클래스 타입의 빈을 만들 때, `A`라는 빈을 감싸고 있는 `A`타입의 프록시 빈을 만든다. `A`의 기능을 호출하기 전에 `Aspect`가 가진 기능을 호출하고 나서 `A`의 기능을 호출한다.
  - 빈을 만드는 과정에서 약간의 비용이 들지만, 문법이 쉽고 아무런 설정을 안해도 된다.

