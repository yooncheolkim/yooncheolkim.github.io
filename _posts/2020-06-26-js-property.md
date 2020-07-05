---
layout: post
title: 자바스크립트 프로퍼티
date: 2020-06-26 13:00:00
tags: javascript property prototype
description: 자바스크립트 프로퍼티에 대한 이야기
---

# 자바스크립트 프로퍼티
- 자바스크립트 함수 역시 객체다.
- 일반 객체와는 다르게 함수는 함수 객체만의 표준 프로퍼티를 가지고 있다.

## length, name, caller, arguments 프로퍼티
- length : 인자 갯수
- name : 함수 이름
- caller : 호출한 함수
- arguments : 전달된 인자값

## _proto_ 프로퍼티
- 모든 자바스크립트 객체는 자신의 프로토타입을 가리키는 Prototype 이라는 내부 프로퍼티를 가진다.
- 여기서 말하는 프로토타입은 부모 객체를 의미한다.
- 이 Prototype 프로퍼티가 바로 _proto_ 프로퍼티 이다.
- 이를 통해 자신의 부모 역할을 하는 프로토타입 객체를 가리킨다.
- ECMA에서는 함수 객체의 부모 역할을 하는 프로토타입 객체를 Function.prototype 객체라고 명명한다.

## prototype 프로퍼티
- prototype 프로퍼티와 _proto_ 프로퍼티를 혼동하면 안된다.
- prototype 프로퍼티는 함수가 만들어질때 만들어지고, constructor 프로퍼티 하나만 있는 객체를 가리킨다.
- 그리고 prototype 프로퍼티가 가리키는 프로토타입 객체의 유일한 프로퍼티인 constructor 프로퍼티는 자신과 연결된 함수를 가리킨다.
- 즉, add 함수의 prototype 프로퍼티 -> 프로토타입 객체
- 프로토타입 객체의 constructor -> add 함수



~~~javascript
fucntion myFunction(){
    return true;
}

console.dir(myFunction.prototype);
console.dir(myFunction.prototype.constructor);
~~~

~~~
Object
    constructor: ƒ myFunction()
    __proto__: Object
ƒ myFunction()
~~~
- myFunction이라는 함수가 만들어지면서, myFucntion 함수의 prototype 프로퍼티에는 함수와 연결된 프로토타입 객체가 생성된다.(myFucntion.prototype 객체)
- myFunction.prototype 은 myFunction() 함수의 프로토타입 객체를 의미한다.
- myFUnction.prototype의 constructor 는 myFunction을 가리키고 있다.


# 생성자 함수를 호출할때,,,
- 기존함수에 new 연산자를 붙여서 호출하면 해당 함수는 생성자 함수로 동작한다.
- 자바스크립트에서는 특정 함수를 생성자 함수로 사용하고 싶으면, 대문자로 시작하는 관례가 있다.
- 생성자 함수 코드 내부에서 this는 일반 메서드의 this 바인딩과 다르게 동작한다.

## 생성자 함수가 동작하는 방식
1. 빈 객체 생성 및 this 바인딩
- 생성자 함수가 실행되기전 빈 객체가 생성된다. 이 객체가 this로 바인딩 된다. 생성자 함수 내부에서 사용된 this는 이 빈 객체를 가리킨다.
- 생성된 빈 객체는 자신을 생성한 생성자 함수의 prototype 프로퍼티가 가리키는 객체를 자신의 프로토타입 객체로 설정한다.
2. this를 통한 프로퍼티 생성
- 이후에는 함수 코드 내부에서 this를 사용하여, 동적으로 프로퍼티 나 메서드 생성이 가능하다.
3. 생성된 객체 리턴
- this로 바인딩된 객체가 리턴된다. 


~~~javascript
var Person = function(name){
    //함수 코드 실행 전
    this.name = name;
    //함수리턴
}

var foo = new Person('foo');
console.log(foo.name);
~~~

- Person() 함수가 생성자로 호출되면, 함수 코드가 실행되기 전에 빈 객체가 생성되고, 빈 객체는 Person.prototype 객체를 __proto__ 가 Person.prototype을 가리키게 한다.
- 즉 Person.prototype 객체는 Person() 생성자를 사용하여 생성된 객체 모두가 __proto__ 으로 링크되어있다.


## 객체 리터럴 방식과 생성자 함수를 통한 객체 생성 방식의 차이
~~~javascript
var foo = {
    name:'foo',
    age : 35,
    gender: 'man'
};

var bar = new Person();
~~~

- 위 두방식의 차이는, __proto__(프로토타입 객체) 에 있다.
- 객체 리터럴 방식은 __proto__ 가 Object.Protytype 이고, 생성자 함수 방식은 Person.Protytype 이다.




# 프로토타입 체이닝
## 프로토 타입의 두가지 의미
- 자바스크립트는 클래스 개념이 없다.
- 생상된 객체의 부모 객체가 바로 프로토타입 객체다
- 상속 개념과 마찬가지로 자식 객체는 부모객체가 가진 프로퍼티 접근이나 메서드를 상속받아 호출하는것이 가능하다.

- 부모인 프로토타입 객체를 가리키는 참조링크 형태의 프로퍼티 : [[Prototype]] 프로퍼티 , __proto__
- 자바스크립트의 모든 객체는 자신을 생성한 생성자 함수의 prototype 프로퍼티가 가리키는 프로토타입 객체를 자신의 부모 객체로 설저하는 [[Prototype]] 링크로 연결한다.


## 프로토 타입 체이닝
- 해당 객체에 접근하려는 프로퍼티 또는 메서드가 없다면, [[Prototype]]링크(__proto__)를 따라 자신의 부모 역할을 하는 프로토타입 객체의 프로퍼티를 차례대로 검색한다.