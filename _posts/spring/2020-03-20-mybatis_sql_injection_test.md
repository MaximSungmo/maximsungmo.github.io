---
title:  "[Spring] Mybatis 로 SQL Injection Test"
excerpt: "Spring에서 사용되는 Mybatis 로 SQL Injection Test"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/spring.png

categories:
  - spring
tags:
  - spring  
last_modified_at: 2020-03-20T14:00:00-05:00
---

# 스프링부트-마이바티스 SQL INJECTION TEST 결과 공유

## 테스트

```
2019-11-28 02:23:52.185 DEBUG 3317 --- [p-nio-80-exec-3] c.c.c.b.m.d.t.TestUsrAdmAMapper.insert   : ==>  Preparing: INSERT INTO test_usr_adm_a ( adm_id, adm_nm, adm_hp ) VALUES ( ?, 'TEST?', '0000' ) 
2019-11-28 02:23:52.191 DEBUG 3317 --- [p-nio-80-exec-3] c.c.c.b.m.d.t.TestUsrAdmAMapper.insert   : ==> Parameters: aaaa(String)
2019-11-28 02:23:52.200 DEBUG 3317 --- [p-nio-80-exec-3] c.c.c.b.m.d.t.TestUsrAdmAMapper.insert   : <==    Updates: 1

2019-11-28 02:23:52.200 DEBUG 3317 --- [p-nio-80-exec-3] c.c.c.b.m.d.tes.TestUsrAdmAMapper.read   : ==>  Preparing: SELECT adm_id, adm_nm, adm_hp FROM test_usr_adm_a WHERE adm_nm = ? AND adm_id=? 
2019-11-28 02:23:52.200 DEBUG 3317 --- [p-nio-80-exec-3] c.c.c.b.m.d.tes.TestUsrAdmAMapper.read   : ==> Parameters: "" OR 1=1 --(String), aaaa(String)
2019-11-28 02:23:52.206 DEBUG 3317 --- [p-nio-80-exec-3] c.c.c.b.m.d.tes.TestUsrAdmAMapper.read   : <==      Total: 0
```

## 결론 

SQL Injection을 위하여 `"" OR 1=1 --` 를 넣어 모든 사용자 정보를 가져오려고 하였으나 PreparedStatement로 작동하는 mybatis의 `#{}` 을 통한 데이터 바인딩 사용으로 인해 `"" OR 1=1 --` 가 문자열 처리가 되어 쿼리 무력화가 되지 않았다. 

관련된 내용을 찾던 중 SK인포섹의 글을 발견하였다. 

[SQL인젝션 - Prepared Statement를 쓰면 SQL 인젝션 공격이 되지 않는 이유는?](http://blog.skinfosec.com/220482240245)

해당 링크의 결론은 다음과 같다. 
> Prepared Statement에서 바인딩 변수를 사용하였을 때, 쿼리의 문법 처리 과정이 미리 선 수행되기 대문에 바인딩 데이터는 SQL 문법적인 의미를 가질 수 없습니다. 따라서 Prepared Statement를 사용하면SQL 인젝션 공격에 안전하게 구현할 수 있습니다.

다만, Mybatis에서는  `${}` 을 통해서도 데이터 바인딩이 가능하나 prepared statment로 동작하지 않아 sql injection에 취약할 수 있다.

또한 xss 에 대하여 확인하려고 `<script> </script>` 데이터를 넣었으나 해당 데이터 &lt와 같은 변환은 진행되지 않으므로 별도의 필터 적용이 필요할 것으로 보인다. 

현재 제작되어 있는 것으로는 네이버에서 제공하는 lucy 라는 오픈소스가 있다. 
[Lucy XSS Filter](http://naver.github.io/lucy-xss-filter/kr/)



references :  
[SQL인젝션 - Prepared Statement를 쓰면 SQL 인젝션 공격이 되지 않는 이유는?](http://blog.skinfosec.com/220482240245)



