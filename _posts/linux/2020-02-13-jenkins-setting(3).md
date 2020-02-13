---
title:  "[Jenkins] Google Cloud Platform에 연결하기(2)"
excerpt: "Jenkins 명령어로 컨테이너 실행시키기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/1581550818516.png

categories:
  - linux
tags:
  - linux
  - Jenkins
  - GCP
last_modified_at: 2020-02-13T23:06:00-05:00
---



# [Jenkins] Google Cloud Platform에 연결하기(3)

## Gradle 명령어로 Clean Build

![1581550818516](../../assets/1581550818516.png)

spring boot 실행 시킬 때 gradle 로 build 작업을 하게 된다.

현재 위의 코드는 리눅스에서 다음과 같다.

```
./gradlew clean build
```

현재 github에 변화를 감지한 jenkins가 git pull 을 받은 뒤  기존의 build 된 파일을 지우고 새롭게 build 작업을 실행한다는 의미이다.

## 현재 파일 위치 확인, 프로그램 실행 가능 여부 확인

그 다음으로 실제로 내가 jenkins job을 태웠을 때 위치가 어디인 지 확인을 해보자

그리고 현재 파일의 목록과 내가 실행시키고자 하는 프로그램(docker) 의 실행이 가능한 지 확인하자

```
pwd
ls -ll
docker ps
```



## Docker Image Build, Push

새롭게 받은 git repository의 소스코드로 새롭게 docker image를 만들어보자

우리가 만든 dockerfile을 통해  build가 될 수 있도록 작업하며, dockerhub 에 push 되는 작업을 진행한다.

```
docker login -u  -p 
docker build --build-arg JAR_FILE=build/libs/*.jar -t smkim08/springboot-docker-test .
docker tag smkim08/springboot-docker-test webatoz/spring-docker:latest
docker push webatoz/spring-docker:latest
```

우선 도커에 로그인하여 계정을 지정하고 그 뒤 로컬에서 만든 도커이미지를 태그를 달아서 나의 계정으로 푸쉬하는 단계이다.



## Docker pull, Container Delete, Docker run

```
docker pull webatoz/spring-docker:latest
docker stop smkim2208
docker rm -f smkim2208
docker run --name smkim2208 -itd -p 80:80 webatoz/spring-docker 
```

현재 우리의 상황은 동일 서버에서 포트만 달리해서 jenkins를 가져가고 있으므로 docker pull 을 받는 것은 의미가 없을 수 있다.(현재 로컬의 이미지와 docker hub의 이미지는 동일할 것이기 때문)

하지만 일반적으로 실서버와 jenkins서버는 분할되므로 해당 과정을 추가하였다.

이미 실행중인 container 멈추고 지운다. 

이는 포트 중복 및 네이밍이 중복되어 에러가 나기 때문이다.

그다음 일반적인 실행 명령으로 docker run 을 시키면 된다.