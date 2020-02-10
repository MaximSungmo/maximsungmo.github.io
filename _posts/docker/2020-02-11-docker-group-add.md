---
title:  "[Docker] group 에 유저 추가하기"
excerpt: "Docker group 에 유저 추가하기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/bio-photo-keyboard-teaser.jpg

categories:
  - docker
tags:
  - docker  
  - GCP
last_modified_at: 2020-02-11T23:06:00-05:00
---

## Docker user 추가
다음의 명령어를 통해 현재 로그인된 계정으로 Docker group에 유저 추가
```
sudo usermod -a -G docker $USER
```


