---
layout: post
title: 자바스크립트 promise await async
date: 2021-03-01 22:30:00
tags: javascript promise await async
description: 자바스크립트 promise await async
---

# promise

### 콜백 예제

```javascript
setTimeout(() => {
  console.log("3초 걸림!");
}, 3000);
```

- 콜백함수 : 함수 인자로 넣어 해당 함수 안에서 실행되는 콜백(사용자가 넣어주는).
- 만약, 시작 시점으로 부터 1초후에 do something, 함수 끝나고 1초후에 do something, 함수끝나고 1초후에 do something 하고 싶다면?

```javascript
setTimeout(() => {
  console.log("1초 걸림!");
}, 1000);

setTimeout(() => {
  console.log("2초 걸림!");
}, 1000);

setTimeout(() => {
  console.log("3초 걸림!");
}, 1000);
```

- 이렇게 하면, setTimeout은 모두 비동기이기 때문에 1초후 모든 함수가 찍힌다.

```javascript
setTimeout(() => {
  //1
  console.log("1초 걸림!");
}, 1000);

setTimeout(() => {
  //2
  console.log("2초 걸림!");
}, 2000);

setTimeout(() => {
  //3
  console.log("3초 걸림!");
}, 3000);
```

- 이렇게 setTimeout의 시간을 바꿔서 할수도 있지만, 1번 후에 2번이 실행된다는 것을 보장하지 못한다.

```javascript
setTimeout(() => {
  setTimeout(() => {
    setTimeout(() => {
      console.log("3초 걸림!");
    }, 1000);
    console.log("2초 걸림!");
  }, 1000);
  console.log("1초 걸림!");
}, 1000);
```

- 이렇게 하면 각 콜백이 끝날때 마다 setTimeout을 호출해서 비동기 콜백을 등록하기 때문에, 문제없이 동작한다.
- 하지만, 이런 동작이 10개있다고 생각하면, 들여쓰기는 계속 들어가고, 코드 읽기도 굉장히 힘들어 진다. - callback hell

### promise로 문제해결

```javascript
///promise 를 return 하는 함수
function delayP(sec) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      return resolve(); // resolve를 return 하여 정상적으로 끝남.
    }, sec * 1);
  });
}

delayP(1) // promise를 리턴하는 함수 실행.
  .then(result => {
    //.then으로 실행한 결과를 가지고, 어떤일을 함.
    console.log("1초 걸림!");
    //다시 promise를 리턴
    return delayP(1);
  })
  //위에서 return 한 promise를 다시 받음.
  .then(result => {
    console.log("2초 걸림!");
  });
```

- promise.then을 사용하여, 비동기 연산을 순차적으로 진행하게 할수 있다.

### async await

```javascript
function myFunc() {
  return "func";
}
async function myAsync() {
  return "async";
}

console.log(myFunc()); // func
console.log(myAsync()); //promise(fulfilled:"async")
```

- functino 앞에 async를 붙여주면, promise를 리턴하는 함수로 바꿔준다.
- then을 사용할수 있는 promise를 리턴한다.
- new Promise 하서 resolve, reject처리할 필요 , return throw만으로 굉장히 편하게 Promise를 만들어낼 수 있다.

```javascript
myAsync().then(result => {
  console.log(result); // async
});
```

```javascript
async function myAsync() {
  delayP(3).then(time => {
    console.log(time);
  });
  return "async";
}
```

- 이렇게 하면 delayP 함수 호출과 동시에 비동기 이기 때문에 바로 return "async" 된다.
- 만약, 3초 기다렸다가 return 하고 싶다면?

```javascript
async function myAsync() {
  await delayP(3).then(time => {
    console.log(time);
  });
  return "async";
}
```

- async 함수에서는 await 사용할 수 있다.
- await를 기다렸다가 다음 return 을 실행한다.
- 일반 함수앞에 await를 붙여도 상관없다!
