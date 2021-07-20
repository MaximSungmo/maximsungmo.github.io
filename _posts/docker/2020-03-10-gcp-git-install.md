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



## GCP 에서 GIT 확인

![image-20200310221731626](assets/images/2020-03-10-gcp-git-install/image-20200310221731626.png)

```
git 
```

> -bash : git: command not found

```
sudo yum install git
git --version
```

> git version 1.8.3.1

```
docker run --name pgadmin4 --env 'PGADMIN_DEFAULT_EMAIL=sunrise5318@gmail.com' --env 'PGADMIN_DEFAULT_PASSWORD=maxim00!' -it -d --rm -p 5050:80 dpage/pgadmin4
```



## Docker-compose

### Docker-compose 설치

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```



references :  
<https://fishpoint.tistory.com/2152>


