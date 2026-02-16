---
title: "Eaget,Lazy loading"
date: 2022-03-03 00:00:00 +0900
categories: [JPA]
tags: [TIL]
description: "Eaget,Lazy loading의 핵심 개념과 실무 포인트를 정리한 학습 노트입니다."
author: bright-flare
---
## 한눈에 보기
- Eager, Lazy loading
- 즉시 로딩
- 실행해보기

## Eager, Lazy loading

> **즉시로딩 (Eager) : 엔티티를 조회할 때 연관된 엔티티도 함께 조회한다.**

> **지연로딩 (Lazy) : 연관된 엔티티를 실제 사용할 때 조회한다. 처음 조회할 때에는 단일 Entity만 조회하지만 그 Entity를 실제로 사용할 때 연관된 Entity관련 Query가 발생한다.**

## 즉시 로딩

```java
@Entity
public class Member{

	@Id
	@GeneratedValue
	@Column(name = "member_id")
	private Long id;

	private int age;

	private String name;

	@ManyToOne(fetch = FetchType.EAGER) // 즉시 로딩
	@JoinColumn(name = "team_id")
	private Team team;

    // ...생략
}
```

### 실행해보기
```java
Team team = new Team("team !");
entityManager.persist(team);

Member member = new Member("sseob");
member.setCreatedBy("심현섭");
member.setCreatedDate(LocalDateTime.now());
member.setTeam(team);
entityManager.persist(member);

entityManager.flush();
entityManager.clear();

// Fetch전략이 즉시로딩일 경우 join하여 즉시 로딩한다.
Member m = entityManager.find(Member.class, member.getId());
```

- 실행 query
```sql
    select
        member0_.member_id as member_i1_9_0_,
        -- ..생략
        team1_.team_id as team_id1_14_1_,
        team1_.createdBy as createdb2_14_1_,
        team1_.createdDate as createdd3_14_1_,
        team1_.lastModifiedBy as lastmodi4_14_1_,
        team1_.lastModifiedDate as lastmodi5_14_1_,
        team1_.name as name6_14_1_
    from
        Member member0_
    left outer join
        Team team1_
            on member0_.team_id=team1_.team_id
    where
        member0_.member_id=?
```

1. 위와같은 코드가 있다고 해보자. Member Entity와 Team Entity는 `@ManyToOne`으로 연관관계가 설정되어 있다.
2. `@ManyToOne`은 Fetch Type이 기본적으로 Eager이다.
3. Fetch Type이 Eager이기 때문에 `m.getTeam()` 이러한 Team Entity관련 메소드를 호출하지 않더라도 Team을 join하여 결과값을 가져온다.

### 즉시 로딩의 단점

> **즉시 로딩은 미리 결과값을 join하여 가져오기 때문에 성능상 이점을 기대하며 사용할 수 있다.**

> **하지만, 즉시 로딩을 적용하면 예상하지 못하는 SQL query가 발생할 수 있어 주의가 필요하다. `JPQL`과 같이 사용할 경우 `N + 1` 문제가 발생한다.**

- `N + 1` 문제
- 아래와 같이 JPQL을 통해 조회를 하게될 경우.
```java
List<Member> resultList = entityManager.createQuery("select m from Member m ", Member.class).getResultList();
```

- SQL query문 확인
```sql
Hibernate:
    /* select
        m
    from
    Member m  */ select
        member0_.member_id as member_i1_9_,
        member0_.createdBy as createdb2_9_,
        member0_.createdDate as createdd3_9_,
        member0_.lastModifiedBy as lastmodi4_9_,
        member0_.lastModifiedDate as lastmodi5_9_,
        -- ...생략
    from
        Member member0_

Hibernate:
    select
        team0_.team_id as team_id1_14_0_,
        team0_.createdBy as createdb2_14_0_,
        team0_.createdDate as createdd3_14_0_,
        team0_.lastModifiedBy as lastmodi4_14_0_,
        team0_.lastModifiedDate as lastmodi5_14_0_,
        team0_.name as name6_14_0_
    from
        Team team0_
    where
        team0_.team_id=?
```

