---
layout: post
title: 소수점 몇자리 까지 지원할거냐
date: 2022-08-02 20:00
tags: bigdecimal double 부동소수점
description: 소수점 몇자리 까지 지원할거냐
---

### 화폐 계산에 대하여 처리할때,,

- 코인 관련 프로젝트를 하다 보니, 코인의 갯수를 메모리(변수)에 담아서 연산을 처리해야할때가 있다.
- 그런데, fe(react) 에서도 그렇고, be(spring) 에서도 그렇고 js의 number, java double, mysql double 타입으로 커버되지 않는 문제가 생겼다.
- 프로젝트 시작하기 전에 알았더라면...

### 부동소수점에 대하여

- 0.3을 부동소수점 표현식으로 표현하면, 무한 반복 이다. (https://youtu.be/8afbTaA-gOQ)
- 그러다 보니, 소수점 연산하는 과정에서 정확하지 않은 값이 저장될수 있다.
- 화폐가 아닌, 하나에 몇천만원하는 코인에 대해서 연산을 하다보면 소수점은 불가피한데, 연산할때마다 몇천원씩 차이가 난다면, 문제가 생길것이다.
- 실제로 각 블록체인 내부 연산에서는 가장 작은 단위를 사용하여, 실수 연산이 아닌 단순 정수 연산을 진행한다고 한다.
- 우리는 3rd party 클라이언트를 개발하고 있으니,, 여러 코인을 처리할수 있는 로직을 구현해야한다.

### 각 언어별 최대 크기

- js는 number type은 64bit
- java double도 64bit
- mysql doulbe(부동소수점) 방식은 64bit

### HALF_EVNE

- 버그를 하나 받았는데, 내용은, 다음과 같다.
  - 0.005 로 조회가 되는데, 화면에서 소수점 2번째 자리까지 반올림해서 보여주니깐, 0.01이여야 하는데, 0 이 나온다.
- 다음과 같이 되어있다.

```java
 BigDecimal(amount).setScale(2, RoundingMode.HALF_EVEN);
```

- HALF_EVEN을 살펴보니, 올림 대상 자리수가 5일 경우, 대상 수 앞이 짝수면 내림, 홀수면 올림을 한다.
- 요구사항에 맞지 않게, 라운딩 방식을 잘못 사용하고 있던것이여서, HALF_UP 방식으로 변경하였다.
- 은행권 친구에게 물어보니, 은행 코어 계산에서는 모두 HALF_EVEN 방식으로 계산한다고 한다.
- 그 이유는, Banker`s rounding 때문. 아래 링크 참고
  - decimal HALF_EVEN : https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/math/RoundingMode.html#HALF_EVEN
  - Banker`s rounding : https://www.freeism.co.kr/wp/archives/1792

### 결론

- 프로젝트 진행하기전에, 블록체인 별로, 소수점 몇자리까지 허용할것인지 정하는게 좋을거 같다.
- bigdecimal을 사용한다.
- bigdecimal은 IEEE 754-2008에 의해 표준화된 형식을 지원한다. 128bit
- fe에서는 js-big-decimal 혹은 bignumber.js , java에서는 BigDecimal, mysql은 decimal

#### 참고

- 컴퓨터가 부동소수점으로 수를 표현하는 방식 : https://youtu.be/8afbTaA-gOQ
- 코인 연산 관련 : https://steemit.com/kr/@modolee/floating-point
- bigdecimal의 maximum? : https://stackoverflow.com/questions/6791970/how-to-get-biggest-bigdecimal-value
- bigDecimal : https://jsonobject.tistory.com/466
