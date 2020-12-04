---
title:  "gRPC와 Protocol Buffers"
excerpt: "gRPC와 ProtoBuf 이해"
header:

categories:
  - gRPC
tags:
  - gRPC
  - ProtoBuf
last_modified_at: 2020-12-04. 17:00:00

---

<br>

# gRPC와 ProtoBuf

> gRPC와 ProtoBuf 개념에 대해 학습하고 간단한 실습을 통해 이해한다.

### 공식문서

- https://grpc.io/docs/what-is-grpc/core-concepts/
- https://grpc.io/docs/languages/java/quickstart/



## # gRPC

### 핵심정리

- IDL

  - 표준 인터페이스 명세(클라이언트, 서버 모두 필요)

  - `*.ptoto`의 모음

  - `*.proto` 파일은 build 하면 `protoc` 컴파일러에 의해 지정한 경로에 자동으로 각 언어에 맞는 코드가 생성된다.

    ```
    idl/gen/java/com/github/banksalad/idl/daas/v1/collect/
    ```

    ```protobuf
    syntax = "proto3";
    
    package helloworld;
    
    // The greeting service definition.
    service Greeter {
      // Sends a greeting
      rpc SayHello (HelloRequest) returns (HelloReply) {}
    }
    
    // The request message containing the user's name.
    message HelloRequest {
      string name = 1;
    }
    
    // The response message containing the greetings
    message HelloReply {
      string message = 1;
    }
    ```

    - Greeter - service 타입의 class
    - SayHello - Method, Procedure
    - HelloRequest, HelloReply - message 타입의 class
      - 필드값으로 string 타입의 name, message를 갖는다.
    - rpc - 일종의 Endpoint(@GetMapping)
    - message - 일종의 DTO, VO
      - 직렬화, 역직렬화

- Server

  - IDL에서 정의한 메서드를 구체화한다. 일종의 구현체라고 할 수 있다.

  - IDL에 의해 자동 생성된 추상클래스를 상속받아 메서드를 구현한다.

  - **proto에서 정의한 Request를 매개변수로 받아 Response를 정의한다.** 

  - 우리의 경우는 build 후 자동 생성까지 된 repo를 depenency로 주입받아 사용한다. (.jar)

    ```java
    static class GreeterImpl extends GreeterGrpc.GreeterImplBase {
    
      @Override
      public void sayHello(HelloRequest req, StreamObserver<HelloReply> responseObserver) {
        HelloReply reply = HelloReply.newBuilder().setMessage("Hello " + req.getName()).build();
        responseObserver.onNext(reply);
        responseObserver.onCompleted();
      }
    }
    ```

- Client

  - proto에서 정의한 Request의 필드값을 설정하여(여기서는 `name`) 서버에 메서드 단위로(여기서는 `SayHello`) 요청한다.

  - 서버로부터 받은 Response의 필드값을 사용할 수 있다.

    ```java
    public class HelloWorldClient {
      private static final Logger logger = Logger.getLogger(HelloWorldClient.class.getName());
    
      private final GreeterGrpc.GreeterBlockingStub blockingStub;
    
      /** Construct client for accessing HelloWorld server using the existing channel. */
      public HelloWorldClient(Channel channel) {
        blockingStub = GreeterGrpc.newBlockingStub(channel);
      }
    
      /** Say hello to server. */
      public void greet(String name) {
        HelloRequest request = HelloRequest.newBuilder().setName(name).build();
        HelloReply response = blockingStub.sayHello(request);
        logger.info("Greeting: " + response.getMessage());
      }
    }
    ```

### 정의

- 언어 제약이 없는 오픈소스
- 고성능 RPC(Remote Procedure Calls, 원격 프로시저 호출)
- 인터페이스(Contract, 계약) 기반 RPC 서비스
  - 서버에 인터페이스를 구현 후 클라이언트에 동일한 인터페이스 구현 후 통신

### 왜 쓰는가?

- 성능이 빠르다.
- 네트워크에 전송되는 양이 적다.
  - 프로토콜 버퍼를 사용하여 네트워크 트래픽을 XML, JSON에 비해 적게 소모한다.
- 기존 RPC 프레임워크에 비해 빠르고 사용하기 편하다.

### 특징

