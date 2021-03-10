---
layout: post
title: 자바스크립트 eventloop
date: 2021-03-08 20:30:00
tags: javascript eventloop
description: 자바스크립트 동작 원리
---

# javascript는 실제로 어떻게 동작하지?

동영상 링크 : [what the heck is the eventloop](https://www.youtube.com/watch?v=8aGhZQkoFbQ&ab_channel=JSConf)

[로우 레벨로 살펴보는 Node.js 이벤트 루프](https://evan-moon.github.io/2019/08/01/nodejs-event-loop-workflow/)

- 크롬의 자바스크립트 엔진(V8) 런타임이 어떻게 동작하는지

### 메모리와 eventloop

- eventloop 는 ECMA혹은 에 정의된 내용이 아니다.
- eventloop 는 V8의 일부가 아니다.
- eventloop는 node, 브라우저가 담당하고 있다.
- event loop 덕분에, 자바스크립트는 싱글 스레드 event-driven 방식으로 동작하게 된다.

### nodejs 이벤트 루프 설명

[Node Document](https://nodejs.org/ko/docs/guides/event-loop-timers-and-nexttick/)

[javascript info](https://ko.javascript.info/event-loop)
