---
title:  "[Cloud] Google Cloud Platform에 연결하기(1)"
excerpt: "Jenkins에 Job 만들기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/jenkins_main_image.jpg

categories:
  - linux
tags:
  - linux
  - jenkins
  - docker
  - GCP
last_modified_at: 2020-02-16T18:06:00-05:00
---



## 1. Jenkins 에 Job만들기

http://{ip주소}:{포트번호} 로 접속하면 다음의 화면이 나타난다.

![image-20200209121937043](../../assets/images/image-20200209121937043.png)

### Project job 생성

- New Item 클릭

![image-20200209122452782](../../assets/images/image-20200209122452782.png)

- Freestyle project 생성

![image-20200209122430339](../../assets/images/image-20200209122430339.png)





#### github 프로젝트 jenkins와 연동 

Github 프로젝트와 Jenkins를 연동하기 위하여 다음의 위치에 .ssh 디렉토리를 만든다.

![image-20200216191033909](../../assets/images/image-20200216191033909.png)

```
mkdir ./ssh
```



이제 ssh key를 만들어보자.

```
ssh-keygen -t rsa -f /home/sunrise5318/.ssh/github-test
```

제대로 생성되었는 지 확인하기 위하여 해당 폴더로 이동한 뒤 다음의 명령어를 입력한다.

```
ls -al
```

![image-20200216191320197](../../assets/images/image-20200216191320197.png)



이제 github 프로젝트로 이동해서 `setting`으로 들어간다. 

`deploy keys` -> `add deploy key` 를 클릭한다. 

![image-20200216191631080](../../assets/images/image-20200216191631080.png)

key 는 생성된 ssh key 중 뒤에 .pub 이 붙은 것을 확인하면 된다.

```
cat github-test.pub
```



완료가 되면 다음과 같은 화면을 볼 수 있다.

![image-20200216191819271](../../assets/images/image-20200216191819271.png)



이제 다시 Jenkins manage page로 접속해서 해당 프로젝트의 credentials 을 등록한다.

![image-20200216192023356](../../assets/images/image-20200216192023356.png)





![image-20200216192128008](../../assets/images/image-20200216192128008.png)

이제 모두 등록이 되었으므로 Project 생성을 이어나가보자.



![image-20200216192842977](../../assets/images/image-20200216192842977.png)

![image-20200216192903644](../../assets/images/image-20200216192903644.png)

![image-20200216192922761](../../assets/images/image-20200216192922761.png)

![image-20200216192939062](../../assets/images/image-20200216192939062.png)







reference : 
<https://kutar37.tistory.com/entry/Jenkins-Github-%EC%97%B0%EB%8F%99-%EC%9E%90%EB%8F%99%EB%B0%B0%ED%8F%AC-3>



https://jojoldu.tistory.com/442