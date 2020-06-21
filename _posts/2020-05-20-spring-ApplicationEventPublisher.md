---
layout: post
title: "spring ApplicationEventPublisher"
date: 2020-05-20 22:00:00
tags: spring
description: spring ApplicationEventPublisher
---

# ApplicationEventPublisher
- 옵저버패턴 구현, 이벤트 프로그래밍 인터페이스 제공


## 4.2 이전 이벤트 사용법
{%highlight java%}
public class MyEvent extends ApplicationEvent {

    private int data;

    public MyEvent(Object source) {
        super(source);
    }

    public MyEvent(Object source, int data){
        super(source);
        this.data = data;
    }

    public int getData() {
        return data;
    }
}

@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationEventPublisher applicationContext;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        applicationContext.publishEvent(new MyEvent(this,100));
    }
}

@Component
public class MyEventhandler implements ApplicationListener<MyEvent> {
    @Override
    public void onApplicationEvent(MyEvent event) {
        System.out.println("이벤트 받았따." + event.getData());
    }
}

{%endhighlight%}

- ApplicationEvent를 상속받은 이벤트 객체를 생성하고,
- applicationContext.publishEvent를 사용하여, 이벤트를 핸들러에 전달
- ApplicationListener<MyEvent> 를 구현한 이벤트 핸들러에서 해당 이벤트를 사용하여, 코딩...


## ApplicationEvent 필요없이 이벤트 발생시키기

{%highlight java%}

public class MyEvent{

    private int data;

    Object object;

    public MyEvent(Object source) {
        object = source;
    }

    public MyEvent(Object source, int data){
        object = source;
        this.data = data;
    }

    public int getData() {
        return data;
    }
}

@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationEventPublisher applicationContext;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        applicationContext.publishEvent(new MyEvent(this,100));
    }
}

@Component
public class MyEventhandler  {

    @EventListener
    public void handle(MyEvent event){
        System.out.println("이벤트 발생" + event.getData());
    }
}
{%endhighlight%}

- event 와 핸들러가 더욱 pojo 스러워 졌다.
- 스프링 비 침투성,
- handler 메소드에 @EventListener 달아줘야, 스프링이 이 메소드가 핸들러인지 알수있음.


## 비동기로, Order, Async
- 같은 event를 처리하는 여러 핸들러가 존재하면 순서를 줄수 있음

### Order
{%highlight java%}
@Component
public class AnotherHandler {

    @EventListener
    @Order(Ordered.HIGHEST_PRECEDENCE)
    public void handler(MyEvent event){
        System.out.println("another" + event.getData());
        System.out.println(Thread.currentThread().getName());
    }
}
{%endhighlight%}

- Async도 있음..(나중에 정리하자..)


## 스프링이 제공하는 기본 이벤트
- ContextRefreshedEvent : ApplicationContext를 초기화 했더니 리프레시 했을때 발생.
- ContextStartedEvent : ApplicationContext를 Start() 하여 라이프사이클 빈들이 시작 신호를 받은 시점에 발생
- ContextStoppedEvent : ApplicationContext를 Stop() 하여 라이프사이클 빈들이 정지 신호를 받은 시점에 발생
- ContextClosedEvent : ApplicationContext를 close() 하여 싱글톤 빈 소멸되는 시점에 발생.
- RequestHandledEvent : HTTP 요청을 처리했을 때 발생.