- 일반적인 모든 언어를 사용하여 서버, 클라이언트를 구현할 수 있어 언어의 제약이 없다.
  - 다중언어 환경에 대한 광범위한 상호 운용성
- 최신 기술 기반이다.
  - HTTP/2
  - 데이터를 주고 받을 때 프로토콜 버퍼(Protocol Buffers, protobuf)를 사용

### 구조

<img width="822" alt="gRPC" src="https://user-images.githubusercontent.com/58318041/100845544-bb8edf80-34c0-11eb-9a09-a9bf83768c31.png">

### gRPC vs. REST

- REST 서비스는 서버가 어떤 언어로 되어있던 XML 또는 JSON 파일로 데이터를 주고받는다.
- gRPC는 protobuf를 사용해서 binary 파일로 데이터를 전송하는 차이점이 있다. 

### 키워드

- gRPC
- ProtoBuf
  - 구글에서 만든 데이터 전송 구조
- Stub
  - 클라이언트
- IDL



## ProtoBuf (Protocol Buffers)

### 개념

- CSV, XML, JSON과 같은 데이터 구조의 일종
  - 텍스트 형식 - JSON, XML
  - 바이너리 형식 - Protocol Buffers
- IDL(Interface Definition Language)
  - 인터페이스를 한번 정의하여 여러 언어에서 상속해 구현
  - IDL 자체가 Swagger나 API 문서가 된다.

### 특징

- 한번 작성 후 여러 언어에서 재사용한다.
  - 크로스 플랫폼을 지원
- 텍스트 형식이 아니고 바이너리 형식이기 떄문에 가볍고 빠르다.
- ProtoBuf는 내부적으로 protoc라는 컴파일러가 있다.
  - protoc는 `.proto` 파일을 컴파일 해서 원하는 언어(Java, Go, Python 등)의 코드를 자동으로 생성해준다.

### 서비스 모델

- Service
  - 서비스 메서드 정의
- Message
  - 요청과 응답에 대한 구조 정의
- 일반적으로 서비스 하나에 메세지 두개를 갖는다.
- 클래스 타입으로 요청과 응답을 주고받기 때문에 strong typed 하다고 말한다.

### 프로토콜 버퍼 정의하기

- 하나의 `*.proto` 텍스트 파일에 정의한다.
- 정의된 코드는 어렵지 않게 이해 가능하다.
  - 1개의 서비스 클래스 정의
  - 2개의 메세지 클래스 정의
- 컴파일러(protoc)에 의해 원하는 언어 코드를 자동으로 생성한다.

### 데이터 형식

- string, bool, bytes, int32, int64, float, double, uint32, uint64, sint32, sint64
- datetime은 별도의 데이터 형식을 지원하지 않고 string으로 사용하면 된다.

### 의문점

- 직렬화에 대한 구체적인 이해
  - 별도로 학습하여 정리
- 텍스트 형식이던 바이너리 형식이던 결국엔 0101로 넘어갈텐데 가볍다는게 어떤 의미인지?
  - 압축하여 전송
- 서버에서 IDL 인터페이스 구성하고 구현체까지 작성?
  - IDL 인터페이스를 구성 후 빌드하여 해당 의존성을 추가해서 사용
  - 서버에서는 구현체 작성까지



## 실습

### 참고 링크

- https://grpc.io/docs/languages/java/quickstart/#update-the-grpc-service
- https://lelecoder.com/146?category=793314

### 실습 팁

- `*.proto` 파일의 `service`에 메서드를 정의하고 build하면 해당 언어에 맞게 자동으로 코드가 생성된다.

  ```
  ./examples/build/generated/source/proto/main/grpc/io/grpc/examples/helloworld/GreeterGrpc.java
  ```

- Android dependency 관련 에러는 해당 실습해서는 필요하지 않으니 주석처리했다.



## References

- [RPC에 대한 이해](https://real-dongsoo7.tistory.com/131)
- [gRPC 개념](https://postitforhooney.tistory.com/entry/RPCgRPC-%EC%A0%81%EC%9A%A9%EC%9D%84-%EC%9C%84%ED%95%9C-gRPC-%EB%B6%84%EC%84%9D-gPRC%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
- [gRPC 관련 유튜브 링크](https://www.youtube.com/watch?v=OM8jDkgJwWE)
- [gRPC official reference](https://grpc.io/docs/what-is-grpc/core-concepts/)

