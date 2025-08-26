---
title: "Spring Data JPA에서 새로운 Entity인지 판단하는 방법"
date: 2025-01-03T07:51:28.431Z
tags: ["Java","Spring","백엔드"]
slug: "Spring-Data-JPA에서-새로운-Entity인지-판단하는-방법"
image: "../assets/posts/2f7f4d03d210e443c2d08c7b7a1b2657a8649011113852efd316015c59aca8a1.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:58.188Z
  hash: "fd966592333f1cccbfb6c8eddde850ecd61471168ac9b6474642c217b7bda0e4"
---

```java
@Override
public boolean isNew(T entity) {

    if(versionAttribute.isEmpty()) 
          || versionAttribute.map(Attribute::getJavaType).map(Class::isPrimitive).orElse(false)) {
        return super.isNew(entity);
    }

    BeanWrapper wrapper = new DirectFieldAccessFallbackBeanWrapper(entity);

    return versionAttribute.map(it -> wrapper.getPropertyValue(it.getName()) == null).orElse(true);
}
```

새로운 엔티티인지 여부는 JpaEntityInformation의 **`isNew(T entity)`** 에 의해 판단된다. 다른 설정이 없다면 JpaMetaModelEntityInformation 클래스가 동작한다. **`@Version`** 이 사용된 필드가 없거나 **`Version`** 이 사용된 필드가 primitive타입이면 AbstractEntityInformation의 **`isNew()`** 를 호출한다. **`@Version`** 이 사용된 필드가 wrapper class면 `null` 여부를 확인한다.

```java
public boolean isNew(T entity) {

    Id id = getId(entity);
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
**`@Version`** 이 사용된 필드가 없어서 AbstractEntityInformation 클래스가 동작하면 **`@Id`** 어노테이션을 사용한 필드를 확인해서 primitive 타입이 아니라면 null 여부, Number의 하위 타입이면 0인지 여부를 확인한다. **`@GenerateValue`** 어노테이션으로 키 생성 전략을 사용하면 데이터베이스에 저장될 때 id가 할당된다. 따라서 데이터베이스에 저장되기 전에 메모리에서 생성된 객체는 id가 비어있기 때문에 **`isNew()`** 는 true가 되어 새로운 entity로 판단한다.

### 직접 ID를 할당하는 경우에는?
키 생성 전략을 사용하지 않고 직접 ID를 할당하는 경우 새로운 entity로 간주되지 않는다. 이 때는 entity에서 **`Persistable<T>`** 인터페이스를 구현해서 JpaMetamodelEntityInformation 클래스가 아닌 JpaPersistableEntityInformation의 **`isNew()`** 가 동작하도록 해야한다.

```java
public class JpaPersistableEntityInformation<T extends Persistable<ID, ID> 
        extends JpaMetamodelEntityInformation<T, ID> {

    public JpaPersistableEntityInformation(Class<T> domainClass, Metamodel metamodel, 
            PersistenceUnitUtil persistenceUnitUtil) {
        super(domainClass, metamodel, persistenceUnitUtil);
    }

    @Override
    public boolean isNew(T entity) {
        return entity.isNew();
    }

    @Nullable
    @Override
    public ID getId(T entity) {
        return entity.getId();
    }
}
```

### 새로운 Entity인지 판단하는것이 왜 중요할까?
```java
@Override
@Transactional
public <S extends T> S save(S entity) {

    Assert.notNull(entity, "Entity must not be null");

	if (entityInformation.isNew(entity)) {
		entityManager.persist(entity);
		return entity;
	} else {
		return entityManager.merge(entity);
	}
}
```

[SimpleJpaRepository 의 **`save`** 메서드](https://umanking.github.io/2019/04/12/jpa-persist-merge/)에서 **`isNew()`** 를 사용하여 persist를 수행할지 merge를 수행할지 결정하기 때문이다. 만약 ID를 직접 지정해주는 경우에는 신규 entity라고 판단하지 않기 때문에 merge를 수행한다. 이때 해당 entity는 새로운 entity임에도 불구하고 DB를 조회하기 때문에 비효율적이다. 그렇기 때문에 새 entity인지 판단하는것이 중요하다.

### References
- [save()시 식별자가 존재하는 경우 어떻게 동작할까?](https://ttl-blog.tistory.com/807)
- [Persistable - 새로운 엔티티 판별 여부 설정](https://ttl-blog.tistory.com/852)