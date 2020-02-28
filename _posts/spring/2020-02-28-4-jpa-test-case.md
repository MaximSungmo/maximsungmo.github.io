---
title:  "[Spring] 간단한 TestCase 만들기"
excerpt: "Spring 간단한 TestCase 만들기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/spring.png

categories:
  - spring
tags:
  - spring
  - jpa
last_modified_at: 2020-02-28T13:53:00-05:00
---



## 테스트 설정

### build.gradle

```groovy
implementation 'junit:junit:4.12'
testImplementation('org.springframework.boot:spring-boot-starter-test') {
    exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
}
```



### Test

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc
public class TestTableTest {


    @Autowired
    private MockMvc mockMvc;
    
    @Test
    public void tes() {
    }


    @Test
    public void getAllTest() throws Exception {
        mockMvc.perform(get("/mybatis"))
               .andExpect(status().isOk())
               .andDo(print());
    }
}
```