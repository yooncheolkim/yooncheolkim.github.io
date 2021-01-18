---
layout: post
title: 자바스크립트 프로퍼티
date: 2021-01-18 19:30:00
tags: javascript property prototype
description: 자바스크립트 프로퍼티에 대한 고찰
---

# 자바스크립트 [Prototype]

- 함수 생성자로 만들어진, 객체는 모두 **proto** 를 가지고 있다.
- **proto** 는 부모객체(프로토타입)을 참조하고 있다.
- 🔴 **proto** 는 legacy, 개발자가 직접 참조하여 사용하는거는 지양해야한다.

```js
function Yoon() {
  console.log("aa");
}

console.dir(a);
```

# 자바스크립트 함수 생성자의 prototype

```js
function Yoon() {
  console.log("aa");
}

console.dir(Yoon);
```

- 함수 생성자의 속성중 prototype이 있다.
- 이 prototype 은 함수 생성자로 만들어진, 객체의 **proto** 와 같은곳을 참조하고 있다.
- Yoon.prototype

# 자바스크립트 룩업 방식

- 자바스크립트는 객체의 속성을 찾는 방법이 있다.

1. 현재 객체 내부에서 찾는다.
2. 없으면, **proto** 가 참조하는 객체로 간다.
3. **proto** 가 참조하는 객체에서 찾는다.

- 이런 방식으로 객체의 상속/오버라이드를 구현할 수 있다.

```js
var a = {
  method1: function() {
    return "a1";
  }
};

var b = {
  __proto__: a,
  method2: function() {
    return "b1";
  }
};

var c = {
  __proto__: b,
  method3: function() {
    return "c3";
  }
};

a.method1();
c.method1();
```
