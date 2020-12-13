---
title:  "Linux command line"
excerpt: "유용한 리눅스 명령어 저장소"
header:

categories:
  - Linux
tags:
  - Linux
last_modified_at: 2020-12-13. 16:00:00

---

<br>

# 리눅스

> 자주 까먹는 리눅스 명령어를 기록합니다. 계속 업데이트 예정 입니다.

## # 파일 경로

### 현재 경로

- `pwd`

### 특정 파일의 경로

- `find . -name 'GreeterGrpc.java'`



## # 실행중인 파일

### lsof

- list open files, 현재 실행중인 파일 목록
- `lsof`

### 8080 포트 종료

- `kill $(lsof -t -i:8080)`



## # SCP 활용

### ssh 실행 (리눅스)

- `sudo service ssh start`

### 원격 IP 확인 (리눅스)

- `ip addr`
- 접속이 필요한 서버(리눅스)에서 명령어로 ip 확인

### 원격 접속 (from : 맥 to : 리눅스)

- `ssh id(linux)@ip(linux)` : `ssh henry@192.111.11.11`

### 원격 파일 복사 (from : 리눅스 to : 맥)

- `scp {host name}@{ip addr}:{from-directory} {to-directory}` 
- `scp henry@192.111.11.11:/home/henry/Desktop/my01/img001.JPG ~/Documents/backup`

### 원격 디렉토리 복사 (from : 리눅스 to : 맥)

- `scp -r henry@192.111.11.11:/home/henry/Desktop/Store/ ~/Documents/backup`

### 참고링크

- https://gist.github.com/wooody92/0f9099926b9ad109e9b1a7aaefd2146a
- https://skylit.tistory.com/90
- https://eodevelop.tistory.com/65

