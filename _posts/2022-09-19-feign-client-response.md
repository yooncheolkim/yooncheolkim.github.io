---
layout: post
title: spring feign client http 정보
date: 2022-09-19 22:30
tags: spring feign client http
description:
---

### feign client response

- 프로젝트 진행중, be에서 외부 api request 할일이 생기면서, api request 시, 오류 건에 대하여, 이력을 남기는 작업을 진행하였다.
- feign client 로 api request를 진행하였는데, 이력을 남기기위한 정보(request url, pathparam, queryparam, requestbody..)가 필요하다.

#### 기존 feign client response

```java
@FeignClient(
        name = "externalApiCall",
        url = "${external.api.url}",
        primary = false)
public interface ExternalApiFeign {
    @PostMapping(path = "/contract")
    ExternalResponseDTO retrieveContractInfo(
            @RequestBody ExternalRequestDTO dto);
}
```

- 위 코드처럼 하게되면, feign에서 알아서 http response body를 꺼내어 ExternalResponseDTO로 만들어준다.
- 그런데, 나는 dto 뿐만아니라, 이력을 남기기 위한 정보(request url, pathparam, queryparam, requestbody..)가 필요했다.

```java
    @PostMapping(path = "/contract")
        Response retrieveContractInfo(
            @RequestBody ExternalRequestDTO dto);
```

- Feign의 Response 를 리턴하도록 하고, 내부 로직에서 해당 response에서 request url, pathparam, requestbody 등등을 꺼내어 사용할 수 있다.

#### 에러

- feign에서는 기본적으로 200~300 http statuscode는 정상응답으로 처리하고, 이외의 상태코드는 FeignException을 발생시킨다.
- 하지만 리턴타입을 Response로 해놓으면, 4xx의 응답도 정상응답으로 처리하고, 사용하는 개발자에게 처리하도록 만들어 놓았다.
- 이럴때는, feign에서 제공하는 ErrorDecoder 를 사용하여, 처리 가능하다. (참고 : https://www.baeldung.com/java-feign-client-exception-handling)

#### 공통화

- 이력을 남기는 작업은 비즈니스 로직 곳곳에서 이뤄진다.
- spring aop를 이용하여, 외부 호출 전, 후로 이력을 남기는 작업을 진행하면, 공통화 하여 처리할 수 있을듯하다.
