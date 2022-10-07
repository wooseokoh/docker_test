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
