---
layout: post
title: docker jenkins 구동
date: 2020-03-29 19:30:00
tags: docker jenkins
description: 도커 젠킨스 구동
---

### jenkins image 받아오기

~~~
docker pull jenkins/jenkins
~~~

~~~
docker run -d -p 8181:8080 -v jyjenkins:/var/jenkins_home --name jyjenkins -u root jenkins/jenkins
~~~
- d : 컨테이너 백그라운드 실행
- p : 로컬 pc와 docker 포트 설정 , localIp:dockerIp
- v : file sharing
- name : 이름 설정

<image src = "/images/스크린샷 2020-03-29 오후 7.49.17.png"></image>
- docker 화면에서 확인 가능하다.



<image src = "/images/스크린샷 2020-03-29 오후 7.51.32.png"></image>



## ngrok
- 젠킨스 외부 ip 가질수 있게 설정

