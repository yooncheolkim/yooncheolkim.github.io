---
layout: post
title: "spring 빈 스코프"
date: 2020-05-17 22:00:00
tags: spring
description: spring 빈 스코프
---

# 빈의 스코프

## 싱글톤

{%highlight java%}

@Component
public class Single {

    @Autowired
    Proto proto;

    public Proto getProto() {
        return proto;
    }
}

@Component
public class Proto {
}

{%endhighlight%}
- Single의 proto 와 Proto는 같은 인스턴스 이다.
- 기본적으로 Singleton.


## 새로운 빈을 원한다면?
{%highlight java%}
@Component @Scope("prototype")
public class Proto {
}
{%endhighlight%}

- Proto 타입의 빈을 가져올때 마다 새로운 인스턴스를 생성한다.

## 프로토 타입 빈이 싱글톤 빈을 참조
- 문제 없음

## 싱글톤 빈이 프로토타입 빈을 참조
- 싱글톤 빈을 가져올때 마다, 프로토 타입 빈이 변경되어야 되지 않을까? -> 변경되지 않음.
- 변경 되게 하려먼
    - scoped-proxy
        - @Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)
        - 프록시로 proto를 감싸서, 변경될 수 있도록 한다.
        - 실제로 감싸고 있는 프록시 빈을 주입 받는것
    - Object-Provider
    