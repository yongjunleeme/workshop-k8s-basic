# Docker

도커에 대해 기본적인 사용법을 실습합니다.

## Task 1. Docker 기본 실습

### 기본 명령어

| 명령어  |  설명  |
|---|---|
| run | 컨테이너 실행하기 |
| ps | 컨테이너 목록 확인하기 |
| stop | 컨테이너 중지하기 |
| rm | 컨테이너 제거하기 |
| logs | 컨테이너 로그보기 |
| images | 이미지 목록 확인하기 |
| pull | 이미지 다운로드하기 |
| rmi | 이미지 삭제하기 |

### 컨테이너 생성 실습

**ubuntu 컨테이너 생성**

```
docker run --rm -it ubuntu:18.04 /bin/sh
```

- `--rm`: 실행 후 삭제, `-it`: 텍스트 입력 옵션

**간단한 웹 애플리케이션 생성**

```
docker run -d -p 4567:4567 subicura/docker-workshop-app:1
```

- 1 : 이미지 넘버

http://xxxx:4567 접속

**MySQL 생성**

```
docker run -d -p 3306:3306 \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
  --name mysql \
  mysql:5.7
```

- `-e`: 환경변수, `MYSQL_ALLOW_EMPTY_PASSWORD=true` : 패스워드 없이 실행

Database 생성

```
docker exec -it mysql mysql
create database wp CHARACTER SET utf8;
grant all privileges on wp.* to wp@'%' identified by 'wp';
flush privileges;
quit
```

**Wordpress 생성**

```
docker run -d -p 8000:80 \
  -e WORDPRESS_DB_HOST=172.17.0.1 \
  -e WORDPRESS_DB_NAME=wp \
  -e WORDPRESS_DB_USER=wp \
  -e WORDPRESS_DB_PASSWORD=wp \
  wordpress
```

http://xxxx:8000 접속

**컨테이너 목록 확인**

```
docker ps -a
```

**컨테이너 로그**

```
docker logs xxx
```

- xxx : 컨테이너 id

**컨테이너 종료**

```
docker stop xxx xxx xxx
```
 
- 여러 컨테이너 한 번에 정지 가능

**컨테이너 삭제**

```
docker rm xxx
```

**이미지 목록 확인**

```
docker images
```

**네트워크 생성**

```
docker network create app-network
```

**네트워크에 연결된 컨테이너 생성**

```
docker run -d \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
  --name mysql \
  --network=app-network \
  mysql:5.7
```

- 가상 네트워크로 mysql 생성 -> ip 대신 mysql 이름으로 접속 가능

```
docker exec -it mysql mysql
create database wp CHARACTER SET utf8;
grant all privileges on wp.* to wp@'%' identified by 'wp';
flush privileges;
quit
```

```
docker run -d -p 8000:80 \
  --network=app-network \
  -e WORDPRESS_DB_HOST=mysql \
  -e WORDPRESS_DB_NAME=wp \
  -e WORDPRESS_DB_USER=wp \
  -e WORDPRESS_DB_PASSWORD=wp \
  wordpress
```

## Exam 1. 방명록 만들기

**frontend**

이미지
- subicura/guestbook-frontend:latest

환경변수
- PORT # ex) 8000
- GUESTBOOK_API_ADDR # ex) backend:8000

**backend**

이미지
- subicura/guestbook-backend:latest

환경변수
- PORT # ex) 8000
- GUESTBOOK_DB_ADDR # ex) mongodb:27017

**mongodb**

이미지
- mongo:4

기본포트
- 27017

이름을 지정하면 ip를 몰라도 가상네트워크 내에서 컨테이너끼리 통신 가능

```
docker run -d --name=mongodb --network=app-network
```

- `-d`:백그라운드 옵션

```
docker run 0d --name=bakcend --network=app-network -e PORT=8000 -e GUESTBOOK_DB_ADDR=mongodb:27017 subicura/guestbook-backend:latest
```

- `-e`: 환경변수 옵션
- `GUESTBOOK_DB_ADDR`: 데이터베이스 주소

```
docker run -d -p 3000:8000 -e PORT=8000 GUESTBOOK_DB_ADDR=backend:8000 --network==app-network
```

- host와 port를 매핑

## 정리

```
docker stop xxx
docker rm xxx
docker system prune -a # 사용하지 않는 프로세스 정리?
```

```
docker stop f4 99 db b2 87 # id의 앞 2글자만 입력해도 되는가 봄
```
