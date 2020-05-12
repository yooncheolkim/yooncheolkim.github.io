---
layout: post
title: "롬복 기본 annotation 정리"
date: 2020-05-10 17:50:00
tags: lombok java
description: 롬복 기본 annotation 정리
---

# 생성자 관련

### @AllArgsConstructor
- 모든 멤버에 대한 생성자 생성

### @NoArgsConstructor
- 매개변수 없는 생성자 생성

### @RequiredArgsContructor
- 필요한 멤버만 지정할 수 있다. -> 멤버 위에 @NonNull 사용


# Getter / Setter

# Builder
- 빌더 패턴을 이용한 객체 조작 가능하게 해준다.
