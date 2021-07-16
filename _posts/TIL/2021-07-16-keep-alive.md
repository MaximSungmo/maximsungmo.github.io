---
title:  "[TIL] HTTP의 connectless 를 보완하기 위한 keep-alive"
excerpt: "HTTP 특징 중 비연결성(connectless)를 보완하기 위한 기술"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/http.png

categories:
  - TIL
tags:
  - http
last_modified_at: 2021-07-15T08:00:00-05:00
---

## 질문 내용

- [www.naver.com](http://www.naver.com) 으로 접속 시 일어나는 과정 모두를 설명하세요.

## 질문 목적

- HTTP 요청, 응답은 어떻게 구성되어 있고 브라우저와 클라이언트-서버 상호 작용을 하는가?
- 웹 요청, 응답에서 HTTP 프로토콜의 특징에 대한 이해가 있는가?
- HTTP 프로토콜에 따른 보완책은 어떠한 방법이 있는가?