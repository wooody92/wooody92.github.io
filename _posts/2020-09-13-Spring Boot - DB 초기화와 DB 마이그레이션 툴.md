---
title:  "Spring Boot - DB 초기화와 DB 마이그레이션 툴"
excerpt: "스프링 Data : JPA와 스크립트를 사용한 DB 초기화와 Flyway"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Data
  - DB 초기화
  - Flyway
last_modified_at: 2020-09-13. 19:50:00

---

<br>

# 스프링 부트 활용

## Spring Data 7부 : 데이터베이스 초기화

> 데이터베이스 스키마 초기화와 초기 데이터 입력에 대해 알아보자.

### JPA를 사용한 데이터베이스 초기화

- `properties` 설정하여 스키마를 자동 생성 및 업데이트 할 수 있다.

  ```java
  spring.jpa.hibernate.ddl-auto=create
  spring.jpa.generate-ddl=false
  ```

- `update`, `create`, `create-drop` 셋중에 하나로 설정하면 애플리케이션 실행 시 자동으로 스키마를 생성한다.

  - `create-drop` : 처음에 스키마를 만들고 앱을 종료할때 스키마 지운다. (데이터를 유지하지 않는다.)
  - `create` : 처음에 스키마를 만들기 전에 초기화하고 새로 만든다. (데이터를 유지하지 않는다.)
  - `update` : 기존의 스키마는 두고 추가 된 컬럼이나 변경된 항목만 스키마를 변경한다. (데이터 변경 값들이 저장된다. 개발단계에서 주로 사용한다.)

- `spring.jpa.generate-ddl=` 기본 값이 `false`이기 때문에 `create`이나 `create-drop` 설정시에는 `true`로 바꿔주어야 엔티티 정보를 바탕으로 맵핑이되는 스키마를 자동으로 생성해준다.

- 보통 운영하는 애플리케이션의 경우 `spring.jpa.generate-ddl=false`로 설정하고 `spring.jpa.hibernate.ddl-auto=validate`으로 설정한다.

  - `validate` : 현재 엔티티 맵핑이 관계형 DB에 맵핑할 수 있는 상태인지 검증만 한다.
  - `validate` 설정 상태에서 엔티티에 새로운 컬럼을 추가한다면? 맵핑을 할 수 없기 때문에 에러가 발생한다.
  - `ddl`에 변경을 하는 것이 아니기 때문에 운영용에서 안정적이다.

- 개발시에는 보통 `update`로 설정하여 사용하다가 운영시에는 `validate`으로 변경한다.

  - 왜? `update` 설정의 경우 변경사항만 추가하기 때문에 지저분해진다.
  - 예를들어 테이블 컬럼을 변경하면 기존 값을 삭제하지않고 변경된 값만 추가한다.

<br>

### SQL 스크립트를 사용한 데이터베이스 초기화

- `schema.sql` 파일을 `resource` 디렉토리에 생성하여 스크립트로  따로 관리하여 스키마 생성이 가능하다.

  ```java
  drop table if exists account CASCADE
  drop sequence if exists hibernate_sequence
  create sequence hibernate_sequence start with 1 increment by 1
  create table account (id bigint not null, password varchar(255), username varchar(255), primary key (id))
  ```

- 위와 같이 설정하고 위에서 실패했던 `validate` 테스트를 해보면 스크립트에서 테이블을 작성했기 때문에 테스트에 통과한다.

- 스키마 생성 외에도 초기 데이터를 넣고 싶다면 `data.sql`을 사용 할 수 있다.

- 읽는 순서는 `schema.sql` 그리고 `data.sql` 순서이다.

- schema.sql 읽고 그다음에 data.sql 읽는다.

- 데이터베이스 스키마와 데이터를 좀더 체계적으로 관리하고 싶다면 데이터베이스 마이그레이션 툴을 사용할 수 있다.



## Spring Data 8부 : 데이터베이스 마이그레이션 툴

> 스프링 부트가 지원하는 DB 마이그레이션 툴과의 연동에 대해 살펴보자.

