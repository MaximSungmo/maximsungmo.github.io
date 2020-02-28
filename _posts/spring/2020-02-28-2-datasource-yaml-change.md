---
title:  "[Spring] application.yaml 설정하기 (다중 설정, custom.yaml 생성)"
excerpt: "Spring application.yaml 설정하기 (다중 설정, custom.yaml 생성)"
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


## Spring JPA 설정 따라하기

1. [[Spring] Spring JPA 시작하기(기본 설정 값, JPA 객체 생성)](https://maximsungmo.github.io/spring/0-jpa-start/)

2. [[Spring] 다중 데이터소스 설정(Multiple Datasource JPA, Mybatis)](https://maximsungmo.github.io/spring/1-datasource-configuration/)

3. [[Spring] application.yaml 설정하기 (다중 설정, custom.yaml 생성)](https://maximsungmo.github.io/spring/2-datasource-yaml-change/)

4. [[Spring] Controller와 Service 생성하기](https://maximsungmo.github.io/spring/3-jpa-controller-service/)

5. [[Spring] 간단한 TestCase 만들기](https://maximsungmo.github.io/spring/4-jpa-test-case/)

6. [[Spring] Querydsl 설정하기](https://maximsungmo.github.io/spring/5-jpa-querydsl/)



[[Spring] 다중 데이터소스 설정(Multiple Datasource JPA, Mybatis)](https://maximsungmo.github.io/spring/1-datasource-configuration/)  설정한 뒤 데이터베이스가 여러 개가 예상이 되어 database.yaml을 분리하였다.

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
