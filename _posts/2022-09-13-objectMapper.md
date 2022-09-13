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

```
mapper.readValue(jsonStr, CommonResponseDTO.class);
// instead of
mapper.readValue(jsonStr, new TypeReference<CommonResponseDTO<TestDto>>() {});
```

- Class 타입으로 받으면, mapper가 제네릭까지 알수 없기 때문에 TypeReference를 사용하여 convert 가능하다.
- 컴파일 시점에 알수 없는 key, value에 대해서는 Map으로 convert 하는듯
