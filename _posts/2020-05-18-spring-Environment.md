---
layout: post
title: "spring Environment"
date: 2020-05-18 22:00:00
tags: spring
description: spring Environment
---

# ApplicationContext extends EnvironmentCapable
- getEnvironment()


## 프로파일
- 빈들의 그룹
- ex) 개발할때는 A 빈들을 주입 받아서 사용하고, 운영에서는 B 빈들을 사용한다고 할때 A그룹과 B그룹으로 나누어 놓을 수 있다.

{%highlight java%}
    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment environment = ctx.getEnvironment();
        System.out.println(Arrays.toString(environment.getActiveProfiles()));
    }
{%endhighlight%}

- 따로 빈에 어떤 프로파일이라고 설정하지 않으면 default 프로파일 이다.

### 프로파일 설정하기
- 클래스
    - @Configuration @Profile("test")
    - @Component @Profile("test")

{%highlight java%}
@Configuration
@Profile("test")
public class TestConfiguration {

    @Bean
    public BookRepository bookRepository(){
        return new TestBookRepository();
    }
}

@Autowired
BookRepository bookRepository;
{%endhighlight%}

- bookRepository 주입 받을 수 없음,, (프로파일이 test이기 때문에)

- 메소드에 정의
    - @Bean @Profile("test")

### 프로파일 설정
- -Dspring.profiles.active="test,A,B,..."


### 표현식 등록
- @Profile("!prod") : prod가 아닌경우
- ! & | 제공



## 프로퍼티
- 설정값들
- Environment의 역할은 프로퍼티 소스 설정 및 프로퍼티 값 가져오기
- 프로퍼티 가져오는 우선순위가 있음..

### vm option
- -Dapp.name=spring5

### 사용방법
- environment.getProperty("app.name");

### @PropertySource
~~~
@SpringBootApplication
@PropertySource("classpath:/app.properties")
~~~

### 스프링 부트의 외부 설정
- 기본 프로퍼티 소스 application.properties"