- 즉시 로딩으로 설정되어 있지 않았다면, JPQL을 통해 기대한 결과는 `"select * from Member; "` 하나의 SQL query이지만 즉시 로딩으로 설정 되어있기 때문에, 조회된 Member객체에서 비어있는 Team 객체 조회하기 위해 추가로 Team을 select하게 된다. `JPQL`로는 Member에 대한 결과값만 기대하도록 `JPQL`을 작성 하였기 때문에 추가로 Team에 대한 SQL query문이 발생한 것이다.

- Member가 여러명이면 해당하는 모든 Member에 대한 Team의 갯수만큼 N개의 query가 추가로 발생한다. Member에 대한 SQL query문 1개를 기대하고 실행 하였지만 모든 Member에 대한 N개의 Team을 조회하는 것을 `N + 1`문제라고 한다.

### N + 1 문제 해결
- `JPQL fetch join`을 통해 Team에 대한 결과값을 같이(join하여) 가져오도록 JPQL을 작성해야 한다.
- `FetchType`을 즉시 로딩이 아닌 지연 로딩을 사용한다. Team을 즉시 로딩하기 위한 동작을 회피하며, 실제 Team객체를 사용할 때에 Team을 조회한다.
- `persistence.xml` 설정 `batch_fetch_size`를 통해 `N + 1` query를 발생 시키는게 아니라 N개의 Team을 `where in`절 안에 넣어 `1 + 1` query를 발생 시킬 수 있다.
```xml
<property name="hibernate.default_batch_fetch_size" value="100"/>
```
```sql
    -- batch_fetch_size 설정 후 실행된 query문
    select
        team0_.team_id as team_id1_14_0_,
        team0_.createdBy as createdb2_14_0_,
        team0_.createdDate as createdd3_14_0_,
        team0_.lastModifiedBy as lastmodi4_14_0_,
        team0_.lastModifiedDate as lastmodi5_14_0_,
        team0_.name as name6_14_0_
    from
        Team team0_
    where
        team0_.team_id in (
            ?, ?
        )
```

## 지연 로딩

> **Team Entity와의 연관관계 설정을 그대로 두고 `FetchType`을 `Lazy`로 설정하면**

```java
@Entity
public class Member{

	@Id
	@GeneratedValue
	@Column(name = "member_id")
	private Long id;

	private int age;

	private String name;

	@ManyToOne(fetch = FetchType.LAZY) // 지연 로딩
	@JoinColumn(name = "team_id")
	private Team team;

    // ...생략
}
```

### 실행해보기
```java
Team team = new Team("team !");
entityManager.persist(team);

Member member = new Member("sseob");
member.setCreatedBy("심현섭");
member.setCreatedDate(LocalDateTime.now());
member.setTeam(team);
entityManager.persist(member);

entityManager.flush();
entityManager.clear();

Member m = entityManager.find(Member.class, member.getId());
System.out.println("m.getTeam().getClass() = " + m.getTeam().getClass());
```

- query 확인
```sql
    select
        member0_.member_id as member_i1_9_0_,
        member0_.createdBy as createdb2_9_0_,
        member0_.createdDate as createdd3_9_0_,
        member0_.lastModifiedBy as lastmodi4_9_0_,
        member0_.lastModifiedDate as lastmodi5_9_0_,
        -- ...생략
    from
        Member member0_
    where
        member0_.member_id=?

    m.getTeam().getClass() = me.sseob.jpa.practice.basic.Team$HibernateProxy$l5MmBI0U
```

- 즉시 로딩과는 달리 Team을 join한 query가 발생하지 않는다.
- 그리고 아래와 같이 Team객체를 얻어 print해보면 `Proxy` 객체가 조회 되는 것을 확인할 수 있다.

### 지연 로딩 확인하기

