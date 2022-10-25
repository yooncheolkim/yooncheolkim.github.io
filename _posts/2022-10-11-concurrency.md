---
layout: post
title: db 동시성 처리
date: 2022-10-11 20:00
tags: db concurrency isolation
description:
---

### concurrency

- db에 데이터(특정 데이터)의 접근을 어떻게 처리할것인가?
- 수 많은 요청에 대해서, transaction을 생성 처리 하는 과정에서 어떻게 데이터가 유효한지 확인할 수 있을까?

#### 문제 상황

- account의 balance가 10 이라고 하자
- account의 명의가 두명(A,B) 라고 하면

1. A가 계좌에 10이 남아있어서 10을 withdraw 요청을 함.
2. B도 동시에(거의 같은시간에) 요청을 함
   3-1. A 요청 처리, validateion(10 > balance) OK
   3-2. B 요청 처리, validation(10 > balance) OK
   4-1. A 요청 update(balance = balance - 10) 커밋
   4-2. B 요청 update(balance = balance - 10) 커밋

- 이렇게 진행되면, balance는 -10이 됨. 말이 안된다...

#### db isolation level

- 참고 : https://zzang9ha.tistory.com/381

- read uncommitted : 다른 트랜잭션에서 commit 되지 않는 내용도 읽을 수 있음
- read committed : 다른 트랜잭션에서 commit 된 내용만 읽을 수 있음
- repeatable read : 다른 트랜잭션에서 commit 된 내용이 있어도, 처음 읽은 데이터가 유지됨
- serialize : 다른 트랜잭션의 데이터 접근을 막음.

- 실제 서비스에서는 serialize는 사용하지 않는다. 한 트랜잭션이 데이터를 점유하고 있게 되면, 다른 트랜잭션이 무한정 기다리게 되는 deadlock 문제가 발생하게된다.
- read committed/ repeatable read 에서도 문제상황과 같은 현상이 발생하게 될텐데 어떻게 처리할 수 있을까?

### Read committed에서 동시성 문제 해결 방법(lock)

#### 1. 비관적 lock

- select ... for update
- 다른 트랜잭션이 접근하지 못하게 한다. serialize와 같은 방법 문제가 많다.

#### 2. 낙관적 lock - update validation

- 낙관적 Lock은 application 단에서 수행하는 Lock 이다.
- 해당 방법은 비즈니스 요구사항에 따라 다르게 사용된다.
- 보통 update가 가능한지 확인하기 위해 validation을 진행하게 되고, update를 진행한다.
- 문제는 validation은 통과했지만, Update 시 다른 트랜잭션이 update하여, 한 트랜잭션에서 데이터의 동일성을 보장하지 못한다는것이다.
- 이때 update 시에도 validation을 진행하는것이다.

```sql
UPDATE account a
SET balance = a.balance - :amount
WHERE balance > :amount
```

- update 하는 동시에 유효한 값인지 확인하게 되면 동시성문제를 회피할 수 있다.
- read-committed 인 경우, update 하고, Tx가 커밋될때까지, 해당 Row는 lock이 걸리게 된다. 다른 tx에서 접근 불가
- jpa의 가장 큰 장점중 하나는, 쓰기지연이다. write 기능을 트랜잭션 종료전 마지막에 수행한다는것이다. 그러므로 Lock을 최소화해준다.

#### 3. 낙관적 lock - Version

- version등의 구분 컬럼을 이용해서 read - update 시, Read 했을때의 Version이 update 시의 version과 같은지 확인하는 절차를 진행한다.
- jpa에서는 @version 을 통해서 해당 낙관적 lock을 지원한다.
