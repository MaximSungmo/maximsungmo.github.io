---
title:  "[Jenkins] Google Cloud Platform에 연결하기(1)"
excerpt: "Jenkins로 GCP 연결하기(1)"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/bio-photo-keyboard-teaser.jpg

categories:
  - linux
tags:
  - linux
  - Jenkins
  - GCP
last_modified_at: 2020-02-09T14:06:00-05:00
---


# [Jenkins] Google Cloud Platform에 연결하기



## 1. 방화벽 확인 및 해제 

![image-20200209121245482](../../assets/images/image-20200209121245482.png)

- 방화벽 규칙 설정 클릭 

![image-20200209121553175](../../assets/images/image-20200209121553175.png)

- 방화벽 규칙 만들기 클릭 

![image-20200209121627405](../../assets/images/image-20200209121627405.png)

- 포트 tcp:에 지정해서 만들기 (Jenkins 는 별도의 포트만으로 분리해서 관리 예정)



## 2. Jenkins 접속 

http://{ip주소}:{포트번호} 로 접속하면 다음의 화면이 나타난다.

![image-20200209121937043](../../assets/images/image-20200209121937043.png)

### Project job 생성

- New Item 클릭

![image-20200209122452782](../../assets/images/image-20200209122452782.png)

- Freestyle project 생성 

![image-20200209122430339](../../assets/images/image-20200209122430339.png)

### github 계정 jenkins와 연동 

![image-20200209123801791](../../assets/images/image-20200209123801791.png)

다음으로 Persnal access token 을 받아보기 위하여 Generate new token을 클릭한다.

![image-20200209123931255](../../assets/images/image-20200209123931255.png)



아래와 같이 repo와 admin:repo_hook 을 체크한 뒤 생성한다.

![image-20200209124210124](../../assets/images/image-20200209124210124.png)

이 작업을 통해 secret key를 발급받게 되었다.

이제 Jenkins에서 github 계정과 연결을 해보자.

![image-20200209124637933](../../assets/images/image-20200209124637933.png)

Configure System 을 클릭한 뒤 [Github] 에 대한 내용을 add 시킨다.

![image-20200209124932734](../../assets/images/image-20200209124932734.png)

~~위에서 받았던 secret key를 입력할 수 있도록 하단의 화면처럼 셋팅한다.~~

~~Secret 에는 github에서 받았던 secret Key를, ID 에는 github username을 작성한 뒤 add 하고 test connection 이 정상적으로 되는 지 확인한다.~~

![image-20200209125007917](../../assets/images/image-20200209125007917.png)

이상하게도 위의 작업으로 credentials 이 제대로 생성되지 않았다. 

다음의 작업으로 대체한다.

![image-20200209130545491](../../assets/images/image-20200209130545491.png)

이제 위의 작업으로 인해 Credentials 이 생성되었다. 

![image-20200209130447665](../../assets/images/image-20200209130447665.png)

아직 셋팅이 조금 더 남았으니 아래로 가보자

![image-20200209130633234](../../assets/images/image-20200209130633234.png)

- Build Trggers 에서 셋팅을 다음과 같이 추가해준다. 

![image-20200209130703354](../../assets/images/image-20200209130703354.png)

- Build 발생시의 트리거에 대한 script를 다음과 같이 간단하게 작업한다. 



### github 브랜치 설정, trigger 연동

우선은 테스트를 위한 작업이므로 git에 브랜치 하나를 생성하고 오도록 하자.

![image-20200209122745372](../../assets/images/image-20200209122745372.png)

![image-20200209121245482](../../assets/images/image-20200209121245482.png)


참고자료 : 
<https://kutar37.tistory.com/entry/Jenkins-Github-%EC%97%B0%EB%8F%99-%EC%9E%90%EB%8F%99%EB%B0%B0%ED%8F%AC-3>
