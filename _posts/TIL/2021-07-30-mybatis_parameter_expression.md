---
title:  "[TIL] Mybatis $, # 파라미터 차이점"
excerpt: "$, # 파라미터 차이"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/common/mybatis-log.png

categories:
  - TIL
tags:
  - mybatis
last_modified_at: 2021-07-30T08:00:00-05:00
---

# Mybatis 파라미터 처리 방식 

Mybatis를 사용할 때 파라미터로 넘어온 값을 쿼리에 넣어줄 수 있는 방법은 2가지이다.

1. ${...}

2. \#{...}

아마도 1번의 방식으로 많이 사용했을텐데, 헷갈리는 사람을 위해서 비교를 해본다. 

## 1. ${...}

```sql
<select id="find_user_name" parameterType="HashMap" resultType="HashMap">
    SELECT * 
    FROM USERS
    WHERE  USER_NAME = ${USER_NAME}
</select>
```

`PreparedStaement` 를 생성하게 된다. 따라서 ${..}에 해당하는 값만 지정해서 `문자열로 인식` 하기 때문에 따옴표가 붙는다. 
따옴표가 붙으니 컬럼이나 테이블로 인식하지 않고 파라미터로 인식하여 `SQL Injection을 예방` 할 수 있다.
 

## 2. \#{...}

```sql
<select id="find_user_name" parameterType="HashMap" resultType="HashMap">
    SELECT * 
    FROM USERS
    WHERE  USER_NAME = #{USER_NAME}
</select>
```

\# `statement` 상태의 쿼리가 진행되며 파라미터로 넘어온 값을 그대로 사용하게 된다. 

파라미터를 사용자 입력을 받는 상황에서는 사용자가 불순한 의도로 `SQL Injection 시도하면 보안 측면에서 불리하다.`

근데 이를 사용하는 경우가 있다 예를 들자면
`SQL 구문에 변하지 않는 값으로 삽입`이 되길 원하는 경우, `ORDER BY ${columnName}` 의 방식으로 사용할 수 있다.
이 때 \#{} 을 사용하게 되면 문자열을 변경하거나 이스케이프 처리하지 않는다. 

따라서 만약에 \#{} 을 사용하는 경우라면 사용자 입력값에 대해서 자체적으로 이스케이프 처리를 하고 넘겨주던가 또는 사용자의 입력을 받지 않고 프로그래머가 의도한 대로 처리될 수 있도록 해야만 한다.

\#{} 는 사용상 주의가 필요하다.

## 테스트 작업

[Mybatis 로 SQL Injection Test](https://maximsungmo.github.io/spring/mybatis_sql_injection_test/)

--- 

Reference

[마이바티스 공식 사이트 - 문자열 대체(String Substitution)](https://mybatis.org/mybatis-3/ko/sqlmap-xml.html)

