---
layout: post
title: "spring 빈 설정 방법"
date: 2020-05-12 22:00:00
tags: spring
description: spring 빈 설정 방법
---


# spring 전통적 bean 설정 방법 - xml

## resources - application.xml 만들어서 사용한다.

~~~
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

<bean id="bookService" class="io.github.yooncheolkim.demospringioc.BookService"
      scope="singleton"
      autowire="default">
    <property name="bookRepository" ref="bookRepository"/>
</bean>

<bean id="bookRepository"
      class="io.github.yooncheolkim.demospringioc.BookRepository"/>
</beans>
~~~

{% highlight java %}
package io.github.yooncheolkim.demospringioc;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.lang.reflect.Array;
import java.util.Arrays;

//@SpringBootApplication
public class DemospringiocApplication {

    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
        String[] beanDefinitionNames = context.getBeanDefinitionNames();
        System.out.println(Arrays.toString(beanDefinitionNames));
        BookService bookService = (BookService) context.getBean("bookService");
        System.out.println(bookService.bookRepository != null);
    }

}
{% endhighlight %}

### 결과
~~~
[bookService, bookRepository]
true
~~~



## xml에 bean 하나하나 등록하기 싫으니,,

~~~
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="io.github.yooncheolkim.demospringioc" />

</beans>
~~~
- component-scan 이라는 태그 사용하면, @Component 로 된 애들 다 bean으로 등록해준다.
- @Service, @Repository 은 @Component 확장 된 애들
- Autowired, Inject로 주입 받을 수 있음
- spring 2.5 부터 나왔음...



# java class로 빈 등록하기


## @Configuration, @Bean
>java
{% highlight java %}
@Configuration
public class ApplicationConfig {

    @Bean
    public BookRepository bookRepository(){
        return new BookRepository();
    }

    @Bean
    public BookService bookService(){
        BookService bookService = new BookService();
        bookService.setBookRepository(bookRepository());
        return bookService;
    }
}


public class BookRepository {

}

public class BookService {

    BookRepository bookRepository;

    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}

public class DemospringiocApplication {

    public static void main(String[] args) {

        ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
        String[] beanDefinitionNames = context.getBeanDefinitionNames();
        System.out.println(Arrays.toString(beanDefinitionNames));
        BookService bookService = (BookService) context.getBean("bookService");
        System.out.println(bookService.bookRepository != null);
    }

}
{% endhighlight %}

- @Configuration 을 만들어서, 사용하는 방법
- AnnotationConfigApplicationContext




## @ComponentScan()

~~~
@Configuration
@ComponentScan(basePackageClasses = DemospringiocApplication.class)
public class ApplicationConfig {

}

@Repository
public class BookRepository {

}

@Service
public class BookService {

    BookRepository bookRepository;

    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}
~~~



## spring boot 에서 컴포넌트 스캔

- @SpringBootApplication 을 사용하면,,
- @SpringBootApplication 은 @ComponentScan, @Configuration을 확장한 어노테이션 이다.

- 그래서,  @SpringBootApplication 자체가 컴포넌트 스캔 설정 파일 이다. -> 이 어노테이션은 spring boot.