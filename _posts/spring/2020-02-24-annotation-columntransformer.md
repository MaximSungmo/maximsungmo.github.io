---
title:  "[Spring] @ColumnTransformer 을 통한 JPA 암복호화 적용하기"
excerpt: "@ColumnTransformer 을 통한 JPA 암복호화 적용하기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/postgres-image.png

categories:
  - spring
tags:
  - spring  
last_modified_at: 2020-02-24T14:00:00-05:00
---

## Java application을 통한 암복호화 방법

DB단에서 암복호화가 이루어지는 경우에 Java application에서 어떻게 처리할 수 있을까?

여러가지 방법이 있겠지만 크게 2가지 방법으로 나눌 수 있다.

1. DB 에서의 암복호화 진행 
2. Java application 에서의 암복호화 진행

우리는 개발자이다. 그러므로 코드로 이야기하는 게 더 이해가 쉬울 것 같다. 단순하게 생각하면 다음의 코드로 표현이 가능하다. (여러 가지 코드 표현이 있을 수 있겠지만 예시는 간단하게 하겠다.)

1. DB 에서의 암복호화 진행 코드

```sql
-- in Database
-- 암호화
select encode(encrypt(convert_to('홍길동','utf8'),'ENC_KEY','aes'),'hex');
-- 복호화
select convert_from(decrypt(decode('08a9632b88195e92312ec5c86dc81bfb','hex'),'ENC_KEY','aes'),'utf8');
```

2. Java application 에서의 암복호화 진행

```java
-- in Java(Spring)

-- Spring JPA

// 암호화
public Stirng setInfo() {
    return EncryptCustomMethod(this.encryptedInfo);
}


// 복호화
public Stirng getInfo() {
    return DecryptCustomMethod(this.info);
}
```

근데 관련된 자료 중 JPA에서 사용할 수 있는 Domain객체에 걸 수 있는 Annotation을 발견하여 공유하고자 한다.



## @ColumnTransformer 사용법