> **`m.getTeam().getClass()` 만 print를 할 경우 지연 로딩에 의한 SQL query문을 확인할 수 없다.**

> **실제 Team 객체를 사용해보자.**

```java
Member m = entityManager.find(Member.class, member.getId());
System.out.println("m.getTeam().getClass() = " + m.getTeam().getClass());
System.out.println("m.getTeam().getName() = " + m.getTeam().getName());
```

### 결과
```sql

m.getTeam().getClass() = me.sseob.jpa.practice.basic.Team$HibernateProxy$eXEaa5QH

Hibernate:
    select
        team0_.team_id as team_id1_14_0_,
        team0_.createdBy as createdb2_14_0_,
        team0_.createdDate as createdd3_14_0_,
        team0_.lastModifiedBy as lastmodi4_14_0_,
        team0_.lastModifiedDate as lastmodi5_14_0_,
        team0_.name as name6_14_0_
    from
        Team team0_
    where
        team0_.team_id=?

m.getTeam().getName() = team !
```
- `hibernate proxy class`가 print된 후, `m.getTeam().getName()` 메소드가 실행되기 직전, select query가 실행된것을 확인할 수 있다. (말 그대로 `지연` 로딩)
- 즉, 지연 로딩일 때에 Team객체를 `Proxy` 객체로 제공하며 Team객체를 실제 사용할 때에 DB에서 조회하여 Team 객체를 제공한다. 그리고 `Proxy` 객체가 실제로 사용될 때, `Proxy` 객체는 `target(실제 객체의 참조)`을 통해 실제 Entity의 method를 호출하게 되는데 영속성 컨텍스트에 해당 Entity가 비어있게 되면 영속성 컨텍스트에 초기화 요청을 하게된다.

### 지연 로딩일땐 무조건 Proxy 객체 ?

> **연관관계 `FetchType`이 `Lazy`로 설정 되어있다고 무조건 `Proxy`객체로 해당 객체를 제공받는것은 아니다.**

### 실행해보기

> **이번엔 아래 두 메소드를 실행하지 않고 결과를 확인해 보자.**
> **`entityManager.flush();`**
> **`entityManager.clear();`**

```java
Team team = new Team("team !");
entityManager.persist(team);

Member member = new Member("sseob");
member.setCreatedBy("심현섭");
member.setCreatedDate(LocalDateTime.now());
member.setTeam(team);
entityManager.persist(member);

// entityManager.flush();
// entityManager.clear();

Member m = entityManager.find(Member.class, member.getId());

System.out.println(m.getTeam().getClass());
System.out.println("m.getTeam().getName() = " + m.getTeam().getName());
```

### 결과
```
class me.sseob.jpa.practice.basic.Team
m.getTeam().getName() = team !
```

### 왜 Proxy 객체가 아닐까 ?

> **`EntityManager.flush()`, `clear() 간단 설명`**

> **`flush()`는 영속성 컨텍스트에 있는 데이터를 DB로 저장시킨다.**

> **`clear()`는 영속성 컨텍스트에 있는 데이터를 clear시킨다.**

- FetchType이 지연 로딩인데도 왜 `Proxy` 객체가 아닌 실제 Team객체가 print 되었을까 ?

1. `Proxy`객체는 영속성 컨텍스트에 해당 객체가 비어있을 때 얻게된다. 이미 영속성 컨텍스트에 얻고자하는 객체가 있다면 프록시 객체를 제공할 필요가 없기 때문이다.
    - 이전에는 `clear()`를 통해 영속성 컨텍스트를 비웠기 때문에 `Proxy`객체를 반환하였던 것이다.

2. 또한, 영속성 컨텍스트는 `영속화된 Entity의 동일성을 보장 (identity)`하기 때문에 Member와 Team을 `EntityManager.persist()`를 통해 먼저 영속화를 했으므로, `Proxy`객체가 아닌 실제 객체를 제공하는 것이다.
