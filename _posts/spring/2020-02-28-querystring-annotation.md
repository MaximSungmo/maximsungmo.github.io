---
title:  "[Spring] RequestParam값 객체로 매핑하기, Custom Annotation 만들기"
excerpt: "Spring RequestParam값 객체로 매핑하기, Custom Annotation 만들기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/spring.png

categories:
  - spring
tags:
  - spring
last_modified_at: 2020-02-28T16:30:00-05:00
---



## 만들게 된 배경

스프링 Controller에서 Request값을 도메인객체를 사용해서 받을 때는 Json으로 넘어오는 경우에는 `@RequestBody`로 받으면 손쉽게 해결할 수 있었다. 

위의 상황을 코드로 이야기하자면 다음과 같다. 

```java
@RequestMapping(value = "/binding", method = RequestMethod.POST)
@ResponseBody
public TesUsrAdmA createUser(
        @RequestBody TestDomain testDomain
) {
    return testDomain;
}
```

Post 의 payload에 데이터가 json 형식으로 포함되어 다음의 형식으로 넘어온 것을 `jackson library`가 `messageConvert` 해준다.

```json
{
    name : "이름",
    etc : "etc들"
}
```

하지만 QueryString으로 데이터가 넘어온다면? 

더욱 쉽게 설명하자면 Get에서 url 뒤에 값을 넣어서 보내준다면 이것에 대한 정보를 도메인 객체로 받을 수 있을까?

```html
localhost/help/domain/get_req_domain?adm_hp=143&adm_nm=2222&admId=123
```

결론적으로 객체로는 받을 수 없었다. 객체로는 받지 못하지만 각 하나의 값으로는 `@RequestParam`으로 받을 수 있었다.

```java
@RequestMapping(value = "/binding2", method = RequestMethod.POST)
@ResponseBody
public TesUsrAdmA createUser2(
        @RequestParam("adm_id") String admId
        @RequestParam("adm_nm") String admNm
        @RequestParam("adm_hp") String admHp
) {
    TestDomain testDomain = new TestDomain();
    testDomain.setAdmId("admId");
    testDomain.setAdmNm("admNm");
    ...
    return testDomain;
}
```



## Annotatin 만들기

이를 해결하기 위한 Custom Annotation을 만들어보자.

### Annotation 생성

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.PARAMETER)
public @interface QueryStringArgResolver {
}
```

앞으로는 `@QueryStringArgResolver`를 `@RequestBody` 대신에 사용하면 QueryString을 객체에 매핑시켜줄 수 있도록 만들 것이다.

### Annotation비교 후 처리 로직

```java
@Component
public class QueryStringArgumentResolver implements HandlerMethodArgumentResolver {

    @Autowired
    private ObjectMapper mapper;


    @Override
    public boolean supportsParameter(final MethodParameter methodParameter) {
        return methodParameter.getParameterAnnotation(QueryStringArgResolver.class) != null;
    }


    @Override
    public Object resolveArgument(final MethodParameter methodParameter,
                                  final ModelAndViewContainer modelAndViewContainer,
                                  final NativeWebRequest nativeWebRequest,
                                  final WebDataBinderFactory webDataBinderFactory) throws Exception {

        final HttpServletRequest request = (HttpServletRequest) nativeWebRequest.getNativeRequest();
        final String json = qs2json(request.getQueryString());
        final Object a = mapper.readValue(json, methodParameter.getParameterType());
        return a;
    }


    private String qs2json(String a) {
        String res = "{\"";

        for (int i = 0; i < a.length(); i++) {
            if (a.charAt(i) == '=') {
                res += "\"" + ":" + "\"";
            } else if (a.charAt(i) == '&') {
                res += "\"" + "," + "\"";
            } else {
                res += a.charAt(i);
            }
        }
        res += "\"" + "}";
        return res;
    }
}
```

`@QueryStringArgResolver` 을 만나게 되면 request 값에서 `getQueryString()`을 얻을 수 있습니다.

얻어낸 값을 Json type으로 변경시켜주는 작업을 `qs2json(..)` 이라는 메소드를 통해서 작업해주고 리턴된 json 형식의 String값을 `jackson`을 이용하여 object에 매핑될 수 있도록 작업해줍니다.

이때 `jackson`은 `@Autowired`된 값을 사용하며 이유는 `jackson`과 관련된 설정이 `@RequestBody`에서 동작하는 것과 동일하게 동작할 수 있도록 하기 위함입니다. `(jackson 스프링부트에서 지원하는 기본 messageConverter 라이브러리, ObjectMapper가 이에 해당함)`

### ArgumentResolvers 로 등록

```java
@Configuration
@RequiredArgsConstructor
public class WebConfig implements WebMvcConfigurer {
    private final QueryStringArgumentResolver argumentResolver;


    @Override
    public void addArgumentResolvers(
            final List<HandlerMethodArgumentResolver> argumentResolvers) {
        argumentResolvers.add(argumentResolver);
    }
}
```



### 실제 사용하는 법

```java
@RequestMapping(value = "/binding2", method = RequestMethod.POST)
@ResponseBody
public TesUsrAdmA createUser2(
        @QueryStringArgResolver TestDomain testDomain
) {
    return testDomain;
}
```

`@RequestBody`와 동일하게 QueryString으로 넘어오는 값들에 대해서는 `@QueryStringArgResolver` 을 적용하면 QueryString을 읽어서 Json형식의 String 값으로 바꿔준 뒤 `Jackson(ObjectMapper)`를 사용해서 객체에 매핑할 수 있게 도와준다

만일 내용이 이해가 잘 안가신다면 `Jackson동작 원리`에 대해서 확인하시면 좋은 공부가 될 것으로 판단이 되며 당장 문제를 해결하기 위해서라면 위의 코드를 복사해서 사용하면 된다.



references:

[[Spring Boot deserialization snake case to camel case fails. Can't deserialize “some_value” to “someValue”](https://stackoverflow.com/questions/55203735/spring-boot-deserialization-snake-case-to-camel-case-fails-cant-deserialize-s/55204091#55204091)









references:

