---
title:  "[Spring] 다중데이터소스 설정, application.yaml 분리 후 Testcase 오류 발생 및 해결"
excerpt: "다중데이터소스 설정 시 application.yaml에서 database.yaml로 분리 후 Testcase 오류 발생 및 해결"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/spring.png

categories:
  - blog
tags:
  - window
  - tip
last_modified_at: 2020-02-28T14:00:00-05:00
---

## 상황 

TDD를 준수하지는 못하지만 만들면서 테스트케이스를 생각하고 준비해놓는다. 테스트케이스를 작성함으로써 하나의 기능이 수정되었을 때 영향을 받는 범위나 실행이 제대로 되지 않는 것에 대해서 사전에 파악할 수 있기 때문에 열심히 작성하고 있다. 

그러나 `다중데이터소스(Multiple DataSource)` 를 설정을 진행하면서 테스트케이스에서 에러가 발생하기 시작했다. 

오류 내용은 다음과 같았다.

```java
Caused by: java.lang.IllegalArgumentException: dataSource or dataSourceClassName or jdbcUrl is required.
```

물론 더 긴 내용이 있겠지만 대충 내용을 파악하자면 `DataSource`가 제대로 생성되지 않아서 이 후 모든 주입되는 빈에 영향을 주고 있었다. 상당히 고통을 받았으나 해결하게 되었으므로 오늘은 해결 과정에 대해서 포스팅을 해보고자 한다.



## 해결 과정

### jdbc-url 설정 오류?

많은 내용을 구글에서 검색하며 찾아본 결과 [다음의 포스팅](https://jojoldu.tistory.com/296)을 발견하였다. 해당 내용은 이미 설정이 되어 있음을 확인하였고 문제가 되는 부분이 아니라고 인식하게 되었다.



### 다중으로 datasource를 설정을 잘못했나? 

의심을 하게 되었다. 새로운 프로젝트를 만들어서 `datasource` 를 다중으로 설정해서 동작시켜보았으나 정상 작동하는 것을 확인할 수 있었다. 실험 완료된 코드를 서비스에 적용하였으나 동일한 오류 발생하였다.

테스트케이스에서만 오류가 발생하는 것으로 보아 `application.yam`l과 `database.yaml`로 나뉜 구조가 문제인 지 확인해보았다. 

결과적으로 `database.yaml` 파일을 테스트코드에서 제대로 읽어오지 못해서 발생하는 문제임을 파악했고 이를 해결하기 위한 검색을 진행하였다. 



### {customeName}.yaml 을 읽을 수 있도록 설정하기 

`application.yaml` 파일 나누기, database.yaml 등등 검색을 진행하였으나 `application.yaml` 을 `-loc`, `-test` 등으로 개발 환경을 나누는 방법에 대해서만 나와있고 `{customeName}.yaml`에 대한 답변은 찾을 수 없었다. 

`Test code`에서만 제대로 bean을 가져오지 못하는 상황으로 `test code`에서만 yaml파일을 따로 읽으면 해결이 될 것으로 판단하고 관련된 내용을 검색하였다. 



### 해결 

결론적으로 해결방법에 대한 자료는 다음의 블로그에서 찾았습니다.
[@SpringBootTest에서 Yaml 프로퍼티 적용하기](https://velog.io/%40hellozin/Spring-Boot-Test%EC%97%90%EC%84%9C-Yaml-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)

요약하자면 테스트케이스에서의 설정을 다음과 같이 추가해주면 됩니다. (properties 부분 확인 요망)

```java
@AutoConfigureMockMvc@SpringBootTest(    
    webEnvironment = SpringBootTest.WebEnvironment.MOCK,    
    properties = "spring.config.location ="+        
    	"classpath:/application.yaml"+        
    	", classpath:/database.yaml"
)
public class ControllerTest {   ...}
```
다중데이터소스를 설정하였는데, 갑자기 테스트케이스가 동작하지 않으신다면 `application.yaml`을 각 역할에 알맞게 나누다가 테스트케이스에서 오류가 발생하시는 분께 도움이 되길 바란다.



## 해결 과정 중 얻은 것 

여담이지만 위의 에러를 해결하는 과정에서 `application.yaml` 사용에 대한 부분을 학습할 수 있었는데 괜찮은 내용 공유한다.

[[application.yaml 파일 설정 1\]](https://cnpnote.tistory.com/entry/SPRING-YAML%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-Spring-PropertySource)

[[application.yaml 파일 설정 2\]](https://jeong-pro.tistory.com/159)




