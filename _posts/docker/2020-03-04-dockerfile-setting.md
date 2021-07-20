---
title:  "draft"
excerpt: "draft"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/Alt+Space.jpg

categories:
  - blog
tags:
  - window
  - tip
last_modified_at: 2020-02-21T08:00:00-05:00
---




## Dockerfile 작성하기

### Dockerfile 확인하기

```
docker run --name nodejs -itd node:12
```

docker 이미지를 통해서 컨테이너를 만들겠다. 컨테이너의 이름은 nodejs이며 background에서 실행시킬 것이고 dockerhub의 node 라는 파일의 12번 태그를 이미지로 사용하겠다.



정상적으로 다운로드가 되었다면 다음과 같이 이미지를 확인할 수 있다.

```
docker images
```

![image-20200304235137964](assets/images/2020-03-04-dockerfile-setting/image-20200304235137964.png)



이제 run 명령어로 컨테이너가 제대로 만들어졌는 지 확인한다.

```
docker ps
```

![image-20200304235239868](assets/images/2020-03-04-dockerfile-setting/image-20200304235239868.png)



node라는 이미지로 만들어진 컨테이너는 무엇이며 그 안에는 과연 npm이 있을 지 확인한다.



```
docker exec -it nodejs bash 
```

![image-20200304235431872](assets/images/2020-03-04-dockerfile-setting/image-20200304235431872.png)





## React Container를 예시로



## Docker 명령어

자주 사용하는 명령어 위주로 정리하겠다

```
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
```

| 옵션   | 설명                                                      |
| ------ | --------------------------------------------------------- |
| -d     | Background 모드로 사용                                    |
| -p     | 호스트(내컴퓨터)와 컨테이너(도커로 띄운 것)의 port를 연결 |
| -v     | 호스트와 컨테이너의 저장 공간을 연결                      |
| -e     | 컨테이너 내에서 사용할 환경변수 설정                      |
| --name | 컨테이너 이름 설정                                        |
| --rm   | 프로세스 종료시 컨테이너 자동 제거                        |
| -it    | -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션    |
| --link | 컨테이너 연결 [컨테이너명:별칭]                           |



## Docker image 받기



## Docker Container 만들기

```
docker run -it -d --rm --name test1 node:12
```



## Docker Container 들어가기

```
docker run -it -d --rm --name nodejs -p 127.0.0.1:3000:3000 -v D:\Programming\WebAtoZ\react-test:/home/node -v /home/node/node_modules nodejs:latest
```



## Docker image 저장하기

```
docker build -f Dockerfile.dev -t nodejs .
```



## Docker image push



## Dockerfile 작성하기



## docker-compose-yaml 사용하기

