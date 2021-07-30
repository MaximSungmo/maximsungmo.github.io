---
title:  "[TIL] 오버로딩, 오버라이딩"
excerpt: "오버로딩과 오버라이딩 자바코드로 보기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/common/java-logo.png

categories:
  - TIL
tags:
  - mybatis
last_modified_at: 2021-07-30T08:00:00-05:00
---

# 오버로딩(overloading)과 오버라이딩(overriding)

## 오버로딩(overloading)

- 같은 이름의 메소드를 만든다.
- 단, 매개변수의 타입 또는 매개 변수의 갯수가 달라야한다.

```java

public class Overloading {
  public void getUser(User user) {} 
  public void getUser(String username) {}  
  public void getUser(String username, String userId) {}
}
```

- 상속 시 동일한 메서드를 자식 클래스에서 재정의(덮어쓰기)

## 오버라이딩(overriding)

```java
public class Overloading {
  public void getUser(User user) {
    searchUser(user);
  } 
  public void getUser(String username) {}  
  public void getUser(String username, String userId) {}
}

public class Overriding extends Overloading {
  @Override
  public void getUser(User user) {
    findUser(user);
  } 
  public void getUser(String username) {}  
  public void getUser(String username, String userId) {}
}

public class App {
  public static void main(String[] args) {
    Overriding ovr = new Overriding(); 
    ovr.getUser(new User('aa')); // Overriding 클래스에서 재정의된 findUser 함수를 사용하는 메소드 호출 
    Overloading ovl = new Overloading();
    ovl.getUser(new User('aa')); // Overloading 클래스에서 정의된 searchUser 함수를 사용하는 메소드 호출 
  }
}
```