### DB 마이그레이션

- 스프링 부트가 지원하는 DB 마이그레이션 툴에는 `Flyway`와 `Liquibase`가 대표적이다.
- DB의 스키마 변경 또는 데이터 변경과 같은 사항을 버전 관리처럼 사용할 수 있도록 지원한다.
- 도커를 사용하는 경우에는 컨테이너를 없애면 데이터도 같이 날아간다. 그래서 컨테이너를 날려도 데이터를 보존할 수 있도록 로컬 컴퓨터와 디렉토리를 맵핑시켜놓는 볼륨이라는 기능이 있다.



### Flyway

- `Flyway` 사용을 위해 의존성을 추가한다.

  ```xml
  <dependency>
      <groupId>org.flywaydb</groupId>
      <artifactId>flyway-core</artifactId>
      <version>6.5.5</version>
  </dependency>
  ```

- `resource` 하위에 `db/migration`의 디렉토리를 생성하고 `V숫자__이름.sql`과 같이 생성한다. 기본적으로 `V`는 대문자 `__`는 두 개여야 한다.

  <img width="1043" alt="flyway" src="https://user-images.githubusercontent.com/58318041/93016455-32bc8200-f5fc-11ea-947e-9a2ffd8f7fde.png">

- 아래 로그에서 확인할 수 있듯 `flyway`가 먼저 실행되고 `V1_init.sql`의 스키마를 생성한다. 그리고 나서 `Hibernate`이 엔티티와 DB 테이블이 맵핑 가능한지 `validate`로 검증한다.

  ```java
  spring.jpa.hibernate.ddl-auto=validate
  spring.jpa.generate-ddl=false
  ```

  ```java
  09-13 17:12:20.272  INFO 5527 --- [  restartedMain] o.f.c.internal.license.VersionPrinter    : Flyway Community Edition 6.5.5 by Redgate
  2020-09-13 17:12:20.278  INFO 5527 --- [  restartedMain] o.f.c.internal.database.DatabaseFactory  : Database: jdbc:h2:mem:f24b1912-f0ec-4edb-8fb1-b7028fe92b64 (H2 1.4)
  2020-09-13 17:12:20.331  INFO 5527 --- [  restartedMain] o.f.core.internal.command.DbValidate     : Successfully validated 1 migration (execution time 00:00.017s)
  2020-09-13 17:12:20.340  INFO 5527 --- [  restartedMain] o.f.c.i.s.JdbcTableSchemaHistory         : Creating Schema History table "PUBLIC"."flyway_schema_history" ...
  2020-09-13 17:12:20.370  INFO 5527 --- [  restartedMain] o.f.core.internal.command.DbMigrate      : Current version of schema "PUBLIC": << Empty Schema >>
  2020-09-13 17:12:20.372  INFO 5527 --- [  restartedMain] o.f.core.internal.command.DbMigrate      : Migrating schema "PUBLIC" to version 1 - init
  2020-09-13 17:12:20.386  INFO 5527 --- [  restartedMain] o.f.core.internal.command.DbMigrate      : Successfully applied 1 migration to schema "PUBLIC" (execution time 00:00.019s)
  ```

- 위와 같이 `flyway`로 스키마를 생성하고 사용하던 중, 엔티티에 새로운 컬럼을 추가했다. 이 경우에는 기존의 생성한  `V1__init.sql`에 새로 추가 해야할까?

- 정답은 절대 아니다. 한번 적용이 된 스크립트는 건드리지않고 새로 스크립트를 만들어서 추가한다.

  <img width="1043" alt="flyway2" src="https://user-images.githubusercontent.com/58318041/93016522-d5750080-f5fc-11ea-91ca-aa83e1a7c3cf.png">

- 그렇다면 엔티티의 컬럼 이름을 변경하는 경우에는 어떻게 해야 할까?

- 새로운 스크립트를 만든다. 새 컬럼을 추가하고 기존 컬럼 데이터를 새 컬럼으로 복사한 뒤 기존 컬럼을 삭제한다.



