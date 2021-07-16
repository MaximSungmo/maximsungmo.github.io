---
title:  "[TIL] HTTP Status Code 종류에 대해 알아보자"
excerpt: "흔히 쓰이는 HTTP Status code"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/http.png

categories:
  - TIL
tags:
  - http
last_modified_at: 2021-07-16T08:00:00-05:00
---

# 질문

## 질문 내용

---

- HTTP 의 Status code 에 종류를 말하라.

## 질문 목적

---

- HTTP Status code 를 적절하게 사용할 수 있는가?

## 핵심 포인트

---

HTTP Status code 는 HTTP Request 에 대한 Response 내용 중 하나로, 응답이 정상적인 지 혹은 어떠한 문제로 인해 처리가 어려운 지에 대한 상태를 나타낸다.

이번에는 HTTP Status code 의 종류를 크게 나누어보고 자주 사용하는 HTTP Status code에 대해서 알아보자.

### HTTP Status code 큰 범주로 나누기

---

- 1XX : 정보 전달: 요청을 받았고, 작업을 진행 중이라는 의미이다.
- 2XX : 성공: 이 작업을 성공적으로 받았고, 이해했으며, 받아들여졌다는 의미이다.
- 3XX : 리다이렉션: 이 요청을 완료하기 위해서는 리다이렉션이 이루어져야 한다는 의미이다.
- 4XX : 클라이언트 오류: 이 요청은 올바르지 않다는 의미이다.
- 5XX : 서버 오류: 올바른 요청에 대해 서버가 응답할 수 없다는 의미이다.

### 자주 사용하는 HTTP Status code 알아보기

---

1. 1XX : 정보 전달

- 103 Early Hints : 사용해본 적은 없으나 위키에서 나타내길 페이지 로드 속도를 빠르게 하기 위하여 사용될 수 있는 코드라 한다. 솔직히 잘은 모르겠다. 

2. 2XX : 성공

- 200 OK: 성공적으로 처리했을 때 쓰인다. 가장 일반적으로 볼 수 있는 HTTP 상태.
- 201 Created: 요청이 성공적으로 처리되어서 리소스가 만들어졌음을 의미한다. (REST API에서 POST 요청으로 값 생성 시 사용했음)
- 204 No Content: 성공적으로 처리했지만 컨텐츠를 제공하지는 않는다. (REST API에서 DELETE 성공 시 사용했음)

3. 3XX : 리다이렉션 

- 301 Moved Permanently(영구 이동): 영구적으로 컨텐츠가 이동했을 때 사용된다.
- 302 Found: 일시적으로 컨텐츠가 이동했을때 사용된다.
- 307 Temporary Redirect: 302와 동일하게 일시적인 컨텐츠 이동을 나타낼때 사용되나, HTTP 메소드의 변경을 허용하지 않는다.
- 308 Permanent Redirect: 301와 동일하게 영구적인 컨텐츠 이동을 나타낼때 사용되나, HTTP 메소드의 변경을 허용하지 않는다.

4. 4XX : 클라이언트 오류

- 400 Bad Request(잘못된 요청): 요청 자체가 잘못되었을 때 사용하는 코드이다.
- 401 Unauthorized(권한 없음): 인증이 필요한 리소스에 인증 없이 접근할 경우 발생한다. 단순히 권한이 없는 경우 이 응답 코드 대신 아래 403 Forbidden을 사용해야 한다.
- 403 Forbidden(거부됨): 서버가 요청을 거부할 때 발생한다. 혹은 권한이 없을 때(로그인 여부와는 무관하다)에도 발생한다.
- 404 Not Found(찾을 수 없음): 찾는 리소스가 없다는 뜻으로, 가장 흔하게 볼 수 있는 오류 코드이다.

5. 5XX : 서버 오류 

- 500 Internal Server Error(내부 서버 에러): 서버에 오류가 발생해 작업을 수행할 수 없을 때 사용된다. 보통 설정이나 퍼미션 문제. 아니면 HTTP 요청을 통해 호출한 문서가 실제 HTML 문서가 아니라 JSP, PHP, 서블릿 등의 프로그램일 경우 그 프로그램이 동작하다 세미콜론을 빼먹는 등의 각종 에러로 비정상 종료를 하는 경우 이 응답코드를 보낸다.
- **[502 Bad Gateway](https://namu.wiki/w/502%20Bad%20Gateway)**(게이트웨이 불량): [게이트웨이](https://namu.wiki/w/%EA%B2%8C%EC%9D%B4%ED%8A%B8%EC%9B%A8%EC%9D%B4)가 연결된 서버로부터 잘못된 응답을 받았을 때 사용된다.
- **503 Service Temporarily Unavailable**(일시적으로 서비스를 이용할 수 없음): 서비스를 일시적으로 사용할 수 없을 때 사용된다. 주로 웹서버 등이 과부하로 다운되었을 때 볼 수 있다.

# 참고 자료

[https://namu.wiki/w/HTTP/응답 코드]