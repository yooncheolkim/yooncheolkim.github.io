---
layout: post
title: 정적 파일 압축 전송
date: 2023-02-08 13:33
tags: webserver gzip frontend
description: frontend 정적 파일 전송시 압축 전송
---

### 문제 상황
- 프로젝트에서 vuejs를 사용하여, 개발하였고, vue-cli(webpack)을 사용하여, 빌드하여, 빌드 결과물을 webserver(Oracle HTTP Server)로 배포하는 상황이였다.
- vue-cli로 빌드 결과로 나온 chunk-vendors.js 파일의 용량이 11mb가 넘었고, 네트워크 상황이 좋지 못한 사용자들은 브라우저에서 첫 페이지 노출까지 1분이상이 걸리게 되었다.
- 인프라 아키텍트와 협의하여, chunk-vendors.js gzip으로 압축하여 배포하는 것으로 결정하였고, 상당한 시간이 감소되는것을 확인하였다.

### as-is
- chunk-vendors.js
    - 사이즈 : 11mb
    - 로딩 타임 : 1분 24초

### gzip 압축 배포 결과
- chunk-vendors.js
    - 사이즈 : 2.3mb
    - 로딩 타임 : 4.24초


### 고려사항 및 결과
- 인프라 아키텍트와 협의하면서, 압축 배포하게되면, 웹서버의 cpu 사용량이 증가하기 때문에, 꼭 필요한 부분에만 적용하여 압축하는것이 좋다고 한다.
- 그리고 gzip 을 지원하는 브라우저(크롬)을 대상으로 서비스를 구축하고 있어서, 문제 없었지만, gzip 지원 브라우저를 꼭 확인해야한다.

### 이후 알아볼것
- 코드 스플리팅 : spa 초기 로딩시간 극복을 위함.
- chunkHash
- 청크 관리