[@ColumnTransformer 공식문서]( https://docs.jboss.org/hibernate/orm/5.3/javadocs/org/hibernate/annotations/ColumnTransformer.html )을 참고하였다.

### 0. DB암호화 모듈 설치

DB 처음 암호화 작업하시는 분은 다음의 설정으로 DB에 암호화 모듈을 설치하길 바란다.

```
-- Postgresql 기준, 쿼리문처럼 입력
CREATE EXTENSION pgcrypto;
```


### 1. Domain객체에 설정

```java
@Entity(name = "test_table")
@NoArgsConstructor
@Data
@Table(name = "test_table")
public class TestTable {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "TEST_SEQ_GENERATOR")
    private int c1;

    @Column(name = "c2",length = 16)
    private String c2 ;

    @Column(name = "insert_date", length = 20)
    private Date insertDate = new Date();

    @Column(name="c3")
    @ColumnTransformer(
            read = "convert_from(decrypt(decode(c3,'hex'),'ENC_KEY','aes'),'utf8')",
            write = "encode(encrypt(convert_to(?,'utf8'),'ENC_KEY','aes'),'hex')"
    )
    private String c3;
}
```

위의 상황을 좀 더 자세히 이야기하자면 Database에 들어갈 때는 최종적으로 암호화된 값이 들어갈 것이고 암호화된 자료를 가져나올 땐 복호화시켜 가져오겠다는 의미이다.

### 2. 사용 테스트 케이스 

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
public class TestTableTest {

    @Autowired
    private TestTableRepository testTableRepository;

    @Test
    public void ColumnTransformerSaveTest() {
        TestTable testable = new TestTable();
        testable.setC2("tttt");
        testable.setC3("willBeEncrypted");

        System.out.println(testTableRepository.save(testable));
    }

    @Test
    public void ColumnTransformerSelectTest() {
        testTableRepository.findById(52);
        System.out.println(testTableRepository.findById(52).get());
    }
}
```

테스트케이스는 값이 찍히는 것을 확인하고자 간단하게 console에 찍어보았다. 

### 3. 결과 확인

- ColumnTransformerSaveTest

```sql
Hibernate: 
    select
        nextval ('TEST_SEQ_GENERATOR')
Hibernate: 
    insert 
    into
        test_table
        (c2, c3, insert_date, c1) 
    values
        (?, encode(encrypt(convert_to(?,'utf8'),'ENC_KEY','aes'),'hex'), ?, ?)
TestTable(c1=52, c2=tttt, insertDate=Mon Feb 24 11:00:35 KST 2020, c3=willBeEncrypted)
```

- ColumnTransformerSelectTest 결과 

```sql
Hibernate: 
    select
        testtable0_.c1 as c1_0_0_,
        testtable0_.c2 as c2_0_0_,
        convert_from(decrypt(decode(testtable0_.c3,
        'hex'),
        'ENC_KEY',
        'aes'),
        'utf8') as c3_0_0_,
        testtable0_.insert_date as insert_d4_0_0_ 
    from
        test_table testtable0_ 
    where
        testtable0_.c1=?
Hibernate: 
    select
        testtable0_.c1 as c1_0_0_,
        testtable0_.c2 as c2_0_0_,
        convert_from(decrypt(decode(testtable0_.c3,
        'hex'),
        'ENC_KEY',
        'aes'),
        'utf8') as c3_0_0_,
        testtable0_.insert_date as insert_d4_0_0_ 
    from
        test_table testtable0_ 
    where
        testtable0_.c1=?
TestTable(c1=52, c2=tttt, insertDate=Mon Feb 24 11:00:35 KST 2020, c3=willBeEncrypted)
```



### 4. 암복호화 키,  코드에서 제거하기 

현재는 암복호화 키가 코드에 하드코딩되어 있는 상태이다.

이는 보안적으로 좋지 못한 방식이므로 yaml 또는 properties 파일로 빼는 것이 옳은 것으로 판단이 되었다.

그러나 2020-02-24 기준, 외부 파일로 빼서 관리하는 방법에 대해서는 찾지 못하였다.

다만 다른 방식으로 database 내에 암복호화 함수의 키를 리턴하는 Function을 하나 만들어서 사용하는 방식을 공유하겠다. 만약 다른 누군가가 외부파일로 암복호화키를 빼서 관리하는 방식에 대해서 공유해주신다면 감사하겠다.

- 데이터베이스에 함수 생성 

```sql
CREATE OR REPLACE FUNCTION enc_test() RETURNS bytea AS $$
BEGIN
RETURN 'ENC_KEY';
END; $$
LANGUAGE PLPGSQL;
```

나는 처음에 char 타입으로 returns 를 설정해서 테이블을 지웠었다.

```sql
DROP FUNCTION enc_test()
```

제대로 입력되었는 지 확인해보자 타입은 `bytea` 로 반환이 되어야 한다.

```sql
SELECT enc_test();
```



- 기존 코드에서 변경 사항 

```java
-- in java, TestTable Class
class TestTable {
    @Column(name="c3")
    @ColumnTransformer(
            read = "convert_from(decrypt(decode(c3,'hex'), enc_test(),'aes'),'utf8')",
            write = "encode(encrypt(convert_to(?,'utf8'),'ENC_KEY','aes'),'hex')"
    )
    private String c3;
}
```

기존에 하드코딩으로 `ENC_KEY` 로 작성했던 부분을 데이터베이스 내부 function으로 작성하였다.

테스트를 동일하게 해보면 정상 작동하는 것을 알 수 있다.



## 마무리 

상황에 따라서 Database에서 암복호화를 진행하는 경우가 있을 수 있고 Java application단에서 암복호화를 진행할 경우가 있겠지만  위의 방법으로 Java application 단에서 암복호화를 신경쓰지 않고 Annotation 설정만으로 쉽게 조작할 수 있다는 것이 참 매력적으로 다가왔다. 