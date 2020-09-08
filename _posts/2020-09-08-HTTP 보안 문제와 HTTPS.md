---
title:  "HTTP 보안 문제와 HTTPS"
excerpt: "SSL 인증서 : 대칭키와 비대칭키 암호화"
header:

categories:
  - Network
tags:
  - Network
  - 네트워크
  - HTTP
  - HTTPS
  - SSL
  - 대칭키와 비대칭키
  - 공개키와 비밀키
last_modified_at: 2020-09-08. 18:30:00

---

<br>

# HTTP와 HTTPS

>  `HTTP`의 보안 취약점을 해결하기 위한 프로토콜이 `HTTPS`이고 `SSL`을 이용해 보안 취약점을 해결한다. `HTTP`의 문제점과 `HTTPS`가 사용하는 `SSL`의 암호화 방식에 대해서도 알아보자.

## HTTP

### 개념

- `HyperText Transfer Protocol`
- 웹 서버와 사용자 컴퓨터에 설치된 웹 브라우저 사이에 문서(`HyperText`)를 전송(`Transfer`)하기 위해 사용되는 통신규약(`Protocol`)이다.
- `HTTP` 서버는 기본 포트인 80번 포트에서 서비스 대기 중이며, 클라이언트(웹 브라우저)가 TCP 80 포트를 사용해 연결하면 서버는 요청에 응답하며 정보를 전송한다.
- `HTTP`는 정보를 텍스트 기반의 평문으로 주고 받기 때문에 네트워크에서 정보를 탈취하거나 변조할 수 있는 보안적 위험이 있다.
- 아래의  `HTTP`의 보안 취약점을 해결하기 위한 프로토콜이 `HTTPS`이다.

### HTTP의 문제점

- 평문 통신이기에 도청이 가능하다. -> 암호화
- 완전성을 증명할 수 없기 떄문에 변조가 가능하다. -> 암호화
- 통신 상대를 확인하지 않기 때문에 위장이 가능하다. -> 인증
  - 의미없는 리퀘스트도 수신하기 때문에 `DoS` 공격을 당할 수 있다. 
  - 통신하고 있는 상대방이 허가된 상대인지 확인할 수 없다.

<br>

## HTTPS

### 개념

- `HyperText Transfer Protocol` + `Secure Socket Layer`

- 기본적인 사항은 `HTTP`와 거의 동일하지만, `HTTP` 메세지에 포함되는 콘텐츠 정보에 보안요소(암호화)가 추가되는 것이 가장 큰 차이점이다.

- 모든 `HTTP` 요청과 응답 데이터는 네트워크로 보내지기 전에 `SSL`계층을 통해 암호화 된다.

