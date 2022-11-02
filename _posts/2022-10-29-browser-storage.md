---
layout: post
title: browser storage 와 token 공유
date: 2022-10-29 14:00
tags: browser storage token
description:
---

### browser session storage와 local storage

- local storage 특징
  - 브라우저 닫고 켜도 유지됨
  - 도메인 별로 관리됨, 다른 도메인에서 접근 불가
  - 탭간 공유 가능
- session storage 특징
  - 브라우저 닫고 키면 사라짐
  - 브라우저 탭간 공유 불가능

### 탭간 session storage 공유하기

- token같은 정보를 sessino storage에 저장해 두었다면, 다른 탭에서는 해당 내용을 볼수 없으니, 로그인 상태도 유지할 수 없다.
- session storage에 token 생성/삭제 상태를 다른 탭에 공유하여야한다.
- eventListenr 중에 'storage' 이벤트가 존재하는데, local storage가 변경되면 발생하는 이벤트이다.
- 이 이벤트를 사용하여, token 삭제/생성 등 상태를 공유하여, 탭간 세션 공유를 구현할 수 있다. (publish / subscribe 동작)
- 그리고, local storage로 token 를 공유해야하므로, local storage로 전달하고, 바로 삭제해야, 보안상 문제가 발생하지 않는다.
