---
title:  "Spring Boot - Redis"
excerpt: "스프링 Data : Redis 설정과 연결"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Data
  - Redis
  - RedisTemplate
last_modified_at: 2020-09-14. 19:10:00

---

<br>

# 스프링 부트 활용

## Spring Data 9부 : Redis

> 스프링 부트의 Spring Data Redis에 대해 알아보자.

### Redis 설정하고 연결하기

- 의존성을 추가하면 스프링 부트 자동설정으로 아무런 추가 설정없이 바로 `Redis`를 사용할 수 있다.

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.3.0.RELEASE</version>
  </dependency>
  ```

- 도커로 `Redis`를 실행한다. 로컬의 6379 포트로 컨테너의 6379 포트를 연결한다.

  ```
  docker run -p 6379:6379 --name redis_boot -d redis
  docker exec -i -t redis_boot redis-cli
  ```

- 기본적으로 `Redis`를 사용하는데 두 가지 방법이 있다.

  - `StringRedisTemplate` 또는 `RedisTemplate`
  - `extends CrudRepository`

- `properties` 설정으로 `redis` 설정을 커스터마이징 할 수 있다.

  - `spring.redis.*` 을 설정하면 된다.
  - `spring.redis.prot=6379` 기본 포트값은 6379이다.



### StringRedisTemplate로 Redis에 데이터 입력하기

- `StringRedisTemplate`을 사용하여 데이터를 입력하는 예제와 도커 `Redis`에서 정상적으로 데이터가 들어왔는지 확인한 결과이다.

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Autowired
      StringRedisTemplate redisTemplate;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          ValueOperations<String, String> values = redisTemplate.opsForValue();
          values.set("keesun", "whiteship");
          values.set("springboot", "2.0");
          values.set("hello", "world");
      }
  }
  ```

  ![redis`](https://user-images.githubusercontent.com/58318041/93070709-2bf44480-f6ba-11ea-9242-b8f14b5d07a5.png)



 ### CrudRepository를 상속받아 Redis에 데이터 입력하기

- `CrudRepository`는 스프링 데이터의 아주 기본적이고 최상위쪽에 가까운 `Repository` 인터페이스 중 하나이다.

  ```java
  @RedisHash("accounts")
  @Getter @Setter
  public class Account {
  
      @Id
      private String id;
  
      private String username;
  
      private String email;
  }
  ```

  ```java
  public interface AccountRepository extends CrudRepository<Account, String> {
  }
  ```

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Autowired
      StringRedisTemplate redisTemplate;
  
      @Autowired
      AccountRepository accountRepository;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          ValueOperations<String, String> values = redisTemplate.opsForValue();
          values.set("keesun", "whiteship");
          values.set("springboot", "2.0");
          values.set("hello", "world");
  
          Account account = new Account();
          account.setEmail("henry@gmail.com");
          account.setUsername("henry");
  
          accountRepository.save(account);
  
          Optional<Account> byId = accountRepository.findById(account.getId());
          System.out.println(byId.get().getUsername());
          System.out.println(byId.get().getEmail());
      }
  }
  ```

- `key`를 확인해보면 위 `Account` 엔티티에서 설정한(`@RedisHash("accounts")`) `HashName`과 그 `hash` 엔티티 하나의 키가 나온다.

- `hash`는 `hget {key} {column}`으로 가져오고 원하는 필드값도 설정해야 확인할 수 있다.

  ![redis2](https://user-images.githubusercontent.com/58318041/93072809-e422ec80-f6bc-11ea-9fd3-cbebc7760864.png)



### Redis 주요 커맨드

- https://redis.io/commands
- `keys *` : 전체 키의 값 확인
- `get {key}` : 특정 키의 값 확인
- `hgetall {key}` : 해쉬 키가 갖고있는 전체 값 확인
- `hget {key} {column}` : 해쉬 키의 특정 값 확인