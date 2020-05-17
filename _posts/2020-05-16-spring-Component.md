---
layout: post
title: "spring component"
date: 2020-05-16 15:30:00
tags: spring
description: spring component, component scan
---

# component scan
- @SpringBootApplication -> @ComponentScan
- @ComponentScan의 패키지 부터 scan 한다.

- @Filter : scan중 걸러낼것들

- @Component
    - @Repository
    - @Service
    - @Controller
    - @Configuration
