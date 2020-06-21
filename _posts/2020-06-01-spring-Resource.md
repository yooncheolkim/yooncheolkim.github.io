---
layout: post
title: "Resource"
date: 2020-06-01 22:21:00
tags: spring Resource
description: spring Resource 추상화
---

# Resource 추상화
- org.springframework.core.io.Resource
- java.net.URL 를 추상화
- 추상화를 시킨 이유 : 
    - java.net.URL 가 classpath기준으로 가져오는 기능이 없음., prefix를 제공, 
    - resource 지칭, 가져오는것을 통일 시킴
    - SevletContext를 기준으로 상대 경로로 읽어오는 기능 부재
    - 새로운 핸들러를 등록하여 특별한 URL 접미사를 만들어 사용할 수는 있지만 구현이 복잡하고 편의성 메소드가 부족하다.


### 인터페이스
- 주요메소드
    - getInputStream()
    - existed
    - isOpen()
    - getDescription() : 전체 경로를 포함한 파일 이름 또는 실제 URL

## 구현체
- UrlResrouce : java.net.URL 참고, 기본으로 지원하는 프로토몰 http, https, ftp, file, jar.
- ClassPathResource: 지원하는 접두어 classpath:
- FIleSystemResource
- ServletContextResource : 웹 애플리케이션 루트에서 상대 경로로 리소스 찾기 , 가장많이 씀

##리소스 읽어오기
**Resource의 타입은 location 문자열과 ApplicationContext의 타입에 따라 결정된다.**
- 예를 들어, 
- var ctx = FileSystemXmlApplicationContext("xxx.xml") 
- 이렇게 하게 되면, FileSystemResource로 resolving 하게 된다.

**ApplicationContext의 타입에 상관없이 리소스 타입을 강제하려면 java.net.URL, 접두어(+classpath:)중 하나를 사용할 수 있다.**
- **classpath:**io/github/yooncheolkim/config.xml -> ClassPathResource
- **file://**some/resource/path/config.xml -> FileSystemResource

- 이렇게 classpath, file 과 같은 접두어를 사용하면, 확실히 명시적이기 때문에, 리소스가 어디서 오는지 명확화할수 있다.




{%highlight java%}
    @Autowired
    ApplicationContext resourceLoader;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        System.out.println(resourceLoader.getClass());

        Resource resource = resourceLoader.getResource("classpath:test.txt");
        System.out.println(resource.getClass());
        System.out.println(resource.exists());
        System.out.println(resource.getDescription());
    }
{%endhighlight%}

- 결과
~~~
class org.springframework.context.annotation.AnnotationConfigApplicationContext
class org.springframework.core.io.ClassPathResource
true
class path resource [test.txt]
~~~

{%highlight java%}
    @Autowired
    ApplicationContext resourceLoader;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        System.out.println(resourceLoader.getClass());

        Resource resource = resourceLoader.getResource("test.txt");
        System.out.println(resource.getClass());
        System.out.println(resource.exists());
        System.out.println(resource.getDescription());
    }
{%endhighlight%}
- 결과
~~~
class org.springframework.context.annotation.AnnotationConfigApplicationContext
class org.springframework.core.io.DefaultResourceLoader$ClassPathContextResource
true
class path resource [test.txt]
~~~
- location 문자열이 변경되지 resource 구현체가 변경됨.
