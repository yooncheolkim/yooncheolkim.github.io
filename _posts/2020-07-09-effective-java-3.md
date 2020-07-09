---
layout: post
title: 이펙티브 자바 싱글턴 직렬화
date: 2020-07-09 21:00:00
tags: java
description: 이펙티브 자바 item 3
---

# private 생성자나 열거 타입으로 싱글턴임을 보증하라
- 클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다.
- 인터페이스를 구현해서 만든 싱글턴이 아니라면, mock 만들수 없기 때문이다.

## 첫번째 방법
~~~java
public class Elvis{
    public static final Elvis INSTANCE = new Elvis();
    private Elvis(){}
    public void leaveTheBuilding(){}
}
~~~
- 위 싱글턴 클래스는 딱한번 인스턴스 생성된다.
- 리플렉션을 사용하여 private 생성자를 호출할 수 있지만, 하나를 더 만드려고 할때 예외를 던지면 된다.

## 두번째 방법
~~~java
public class Elvis{
    private static final Elvis INSTANCE = new Elvis();
    private Elvis(){}
    public static Elvis getInstance(){return INSTANCE;}
}
~~~

- 무조건 getInstance를 사용하여, 접근하야여한다.
### 두번재 방법의 장점
- 마음이 바뀌면 API를 변경하지 않고 싱글턴이 아니게 변경할 수 있다.
- 유일한 인스턴스를 반환하던 팩터리 메서드가 스레드 별로 다른 인스턴스를 넘겨주게 할 수 있다.
- 정적 팩터리 메소드를 제네릭 싱글턴 팩터리로 만들수 있다.
- 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다. (Elvis::getInstance -> Supplier<Elvis>)

## 직렬화 한다면?
- 위 방식으로 만든 싱글턴을 직렬화 할때, Serializable을 구현하는건만으로는 안된다.
- 역직렬화 하는 순간, 초기화 해둔 인스턴스가 아닌 다른 인스턴스가 반환된다.
- 모든 인스턴스 필드를 transient로 선언하고 readResolve() 메서드를 제공해야한다.
- readResolve 메서드를 제공하여, 역직렬화 과정에서 만들어진 인스턴스를 반환하도록 한다.
- 이렇게 하지 않으면, 역직렬화할 때 마다 새로운 인스턴스가 생성된다.
~~~java
private Object readResolve(){
    return INSTANCE;
}
~~~

## 가장 간결한 싱글턴 방법 - enum
- 간결하지만, 부자연스러워서,, 잘 사용안하는듯...