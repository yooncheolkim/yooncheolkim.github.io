---
layout: post
title: 이펙티브 자바 팩터리 메서드
date: 2020-07-05 21:00:00
tags: java
description: 이펙티브 자바 item 1
---

# 생성자 대신 정적 팩터리 메서드를 고려하라
- 개발자는 public 클래스 말고도 static factory method를 제공할 수 있다.

~~~java
public static Boolean valueOf(boolean b){
    return b? Boolean.TRUE : boolean.FALSE;
}
~~~

### 장점

1. 이름을 가질 수 있다.
- public 생성자는 이름이 없다.
- BigInteger(int,int,Random) 과 BigInteger.probablePrime 중 어느게 더 선명한가


2. 호출될때마다 인스턴스를 새로 생성하지는 않아도 된다.
- Boolean.valueOf(boolean) 은 대표적으로 인스턴스를 아예 생성하지 않는다.
- 반복되는 요청에 같은 객체를 반환하는 식으로 정적 팩터리 방식의 클래스는 언제 어느 인스턴스를 살아 있게 할지를 철저히 통제할 수 있다.
- 이를 인스턴스 통제 클래스라 한다. , 싱글턴, 혹은 인스턴스화 불가


3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
- 반환할 객체의 클래스를 자유롭게 선택할 수 있다.(엄청난 유연성)
- Collections의 정적 팩터리 메서드를 통해 구현체를 얻을 수 있다.(ex. Collections.emptyList, Collections.singletonList ..)

4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
- jdbc 의 경우
- Connection 인터페이스가 서비스 인터페이스 역할
- DriverManager.registerDriver가 제공자 등록 API 역할
- DriverManager.getConnction이 서비스 접근 API 역할
- DriverManager.getConnction이 유연한 정적팩터리 의 실체이다.



### 단점
1. 상속을 하려면 public이나 proteced 생성자가 필요하니, 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.

2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

