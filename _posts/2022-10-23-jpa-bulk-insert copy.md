---
layout: post
title: spring jpa save와 saveAll 그리고 batch insert(with transaction)
date: 2022-10-23 12:30
tags: db jpa saveAll
description:
---

### @Transactional 동작과정 with 프록시

- TransactionAspectSupport에서 빈으로 등록된 transacitonManger를 통해 begin, commit, rollback 한다. 이과정을 프록시를 통한 함수 호출 과정, 프록시 과정이라고 하자.
- 컨트롤러에서 @Transactinal이 붙은 함수를 호출하거나, save를 호출하면, 에서 프록시 과정을 통한다. save도 @Transactional이 붙어있다.
- 즉, 외부 호출을 통한 @Transactional 호출은 프록시 과정을 거친다.
- 하지만, this 에 존재하는 @Transactional 함수 호출은 프록시 과정을 거치지 않게 된다.

### save와 saveAll 차이

- 여기서 save, saveAll 둘다 @Transactional 이 붙어 있어, 외부에서 호출되었을 경우, 프록시 과정을 거치게된다.

```java
	@Transactional
	@Override
	public <S extends T> S save(S entity) {

		Assert.notNull(entity, "Entity must not be null.");

		if (entityInformation.isNew(entity)) {
			em.persist(entity);
			return entity;
		} else {
			return em.merge(entity);
		}
	}
```

```java
	@Transactional
	@Override
	public <S extends T> List<S> saveAll(Iterable<S> entities) {

		Assert.notNull(entities, "Entities must not be null!");

		List<S> result = new ArrayList<>();

		for (S entity : entities) {
			result.add(save(entity));
		}

		return result;
	}
```

- 만약에 service 에서 for문을 통해 여러 entity를 save 하게 되면, 매번 save 호출 시 마다, 프록시 과정을 통하지만,
- entity list를 만들어서, saveAll을 하게 되면, saveAll 호출 -> 내부 save 호출 이기 때문에, saveAll내부의 save 과정에서는 프록시 과정을 거치지 않게 된다.
- 이 과정에서 시간차이가 많이 나게 된다.

### batch insert

- hibernat.jdbc.batch_size 를 설정하면, 여러건의 insert를 모아서 한번에 보낼수 있다.

- 문제 상황, id 생성 전략을 Identity로 해놓았을 경우, batch insert 기능을 사용할 수 없다.
- 이유는, jpa는 쓰기 지연으로 동작한다.(write 작업을 모아두었다가, transaciton commit 전에 한번에 쓰는방법)
- 그러므로, auto_increment로 설정해놓으면, 실제 insert 하기전에는 해당 entity의 실제 id값을 알수가 없다.
- 그런 이유로, identity 전략으로 생성된 entity의 batch insert를 지원하지 않는다.

- [jpa의 identity id 생성 전략과 batch insert](https://stackoverflow.com/questions/27697810/why-does-hibernate-disable-insert-batching-when-using-an-identity-identifier-gen)
- [jpa의 table id 생성 전략과 transaction 문제](https://techblog.woowahan.com/2663)

### 참고. spring transactional 구조

- 가장 상위에 PlatformTransactionManager interface를 가지고 있다.
- PlatformTransactionManager를 구현한 AbstractPlatformTransactionManager 추상 클래스가 있고, datasource(직접 jdbc 사용), jpa, hibernate TransacitonManager들은 AbstractPlatformTransactionManager을 구현하고 있다.
- spirng boot autoconfigure의 JpaBaseConfiguration 에서 bean등록해주고 있다.

### 참고. spring 의 transactionManager

- @Transactional 메소드가 호출될때, TransactionAspectSupport의 determineTransactionManager에서 프로젝트에 등록된 TransactionManager 빈을 가져와서 처리한다.

```java
protected TransactionManager determineTransactionManager(@Nullable TransactionAttribute txAttr) {
  ...
		else {
			TransactionManager defaultTransactionManager = getTransactionManager();
			if (defaultTransactionManager == null) {
				defaultTransactionManager = this.transactionManagerCache.get(DEFAULT_TRANSACTION_MANAGER_KEY);
				if (defaultTransactionManager == null) {
					defaultTransactionManager = this.beanFactory.getBean(TransactionManager.class);
  ...
	}
```

### spring transactional 새로운 트랜잭션 만들기

- @Transactional propagation attribute 을 통해 부모의 트랜잭션에 합류할건지, 새로운 트랜잭션을 만들건지 선택할 수 있다.
- TransactionAspectSupport 에서 attribute를 확인하여, 새로운 트랙잭션을 만들지 결정한다.
