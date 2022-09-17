---
layout: post
title: profile에 따라 bean 주입
date: 2022-09-17 11:30
tags: bean profile primary
description: profile에 따라 bean 주입하는 방법
---

### bean 주입 방식

- bean 주입 방식은 필드 주입(@autowire), final 을 이용한 생성자 주입, bean 주입이 있다.

#### bean으로 등록하기

- @Service, @Repository, @RestController 들은 모두 @Component 로 되어있어, 런타임시 자동으로 spring bean으로 등록된다.
  - 내가 만든 class를 bean으로 등록하고 싶다면, @Component를 붙여주면 spring component 스캔대상이 되어 bean으로 등록된다.
  - 즉, spring 에게 알아서 bean으로 등록해서 인스턴스 생성/관리 해달라고 하는것
- @Configure class에 @Bean 메소드(setter, builder)를 생성하여, bean 등록할 수 있다.
  - 주로 외부 라이브러리(redis관련, swagger) 를 등록할때 사용된다.
  ```java
    @Bean
    public RedisTemplate<String, Object> redisTemplate() throws SystemException {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(this.redisConnectionFactory());
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(new StringRedisSerializer());
        return redisTemplate;
    }
  ```
  - 개발하면서 생성한 클래스도 @Bean을 사용하여 등록가능하다.
  ```java
    @Bean
    @Primary
    public FeignUtility feignUtility() {
        return new FeignUtilityMock();
    }
  ```

#### profile에 따라 bean 주입을 선택적으로

- feign client를 사용하여, 외부 api call을 개발한 경우, 모듈 테스트를 위하여, 실제 request를 하지 않도록 하고, mocking 해야하는 경우가 있다.
- 이때 feign interface를 구현한 mocking 클래스를 @Bean으로 주입하여, spring app이 기동되도록 profile과 간단한 설정으로 가능하다.

```java
// build.gradle 설정 : automation profile
task bootRunAutomation {
    group = "application"
    gradle.taskGraph.whenReady { graph ->
        if (graph.hasTask(bootRunAutomation)) {
            bootRun {
                configure {
                    systemProperty "spring.profiles.active", "automation"
                }
            }
        }
    }
    finalizedBy("bootRun")
}
```

```java
// application-automation.yml
spring:
  config:
    import: application-local.yml
```

```java
@Configuration
@Profile({"automation", "feature"})
public class MockConfig {

    @Bean
    @Primary
    public FeignUtility feignUtility() {
        return new FeignUtilityMock();
    }
}
```

```java
//실제 Feign client interface
@FeignClient(
        name = "feignUtility",
        url = "${feign.url}",
        primary = false)
public interface FeignUtility {
    @PostMapping(path = "/member")
    MemberDTO getMemberInfo(
            @RequestBody RequestDTO requestDTO);
}
```
