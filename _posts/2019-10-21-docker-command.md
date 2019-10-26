---
layout: post
title: "Docker 기본 명령어"
subtitle: "docker 기본 명령어를 알아보자"
date: 2019-10-21 21:10:00 +0900
categories: docker
tags: docker
---

## Docker 기본 명령어

docker 명령어 실행시 사용하는 인자
- repository : 이미지 이름
- tag : 이미지의 버전
- container : 컨테이너 이름 혹은 ID
- command : 명령어

---

### pull
로컬에 이미지가 없으면 이미지를 다운로드한다.
```bash
$ docker pull <repository>:<tag>
```
tag 를 생략하면 latest 버전으로 다운로드된다.

---

### images
로컬에 존재하는 이미지 목록을 보여준다.
```bash
$ docker images
```
![docker images](/img/docker/images.png)

---

### run
이미지를 컨테이너로 실행한다.
```bash
$ docker run <repository>:<tag>

-d : background 실행
-p : 호스트 port : 컨테이너 port
-i(interactive) : 컨테이너의 표준 입력 연결  
-t(tty) : 컨테이너를 위한 가상 터미널 할당
    -it 옵션을 이용하면 해당 프로그램을 실행하고 컨테이너에 터미널로 연결한다.  
--name : 컨테이너 이름
--rm : 컨테이너 종료시 삭제  
```
![docker run](/img/docker/run.png)

---

### ps
실행중인 컨테이너 목록을 보여준다.
```bash
$ docker ps

-a : 모든 컨테이너 목록 노출
```

---

### exec
외부에서 실행중인 컨테이너에 명령을 실행한다.
```bash
$ docker exec <container> <command>
```
![docker exec](/img/docker/exec.png)

---

### attach
실행중인 컨테이너에 연결한다.
```bash
$ docker attach <container>
```

---

### stop
실행중인 컨테이너를 중지한다.
```bash
$ docker stop <container>

# 모든 컨테이너 중지
$ docker rm $(docker ps -a -q)
```

---

### start
중지된 컨테이너를 실행한다.
```bash
$ docker start <container>
```
![docker restart](/img/docker/restart.png)

---

### restart
실행중인 컨테이너를 재실행한다.
```bash
$ docker restart <container>
```

---

### rm
생성된 컨테이너를 삭제한다.
```bash
$ docker rm <container>

# 모든 컨테이너 삭제
$ docker rm $(docker ps -a -q) 
```
![docker rm](/img/docker/rm.png)

---

### rmi
로컬에 이미지를 삭제한다.
```bash
$ docker rmi <repository>:<tag>
```
tag 를 생략하면 해당 이미지의 모든 버전이 삭제된다.

---

<br>
##### Reference
- *[Blog](http://pyrasis.com/Docker/Docker-HOWTO)*