- `HTTPS`는 `HTTP` 하부에 `SSL` 또는 `TLS`의 보안 계층이 추가된다.

  ![https layer](https://user-images.githubusercontent.com/58318041/92450515-80994a80-f1f6-11ea-80a7-b0cc26f78ab5.png)

- 그렇다면 `SSL` 계층에서는 정보를 어떻게 암호화 할까? 아래 `SSL 인증서`에서 알아보자.

### HTTPS 보안 작동 방식

- 출처 - [정보통신기술용어해설](http://www.ktword.co.kr/abbr_view.php?m_temp1=3132)

  ![https](https://user-images.githubusercontent.com/58318041/92453709-93158300-f1fa-11ea-912a-8ec23e19c8d2.png)

<br>

## HTTP vs. HTTPS

- `HTTPS`에는 보안(암호화) 기능이 추가된 만큼 `HTTP`에 비해 `CPU`나 메모리 등 리소스를 사용하기 떄문에 처리 속도가 느리다.
  - 요즈음에는 서버와 네트워크 발전으로  속도 차이가 거의 없다.
- `HTTPS`는 사용자 정보가 포함된 민감 정보 또는 결제 관련 정보 등과 같이 정보 보호가 꼭 필요한 네트워크 통신 시 사용할 수 있다.
- `HTTP`는 커머스의 특정 상품 정보나 날씨 정보 등과 같이 정보가 유출되어도 상관없는 경우에 사용 할 수 있다.

<br>

## SSL 인증서

### 개념과 역할

- 클라이언트와 서버간의 통신을 공인된 제 3자(CA) 업체가 보증해주는 전자화된 문서이다.
- 통신 내용이 노출, 변경되는 것을 방지한다.
- 클라이언트가 접속하려는 서버가 신뢰 할 수 있는 서버인지 확인한다.
- SSL 통신에 사용할 공개키를 클라이언트에게 제공한다.

### 대칭키 암호화(비밀키)

- 비밀키는 개인키(private key)와 동일하다.

- 암호화와 복호화를 하나의 비밀키로 사용하는 방식이다.

- 하나의 비밀키로 클라이언트와 서버가 같이 사용하기 때문에 양측 모두 비밀키를 가지고 있어야 한다. 즉, 비밀키를 교환하는 과정이 필요하다.

- 비밀키를 전달하는 과정에서 키가 탈취되면 암호화 내용이 모두 유출될 수 있다는 치명적인 단점이 있다.

- 하지만 비대칭키 알고리즘 방식에 비해 속도가 빠르다는 장점이 있다.

- 대표적인 대칭키 알고리즘으로 `DES`, `SEED`, `ARIA` 등이 있다. 

  ![one key](https://user-images.githubusercontent.com/58318041/92457018-a4f92500-f1fe-11ea-835e-95384ea32b0f.png)



### 비대칭키 암호화(공개키 + 비밀키)

- 공개키(public key)와 비밀키(private key)를 사용한다.

- 공개키로 암호화 한 문서는 비밀키로만 복호화 할 수 있고, 비밀키로 암호화하면 공개키로만 복호화 할 수 있다.

- 공개키는 공개키 저장소에 등록되어 있으며 수신자의 공개키를 얻을 수 있다.

- 비대칭키의 암호화 방식에는 **공개키를 이용한 암호화 방식**과 **비밀키를 이용한 전자서명** 두 가지 방식이 있다.

- 공개키를 이용한 암호화 방식은 비밀키의 소유자만 복호화 할 수 있기 때문에 보안적으로 안전하다.

- 비밀키를 이용한 전자서명 방식은 정보 제공자의 신원을 확인할 수 있는 장점이 있다.

  ```
  // 공개키 암호화를 이용한 공개키 암호화 방식
  1. A는 A-공개키와 A-비밀키를 갖고있다.
  2. B는 B-공개키와 B-비밀키를 갖고있다.
  3. A와 B의 공개키는 공개키 저장소에 등록되어 있고, 비밀키는 각자 가지고 있다.
  4. A-공개키로 암호화된 내용은 A-비밀키로만 복호화 할 수 있다. (B도 동일)
  5. A는 B만 볼 수 있도록 공개키 저장소에서 B의 공개키를 얻고 정보를 B-공개키로 암호화하여 전송한다.
  6. B는 자신의 B-비밀키로 정보를 복호화하여 확인한다.
  7. 중간에 정보가 탈취되어도 B-비밀키로만 복호화 할 수 있기 때문에 탈취자는 아무것도 확인 할 수 없다.
  ```

  ```
  // 비밀키 암호화를 이용한 전자서명 방식
  1. A는 A-공개키와 A-비밀키를 갖고있다.
  2. A는 정보를 A-비밀키로 암호화하여 B에게 전송한다.
  3. B는 공개키 저장소에서 A-공개키를 얻어 복호화하여 A의 신원을 확인한다.
  4. 이를 통해 B는 정보 제공자인 A의 신원을 보장받을 수 있다.
  ```

  ![two key](https://user-images.githubusercontent.com/58318041/92457145-d1ad3c80-f1fe-11ea-8dfb-6dfa6b21bae2.png)

- 비대칭키 알고리즘은 대칭키 알고리즘에 비해 느린 단점이 있어서, 긴 문서의 암호화보다는 대칭키 알고리즘의 키값에 대해 암호화를 사용한다.

- 대표적인 비대칭키 알고리즘으로 `RSA`, `Elgamal`가 있다. 



<br>

## References

- [https://post.naver.com/viewer/postView.nhn?volumeNo=16561296&memberNo=1834](https://post.naver.com/viewer/postView.nhn?volumeNo=16561296&memberNo=1834)
- [https://wayhome25.github.io/cs/2018/03/11/ssl-https/](https://wayhome25.github.io/cs/2018/03/11/ssl-https/)
- [https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Network#http와-https](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Network#http와-https)
- [정보통신기술용어해설](http://www.ktword.co.kr/abbr_view.php?m_temp1=3132)

