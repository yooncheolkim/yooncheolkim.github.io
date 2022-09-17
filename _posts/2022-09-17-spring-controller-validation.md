---
layout: post
title: spring controller validation
date: 2022-09-17 12:30
tags: controller validation
description:
---

### controller parameter validation

- 기본적으로 springmvc는 JSR 303 implementation, hibernate-validator 같은 library가 추가되어야한다.
  - 그리고 @Configure로 bean 등록하여야한다.
- spiring boot는 자동으로 등록되어있다.
- 기본적으로 path or request param error 는 500 http error 로 response 된다.
  - @ControllerAdvice로 custom error 처리를 할 수 있다.

#### validation

- javax.validation.constraints package에 있는 모든 validation 을 사용할 수 있다.

```
    //request /testController?age=100
    @PostMapping("/testController")
    public ResponseEntity<ResponseData> test(@RequestParam @Min(1) @Max(99) Integer age) {
        return ResponseUtility.createPostAsyncSuccessResponse(null);
    }

    //결과 : test.age: 99 이하여야 합니다
```

```
    @PostMapping("/testController/{name}")
    public ResponseEntity<ResponseData> updateMemberWallets(@RequestParam @Min(1) @Max(99) Integer age, @PathVariable(name = "name") @NotBlank @Size(min = 10) String name) {
        return ResponseUtility.createPostAsyncSuccessResponse(null);
    }

    //결과 : test.name: 크기가 10에서 2147483647 사이여야 합니다
```

- @RequestBody의 경우, 정의된 dto의 각 필드에 validation annotation을 정의하고, @RequestBody @Valid DTO dto 로 하면 validation 처리할 수 있다.

- 참고 : https://www.baeldung.com/spring-validate-requestparam-pathvariable
