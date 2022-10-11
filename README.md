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

### 컨테이너 이미지

---

```
$ docker search [옵션] [이미지이름:태그명]  // 이미지 검색
$ docker pull [옵션] [이미지이름:태그명]    // 이미지 다운로드
$ docker images                           // 다운 받은 이미지 목록 출력
$ docker inspect [옵션] [이미지이름:태그명] // 다운 받은 이미지 상세보기
$ docker rmi [옵션] [이미지이름]            // 이미지 삭제
```

### 컨테이너 Life cycle

---

```
$ docker create [옵션] [이미지이름:태그명]  // 컨테이너 생성
$ docker start [옵션] [이미지이름:태그명]   // 컨테이너 실행
$ docker run [옵션] [이미지이름:태그명]     // 컨테이너 생성 / 실행
$ docker ps [옵션] [이미지이름:태그명]      // 실행중인 컨테언 목록 확인
$ docker stop [옵션] [이미지이름]          // 동작중인 컨테이너 중지 삭제
$ docker rm [옵션] [이미지이름]            // 컨테이너 삭제
```

### 컨테이너 리소스

---

기본적으로 컨테이너는 호스트 하드웨어 리소스의 사용 제한을 받지 않는다
Docker commands를 이용해 리소스 제한
 - CPU, MEMORY, DISK I/O
 - docker run --help

### Memory 리소스 제한

```
--memory, -m // 컨테이너가 사용할 최대 메모리 양을 지정
$ docker run -d -m 512m nginx:1.14
--memory-swap // 컨테이너가 사용할 스왑 메모리 영역에 대한 설정, 메모리+스왑, 생략 시 메모리의 2개 설정됨
$ docker run -d -m 200m --memory-swap 300m nginx:1.14 // 200m(메모리)+100m()
--memory-reservation // --memory 값보다 적은 값으로 구성하는 소프트 제한값 설정
$ docker run -d -m 1g --memory-reservation 500m nginx:1.14 // 500m를 보장함
--oom-kill-disable // OOM killer가 프로세스 kill 못하도록 보호
// 리눅스 커널은 피지컬 메모리를 넘어버리면 메모리가 큰 프로세스들을 kill함
$ docker run -d -m 1g --oom-kill-disable nginx:1.14 // oom - out of memory
```

### CPU 리소스 제한

```
--cpus // 컨테이너에 할당할 CPU core수를 지정 
$ docker run -d --cpus=".5" ubuntu:1.4
--cpuset-cpus // 컨테이너가 사용할 수 있는 cpu나 코어를 할당 cpu index는 0부터
$ docker run -d --cpuset-cpus 0-3 ubuntu:1.4 
--cpu-share // 컨테이너가 사용하는 cpu 비중을 1024 값을 기반으로 설정
$ docker run -d --cpus 2048 ubuntu:1.4
```

### Block I/O 리소스 제한

```
--blkio-weight        // Block io의 quota를 설정할 수 있으며 100~1000까지 선택
--blkio-weight-device // default 500
$ docker run -it --rm --blkio-weight 100 ubuntu:1.4 /bin/bash
--device-read-bps     // 특정 디바이스에 대한 읽기와 쓰기 작업의 초당 제한을 
--device-write-bps    // kb,mb,gb 단위로 설정
$ docker run -it --rm --device-write-bps /dev/vda:1mb ubuntu:1.4 /bin/bash
$ docker run -it --rm --device-write-bps /dev/vda:10mb ubuntu:1.4 /bin/bash
--device-read-iops    // 컨테이너의 read/write 속도의 쿼터를 설정한다.
--device-write-iops   // 초당 quota를 제한해서 i/o를 발생시킴. 0이상의 정수로
                      // 표기, 초당 데이터 전송량 = IOPS*블럭크기(단위데이터)
$ docker run -it --rm --device-write-ipos /dev/vda:10 ubuntu:1.4 /bin/bash
$ docker run -it --rm --device-write-ipos /dev/vda:100 ubuntu:1.4 /bin/bash
```

### Docker Tip

```
jdk version 11 이상 사용 권장
- 컨테이너 리소스 할당 불가
- 메모리 리소스 적용 불가

k6
- 부하테스트 가능
```