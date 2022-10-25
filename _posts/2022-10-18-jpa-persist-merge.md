---
layout: post
title: jpa persist merge
date: 2022-10-18 20:00
tags: db jpa persistanceContext
description:
---

### save(persist, merge)와 isNew

- save를 하면, jpa는 저장하려는 entity가 isNew 인지 확인하여, isNew라면 persist를 호출하고, 아니라면 merge를 진행한다.
- persist : 최초 생성된 entity를 영속화 한다.
- merge : 식별자값(id)로 1차 캐시에서 조회하고, 없으면 데이터베이스를 조회하고 1차 캐시에 저장한다. entity에 값을 채워 넣는데, 모든 값이 변경된다.
- isNew는 Persistable interface의 메소드이다.

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

- persist 와 merge의 설명

```java
    /**
     * Make an instance managed and persistent.
     * @param entity  entity instance
     * @throws EntityExistsException if the entity already exists.
     * (If the entity already exists, the <code>EntityExistsException</code> may
     * be thrown when the persist operation is invoked, or the
     * <code>EntityExistsException</code> or another <code>PersistenceException</code> may be
     * thrown at flush or commit time.)
     * @throws IllegalArgumentException if the instance is not an
     *         entity
     * @throws TransactionRequiredException if there is no transaction when
     *         invoked on a container-managed entity manager of that is of type
     *         <code>PersistenceContextType.TRANSACTION</code>
     */
    public void persist(Object entity);

    /**
     * Merge the state of the given entity into the
     * current persistence context.
     * @param entity  entity instance
     * @return the managed instance that the state was merged to
     * @throws IllegalArgumentException if instance is not an
     *         entity or is a removed entity
     * @throws TransactionRequiredException if there is no transaction when
     *         invoked on a container-managed entity manager of that is of type
     *         <code>PersistenceContextType.TRANSACTION</code>
     */
    public <T> T merge(T entity);
```

- Persistable을 구현하지 않으면, 기본적으로 AbstractEntityInformation의 isNew를 실행하게 된다.

```java
	public boolean isNew(T entity) {

		ID id = getId(entity);
		Class<ID> idType = getIdType();

		if (!idType.isPrimitive()) {
			return id == null;
		}

		if (id instanceof Number) {
			return ((Number) id).longValue() == 0L;
		}

		throw new IllegalArgumentException(String.format("Unsupported primitive id type %s", idType));
	}
```

- id를 가져와서, wrapper Type이고, id가 null이면 isNew라고 판단한다.
- primitive Number 이고, 0 이면 isNew라고 판단한다.

- 그렇기 때문에, isNew를 override하지 않았다면, id가 없을때 isNew라고 판단하고, persist를 호출한다.
- 반대로 Id가 존재할 경우 save했을때, isNew가 false가 되고 ,merge를 호출한다.
  <br/>
- 자주 있는 상황은 아니지만, 준영속 상태의 엔티티의 필드를 변경하고 save를 하게되면, merge가 호출된다.
- 이때 영속성 컨텍스트에 없는 엔티티 이므로, id 기준으로 db에서 조회해서, 준영속상태의 엔티티를 조회한 엔티티에 덮어씌워버린다.

### 정수형이 아닌 키와 isNew

- 복합키 또는 String의 키를 사용하게 되면, 키 생성 전략(auto_increment, using sequence 등등)을 사용할수 없다.
- 그렇기 때문에, 로직에서 복합키를 set 해주게되고, SimpleJpaRepository의 isNew 동작에서 ID가 null이 아니기 때문에, merge를 진행한다.
- merge 진행순서 : 영속성 컨텍스트에 있는지 확인 -> 영속성 컨텍스트에 없으면 select 후 merge, 트랜잭션 종료전 insert
- 즉, select -> insert가 발생하고. 필요없는 select 가 발생한다...
- 그러므로, isNew를 override 하여, 불필요한 select가 발생하지 않도록 해야한다.
- 결론 : system key(auto_increment) 시스템 키를 사용하자.
