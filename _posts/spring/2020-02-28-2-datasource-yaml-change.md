---
title:  "[Spring] 다중 데이터소스 설정(Multiple Datasource JPA, Mybatis)"
excerpt: "다중 데이터소스 설정(Multiple Datasource JPA, Mybatis)"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/spring.png

categories:
  - spring
tags:
  - spring
  - jpa
last_modified_at: 2020-02-28T13:51:00-05:00
---

[다중 데이터 소스](https://growing-up-constantly.tistory.com/48)  설정한 뒤 데이터베이스가 여러 개가 예상이 되어 database.yaml을 분리하였다.

`application.yaml`이 아닌 `database.yaml`의 설정 내용을 어떻게 하면 인식 할 수 있을까? 

간단한 방법으로는 다음과 같다. 

```java
@SpringBootApplication
public class Application {
    public static final String PROPERTIES =
            "spring.config.location="
                    +"classpath:/application.yaml"
                    +",classpath:/database.yaml";


    public static void main(String[] args) {
        new SpringApplicationBuilder(Application.class)
                .properties(PROPERTIES)
                .run(args);
    }
}
```

하지만 위의 작업을 진행한 뒤로 Testcase에서 이상 현상이 발생하였다. 

이를 해결하기 위한 포스트는 다음의 글을 참고하시길 바란다. 

[[Spring\] 다중데이터소스 설정, application.yaml 분리 후 Testcase 오류 발생 및 해결](https://maximsungmo.github.io/spring/datasource-testcase-error-yaml/)



Next works : 

[[Spring\] Controller와 Service 생성하기](https://growing-up-constantly.tistory.com/49?category=333833)
