---
title:  "Docker - docker compose"
excerpt: "도커 컴포즈 docker-compose"
header:

categories:
  - Docker
tags:
  - Docker
  - docekr compose
last_modified_at: 2021-07-20. 18:30:00

---

<br>

# Docker

## 도커 컴포즈 (docker compose)

> 도커 컴포즈의 기본 명령어와 문법을 이해한다.

### 개요

- 도커 컴포즈의 기본 명령어와 문법에 대해 이해한다.



### 도커 컴포즈 설치

```
docker-compose version
```

- Mac의 경우 Docker for Mac으로 설치 시 docker compose도 기본적으로 설치된다. (리눅스의 경우 별도로 설치가 필요)



### 실습 예제

```yaml
version: '2'
services:
  db:
    image: mysql:5.7
    volumes:
      - ./mysql:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: wordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
 	wordpress:
    image: wordpress:latest
    volumes:
      - ./wp:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_PASSWORD: wordpress
```

- docker-compose.yml
- 도커 실행 명령어를 yml 파일로 스크립트 문서화 하여 관리한다.
  - 도커 실행 명령어로 작성 시 발생하는 휴먼에러(띄어쓰기 등)를 줄이고 명확하게 파악 할 수 있다.



## docker-compose 기본 명령어

### 1.1 컨테이너 실행 : up

```
docker-compose up
docker-compose up -d
docker-compose up --force-recreate
docker-compose up --build
```

- 현재 디렉토리에 있는 docker-compose 파일을 실행하여, docker-compose 파일에 정의된 컨테이너를 실행한다.
- -d 옵션을 주면 daemon(백그라운드)으로 실행한다.
- --force-recreate 옵션으로 컨테이너를 새로 만들 수 있다.
- --build 옵션으로 도커 이미지를 다시 빌드한다. (build로 선언했을 때만)



### 2.1 컨테이너 종료 : down

```
docker-compose down
```

- docker-compose 파일에 정의된 컨테이너를 종료하고 삭제한다.



### 3.1 컨테이너 목록 : ps

```
docker-compose ps
```

- docker-compose로 실행중인 목록을 출력한다.

<img width="1244" alt="dc" src="https://user-images.githubusercontent.com/58318041/126296394-13e31979-25c6-4d06-9a2c-f6a6e1da48ea.png">

- docker 실행 명령어로 동작시킨 컨테이너는 docker-compose 명령어에 나타나지 않는다.



### 4.1 멈춘 컨테이너 시작 : start

```
docker-compose start
docker-compose start {service}
```

- stop으로 멈춘(PC 재부팅 등으로 멈춘) 컨테이너를 재개한다.
- 특정 서비스(예제에서는 wordpress)만 재개할 수 있다.



### 5.1 컨테이너 재시작 : restart

```
docker-compose restart
docker-compose restart {service}
```

- 컨테이너를 재시작한다.



### 6.1 컨테이너 정지 : stop

```
docker-compose stop
docker-compose stop {service}
```

- 실행중인 컨테이너를 정지한다.



### 7.1 컨테이너 로그 : logs

```
docker-compose logs
docker-compose logs -f
```

- 컨테이너의 로그를 확인하고, -f 옵션으로 로그를 follow 할 수 있다.



### 8.1 실행 중인 컨테이너에서 명령어 실행 : exec

```
docker-compose exec {service} {command}
docker-compose exec wordpress(컨테이너 이름) bash
```

- 실행 중인 컨테이너에 명령어를 실행 할 수 있다.



### 9.1 컨테이너 build 부분에 정의된 내용대로 빌드 : build

```
docker-compose build
docker-compose build {service}
```

- 컨테이너 build 부분에 정의된 내용대로 빌드한다.



## docker-compose 기본 문법

### version

```yaml
version: '3'
```

- docker-compose.yml 파일의 명세 버전이다.
- docker-compose.yml 버전에 따라 지원하는 도커 엔진 버전도 다르다.



### services

```yaml
services:
	mysql:
	...
	django:
	...
```

- 실행할 컨테이너의 이름 정의이다.
- 도커 실행 명령어의 docker run --name mysql과 같다고 생각 할 수 있다.

### 

### image

```yaml
services:
	mysql:
		image: mysql:5.7
```

- 컨테이너에 사용할 이미지 이름과 태그를 정의한다.
- 태그를 생략하면 최신 버전(latest)을 가져오고, 로컬 환경에 이미지가 없으면 자동으로 pull 한다.

### 

### ports

```yaml
services:
	mysql:
	...
		ports: 
			- "3306:3306"	## "{host-port}:{container-port}"
```

- 호스트와 컨테이너의 포트 연결을 정의한다. port-fowarding

### 

### environment

```yaml
services:
	mysql:
	...
		environment: 
			- MYSQL_ROOT_PASSWORD: 1234
```

- 컨테이너에서 사용할 환경변수를 정의할 수 있다.
- {환경변수 이름} : {value}

### 

### volumes

```yaml
services:
	mysql:
	...
		volumes:
      - ./mysql:/var/lib/mysql	## {host-directory}:{container-directory}
```

- 호스트 디렉토리와 컨테이너의 디렉토리를 마운트 할 수 있다.
- 예제에서는 현재 디렉토리 내 mysql 디렉토리로 마운트했다.

### 

### restart

```yaml
services:
	mysql:
		restart: always ## "no", always, on-failure, unless-stopped
```

- 컨테이너 재시작 정책으로 "no", always, on-failure, unless-stopped 설정이 있다.



### build

```yaml
services:
	mysql:
		build: 
			context: .
			dockerfile: ./compose/mysql/Dockerfile-dev
```

- 이미지를 자체 빌드 후 사용할 수 있다.
- image 속성 대신 사용하며, 여기에 사용할 별도의 도커 파일이 필요하다.



### Reference

- [subicura - 초보를 위한 도커 안내서](https://www.inflearn.com/course/%EB%8F%84%EC%BB%A4-%EC%9E%85%EB%AC%B8/dashboard)
- [https://github.com/wooody92/hello-docker/tree/main/docker](https://github.com/wooody92/hello-docker/tree/main/docker)

