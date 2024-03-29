---
layout: post
title: browser media 자동재생 정책
date: 2022-05-28 15:00
tags: browser media autoplay
description: browser media 자동재생 정책
---

### browser에서 media 자동재생 정책

- video/audio 태그에 autoplay 속성이 존재하여, 자동재생이 가능하도록 할 수 있다.
- 하지만, 많은 브라우저에서 autoplay 속성을 적용하면, 무조건 muted 속성도 같이 주어야 autoplay 가능하도록 정책상 막고 있다.
- 이유는 사용자가 특정 사이트에 들어왔을때, 사용자와의 interaction 없이 소리가 갑자기 나와 사용자를 깜짝 놀래키는것을 막기 위함이다.
- 크롬 : react에서 link를 타고 router 이동을 한다면, muted 속성 없이 autoplay가 동작한다. (해당 사이트에서 사용자의 interaction이 있다면, 자동재생을 허용하는듯)
- 사파리 : 사용자의 interaction이 있다 하더라도, muted 없이 autoplay는 불가능
- 엣지 : 크롬과 같다.

### 결론

- video/audio 태그에 autoplay를 사용하려면, muted를 함께 주자.
- 브라우저 정책을 위반하면서 까지 개발하지는 말자.

#### 참고

- https://mygumi.tistory.com/333
