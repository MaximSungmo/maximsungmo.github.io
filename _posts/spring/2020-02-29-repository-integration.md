---
title:  "[Spring] Mybatis와 JpaRepository 인터페이스 하나의 repository로 관리하기"
excerpt: "Spring Mybatis와 JpaRepository 인터페이스 하나의 repository로 관리하기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/spring.png

categories:
  - spring
tags:
  - spring
  - jpa
last_modified_at: 2020-02-29T12:30:00-05:00
---



## 만들게 된 배경

이전 포스팅에서 Mybatis와 JPA, Querydsl 을 사용하도록 설정하였다.

다만, 데이터베이스에 접근하는 객체 자체가 여러 관리 포인트가 필요하다보니 이를 하나의 관리포인트로 합치고 싶었다.

즉, `JpaRepository`를 상속받은 `Repository Interface `하나에서 사용되게끔 하고 싶었다. 



이렇게 만듬으로써 장점은 다음과 같다.

1. Repository Interface 하나만 주입받아서 `Service layer` 에서 사용하면 된다.
2. 여러 클래스를 확인하지 않아도 3개의 기능을 모두 활용할 수 있다.
3. 하나의 포인트로 자동완성이 3개의 기능에서 제공하는 메소드를 모두 지원해준다.



## 하나로 설정하는 법

TestSample(entity)객체 와 같은 디렉토리에 TestSampleRepository 생성

extends JpaRepository<TestSample, String>, TestSampleRepositoryCustom(네이밍 꼭 맞춰야함) 추가

### 1. TestSampleRepository

```java
@Repository public interface TestSampleRepository extends JpaRepository<TestSample, String>, TestSampleRepositoryCustom {
}
```

### 2. TestSampleRepositoryCustom

```java
TestSampleRepositoryCustom 인터페이스 생성, 사용할 메소드명 및 파라미터 지정
public interface TestSampleRepositoryCustom {
TestSample test() { }
}
```

TestSampleRepositoryImpl 클래스 생성 후 implements TestSampleRepositoryCustom 추가

미구현된 메소드 구현하기

### 3. TestSampleRepositoryImpl

```java
public class TestSampleRepositoryImpl implements TestSampleRepositoryCustom {
    SqlSessionTemplate sqlSessionTemplate;
    String namespace = "testsample.TestSample.";

    @Autowired
    public TestSampleRepositoryImpl(@Qualifier("SqlSessionTemplate") SqlSessionTemplate sqlSessionTemplate){
        this.sqlSessionTemplate = sqlSessionTemplate;
    }

    @Override
    public TestSample test() {
        return sqlSessionTemplate.selectOne(namespace+"read");
    }
}
```

### 4. mybatis mapper xml

xml 파일에서 지정한 id 값을 TestSampleRepositoryImpl에서 sqlSessionTemplate를 이용해서 .selectOne, selectList, .update 등등의 기능을 사용하여 xml 파일의 쿼리를 사용할 수 있도록 지정 

```xml
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="testsample.TestSample">
    <select id="read" parameterType="string" resultType="testsample.TestSample">
        SELECT * FROM test_sample limit 1;
    </select>
</mapper>
```



## 동작 확인

```java
@Test
public void MybatisWithJpaRepositoryTest(){
    testSampleRepository.findAll(); // JpaRepository 기능 사용
    testSampleRepository.test();  // mybatis에서 구현한 맵퍼 사용
}
```