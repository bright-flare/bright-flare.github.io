---
title: "Persistence context"
date: 2021-12-20 00:00:00 +0900
categories: [jpa]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: jpa/Persistence context.md"
---
## PersistenceContext (영속성 컨텍스트) 👀

- 영속성 컨텍스트는 눈에 보이지 않는 논리적인 개념이다.
- 엔티티를 영구 저장하는 환경이다.
- EntityManager를 통해서 영속성 컨텍스트에 접근할 수 있다.


### Entity 생명주기 💫
1. 비영속(new/transient)
    - Account Class가 존재한다고 가정했을 때 new Account();를 통해 객체를 생성한 상태. JPA와 전혀 관계가 없는 상태.
2. 영속(managed)
    - EntityManager의 persist(account); 를 통해서  객체를 영속성 컨텍스트에 저장한 상태.
    - 영속성 컨텍스트를 통해서 account객체가 관리되기 시작한다.
    - Database Insert Query가 실행되지 않음. Transaction commit이 발생하는 시점에 Query가 실행된다.
3. 준영속(detached)
    - 영속성 컨텍스트에 저장되었다가 분리된 상태.
    - EntityManger detach() 를 통해서 영속성 분리가 가능하다.
4. 삭제(remove)
    - 실제 Database에서 삭제한다.

### 영속성 컨텍스트의 이점

> **EntityManager를 통해서 영속성 컨텍스트에 접근할 수 있다.**

1. 1차캐시를 사용한다.
    - 엔티티 조회시 영속성 컨텍스트 내부 1차캐시에서 먼저 해당 객체를 @Id값으로 식별하여 조회한다. (조회 Query가 실행되지 않는다.)
    - 1차캐시에 해당 객체가 존재하지 않는다면 DB에서 해당 객체를 조회하며, 1차캐시에 저장한다. 이후 똑같은 객체를 한번 더 조회하게 되면 1차 캐시를 참조하게 된다.
    - 1차캐시는 Application전체에서 공유하는 캐시가 아니라 Transaction단위로 공유하는 캐시이다.

2. 영속화된 Entity의 동일성을 보장한다. (identity)
    - 같은 Transaction안에서 1차캐시로 읽어진 객체는 동일한 객체이다.
    ```java
        Account account = entityManager.find(Account.class, 1L);
        Account account2 = entityManager.find(Account.class, 1L);
        account == account2 // true
    ```

3. 쓰기 지연 sql 저장소 (transactional write-behind)
    - Entity를 저장, 수정, 삭제를 바로 실행하는게 아니라, Transaction이 commit되는 시점에 SQL query문을 실행시킨다.
    - persist() method로 Entity를 저장(영속화)하면 해당 Entity를 1차 캐시에 저장하고 query문을 쓰기 지연 sql 저장소에 저장 해놓는다.
    ```java
       entityManager.persist(accountA);  // 1차 캐시에 저장하고, insert query를 쓰기지연 sql 저장소에 저장 해놓는다.
       entityManager.persist(accountB);  // 1차 캐시에 저장하고, insert query를 쓰기지연 sql 저장소에 저장 해놓는다.
    ```
    - 이후에 Transaction이 commit되는 시점에 sql query문을 실제로 실행 시킨다. 이를 flush라고 한다.

4. 변경 감지 (dirty checking)
    1. Transaction commit이 이루어질 때 1차 캐시의 Entity와 Entity에 해당하는 스냅샷을 비교한다.
    2. Entity와 스냅샷과의 비교 이후에 변경사항이 존재하면 쓰기 지연 sql 저장소에 query문을 저장한다.
    3. flush가 이루어지면 저장해 놓았던 sql query문들을 Database에 실행시킨다. (Persistence context의 변경사항을 Database에 반영한다.)

5. 지연 로딩 (Lazy Loading)
    1. 

### Flush

> **영속성 컨텍스트의 변경사항을 Database에 반영한다.**

> **flush를 한다고 해서 영속성 컨텍스트를 비우는 것이 아니다.**

- 영속성 컨텍스트를 플러시하는 방법
    1. flush() method 호출
    2. Transaction commit할 때에 flush 자동 호출
    3. JPQL query 실행할 때에 flush 자동 호출.
