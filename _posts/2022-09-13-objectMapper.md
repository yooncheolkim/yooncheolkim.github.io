---
layout: post
title: object mapper 정리
date: 2022-09-13 14:30
tags: objectMapper
description: 많이 사용하는 object mapper 정리
---

### object mapper 정리

- java spring을 사용하다 보니, 불가피하다.
- 매번 구글링하기 귀찮아서 정리.

#### convertValue

- object(map 포함) to object

#### readValue

- jsonString to object

#### TypeReference

- objectMapper 의 변환 함수는 두번째 인자로 TypeReference를 받을 수 있다.

```java
mapper.readValue(jsonStr, CommonResponseDTO.class);
// instead of
mapper.readValue(jsonStr, new TypeReference<CommonResponseDTO<TestDto>>() {});
```

- Class 타입으로 받으면, mapper가 제네릭까지 알수 없기 때문에 TypeReference를 사용하여 convert 가능하다.
- 컴파일 시점에 알수 없는 key, value에 대해서는 Map으로 convert 하는듯

### spring controller에서 json convert

- spring http message converter는 controller에서 응답을 줄때, 어떤 형태(json? xml?)로 줄지 결정한다.
- spring mvc는 spring 프로젝트 의존성에 따라 메시지 컨버터를 spring mvb에서 등록한다.
  - WebMvcConfigurationSupport 에서 필요한 라이브러리의 classPath가 있는지 확인하여 등록함.
- http contentType/ acceptType을 기본적으로 참고한다.

#### spring boot에서 json converter

- spring boot를 사용하는 경우 기본적으로 jacksonJson2 의존성(objectMapper)이 들어있다. 즉, JSON용 http 메시지 컨버터가 잇는거임.

#### 존재하지 않는 field mapping

- FAIL_ON_UNKNOWN_PROPERTIES 설정이 기본적으로 켜져있다. 그렇기 때문에, 존재하지 않는 field를 dto로 변환하려고 하면 fail 됨.
- 특히, 외부 연동을 할 경우, 아무 생각없이 json에 field 하나 추가해서 에러가 발생할 수도 있다. 이때 해당 설정을 disable 해야 문제가 없을것이다.
- 상황에 맞게, 해당 설정을 disabled 하자.
