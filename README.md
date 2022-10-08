# Docker 공부

---

컨테이너는 하나의 독립된 Application 프로세스

### Docker 명령어

---

```
$ docker search nginx // docker 허브에서 검색
$ docker pull nginx:latest // 최신 nginx version 이미지을 가져옴
$ docker run -d --name web -p 80:80 nginx:latest // 가져온 nginx 이미지를 컨테이너로 실행

```

### DockerFile

---

text file로 top-down 해석하여 컨테이너를 빌드함

```
#               comment
FROM            컨테이너의 BASE IMAGE(운영환경) // ex. ubuntu:18.03
MAINTAINER      이미지를 생성한 살마의 이름 및 정보
LABEL           container 이미지에 container의 정보를 저장 // key-value ex. maintainer="홍길동"
RUN             container를 위해 base image에서 실행할 commands // && <-- 사용으로 레이어를 줄이고 용량을 줄일수 있음
COPY            container 빌드시 호스트의 파일을 container로 복사
ADD             container 빌드시 호스트이 파일(tar, url포함) container로 복사
WORKDIR         container 빌드시 명령이 실행될 작업 디렉털 ㅣ설정
ENV             환경변수 지정
USER            명령 및 container 실행시 적용할 유저 설정
VOLUME          파일 또는 디렉토를 container의 디렉토리로 마운트
EXPOSE          container 동작시 외부에서 사용할 포트지정
COMD            container 동작시 자동으로 실행할 서비스나 스크립트 지정
ENTRTPOINT      CMD와 함꼐 사용하면서 cmooand 지정 시 사용
```

```
$ mkdir builddir
$ cd builddir
$ vi hello.js
$ vi dockerfile
FROM node:12
COPY app.js /
CMD ["node", "/app.js" ]

```

### 컨테이너 배포

---

hub.docker.com에 배포

```
$ docker build -t hellojs:latest .
$ docker login
$ docker push hellojs:latest
```
