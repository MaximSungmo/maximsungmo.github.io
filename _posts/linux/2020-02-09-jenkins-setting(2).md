---
title:  "[Jenkins] Google Cloud Platform에 연결하기(2)"
excerpt: "Jenkins로 GCP 연결하기(2)"
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
last_modified_at: 2020-02-09T23:06:00-05:00
---



# [Jenkins] Google Cloud Platform에 연결하기(2)



## Slack에 연결하여 Jenkins 알람 받기

![image-20200209235731793](../../assets/images/image-20200209235731793.png)

다음과 같이 Add more apps -> Jenkins CI 검색 -> Add to Slack 을 해준다.

![image-20200209235858100](../../assets/images/image-20200209235858100.png)

이제 Add Jenkins CI integration을 클릭하면 다음의 설명이 나오는데, Step1, Step2 는 그대로 따라한다.

![image-20200210000633148](../../assets/images/image-20200210000633148.png)

Step 3에서는 `Team Subdomain` 과 `Integration Token Credential ID` 을 발급해주는 데 따로 기록해놓도록 한다.

위의 작업 중 Step2의 Slack Notification 을 설치 완료했다면 이제 Jenkins 관리페이지로 돌아와서 `Manage Jenkins` -> `Configure System` 을 클릭한다.

`컨트롤 + F` 를 눌러 Slack 을 찾은 후 



![image-20200210002947949](../../assets/images/image-20200210002947949.png)

![image-20200210003257909](../../assets/images/image-20200210003257909.png)

![image-20200210003407921](../../assets/images/image-20200210003407921.png)



![image-20200210003451689](../../assets/images/image-20200210003451689.png)



출처 : 

https://jojoldu.tistory.com/139