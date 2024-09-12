# 영속성 관리

## 목차

- [엔티티 매니저 팩토리와 엔티티 매니저](#엔티티-매니저-팩토리와-엔티티-매니저)
- [영속성 컨텍스트란?](#영속성-컨텍스트란)
- [엔티티 생명주기](#엔티티-생명주기)
- [영속성 컨텍스트의 특징](#영속성-컨텍스트의-특징)

## 엔티티 매니저 팩토리와 엔티티 매니저

**Entity Manager Factory**

- 엔티티 매니저를 만드는 공장으로 데이터베이스를 하나만 사용하는 애플리케이션은 일반적으로 하나만 생성

- 여러 스레드가 동시에 접근해도 안전하므로 서로 다른 스레드간에 공유 가능

**Entity Manager**

- 공장에서 엔티티 매니저를 생성하는 비용은 거의 들지 않으므로 필요할 때마다 엔티티 매니저 팩토리에서 생성됨

- 여러 스레드가 동시에 접근하면 동시성 문제가 발생하므로 스레드 간에 **절대** 공유하면 안됨

<img width="800" alt="엔티티 매니저 동작 구조" src="https://github.com/user-attachments/assets/97e1242c-27c2-445f-be6a-c0cbddd61351">

- 엔티티 매니저는 데이터베이스 연결이 꼭 필요한 시점까지 커넥션을 얻지 않음

## 영속성 컨텍스트란?

- 영속성 컨텍스트 (persistence context) → 엔티티를 영구 저장하는 환경

- 엔티티 매니저로 엔티티를 저장하거나 조회하면 엔티티 매니저는 영속성 컨텍스트에 엔티티를 보관하고 관리함

- em.persist(member) → 엔티티 매니저를 사용해서 회원 엔티티를 영속성 컨텍스트에 저장

## 엔티티 생명주기

<img width="800" alt="엔티티 생명 주기" src="https://github.com/user-attachments/assets/494f6e85-7b32-4bd7-958c-ef9974c16ba9">

### 비영속 (new/transient) : 영속성 컨텍스트와 전혀 관계가 없는 상태

- 엔티티 객체를 생성했다 → 아직 순수한 객체 상태이며 아직 저장하지 않았으므로 비영속 상태라 함

### 영속 (managed) : 영속성 컨텍스트에 저장된 상태

- 엔티티 매니저를 통해서 엔티티를 영속성 컨텍스트에 저장 → 영속성 컨텍스트가 관리하는 엔티티를 영속 상태

### 준영속 (detached) : 영속성 컨텍스트에 저장되었다가 분리된 상태

- 영속성 컨텍스트가 관리하던 영속 상태의 엔티티를 영속성 컨텍스트가 관리하지 않으면 준영속 상태

- 특정 엔티티를 준영속 상태로 만드려면 em.detach()를 호출

- em.close() 나 em.clear()를 호출해도 준영속 상태가 됨

### 삭제 (removed) : 삭제된 상태

- 엔티티를 영속성 컨텍스트와 데이터베이스에서 삭제

## 영속성 컨텍스트의 특징

- 영속성 컨텍스트와 식별자 값
    
    - 영속성 컨텍스트는 엔티티를 식별자 값(@Id로 매핑한 값)으로 구분
    
    - 영속 상태는 식별자 값이 반드시 있어야 한다.

- 영속성 컨텍스트와 데이터
    
    - JPA는 보통 트랜잭션을 커밋하는 순간 영속성 컨텍스트에 새로 저장된 엔티티를 데이터베이스에 반영 → `flush() 함수`

- 영속성 컨텍스트가 엔티티를 관리하면 장점
    
    - 1차 캐시
    
    - 동일성 보장
    
    - 트랜잭션을 지원하는 쓰기 지원
    
    - 변경 감지
    
    - 지연 로딩

### 엔티티 조회

- 1차 캐시
    
    - 영속성 컨텍스트는 내부에 캐시를 가지고 있음
    
    - 영속 상태의 엔티티는 모두 이곳에 저장
    
    - 1차 캐시의 키는 식별자 값
    
    - em.find()를 호출하면 먼저 1차 캐시에서 엔티티를 찾고 만약 찾는 엔티티가 1차 캐시에 없으면 데이터베이스에서 조회한다.

- 1차 캐시에서 조회
    
    <img width="698" alt="1차 캐시" src="https://github.com/user-attachments/assets/248072d4-7cb2-4252-a02e-42479d077797">
    
- 데이터베이스에서 조회
    
    - 만약 em.find()를 호출했는데 엔티티가 1차 캐시에 없으면 엔티티 매니저는 데이터베이스를 조회해서 엔티티를 생성한다. 그리고 1차 캐시에 저장한 후에 영속 상태의 엔티티를 반환
    
    <img width="822" alt="1차 캐시1" src="https://github.com/user-attachments/assets/1b5c43f5-c4b4-4a57-af1f-560b30e32d4a">
    
    1\. em.find(Member.class, "member2") 실행
    
    2\. member2 가 1차 캐시에 없으므로 데이터베이스에서 조회
    
    3\. 조회한 데이터로 member2 엔티티를 생성해 1차 캐시에 저장 (영속상태)
    
    4\. 조회한 엔티티를 반환

- 영속 엔티티의 동일성 보장
    
    ```java
    Memebr a = em.find(Member.class, "member1");
    Memebr b = em.find(Member.class, "member1");
    
    System.out.println(a == b); //동일성 비교
    ```
    
    여기서 a == b 는 참이다.
    
    - 1차 캐시에 있는 같은 엔티티 인스턴스를 반환하므로 같은 인스턴스 : TRUE
    - **영속성 컨텍스트는 성능상 이점과 엔티티의 동일성을 보장**
    
    **동일성과 동등성**
    
    - 동일성 : 실제 인스턴스가 같다. 참조 값을 비교하는 == 비교의 값이 같다.
    
    - 동등성 : 실제 인스턴스는 다를 수 있지만 인스턴스가 가지고 있는 값이 같다. equals() 메소드

### 엔티티 등록

```java
transaction.begin();

em.persist(memberA);
em.persist(memberB);
// 여기까지 데이터베이스에 INSERT 되지 않는다.

// 커밋하는 순간 데이터베이스에 INSERT
transaction.commit();
```

엔티티 매니저는 트랜잭션을 커밋하기 직전까지 데이터베이스에 저장하지 않고 내부 쿼리 저장소에 INSERT SQL을 모아둠

트랜잭션을 커밋할 때 모아둔 쿼리를 데이터베이스에 전송 → 쓰기 지연 ( transactional write-behind )

<img width="800" alt="엔티티 등록1" src="https://github.com/user-attachments/assets/ba9dac28-b6f7-4c02-ba95-8a23cc9a0d6d">

<img width="800" alt="엔티티 등록2" src="https://github.com/user-attachments/assets/9072b35a-1754-4db4-8bd9-37974d4c548d">  

  
💡 **flush : 영속성 컨텍스트의 변경 내용을 데이터베이스에 동기화 하는 작업**
  

### 엔티티 수정

**변경감지 : dirty chceking**

- 엔티티의 변경사항을 데이터베이스에 자동으로 반영하는 가능

<img width="798" alt="Untitled" src="https://github.com/user-attachments/assets/7a99b1db-3547-47fe-b4e5-b60066fd2b2c">

- 스냅샷 : JPA가 엔티티를 영속성 컨텍스트에 보관할 때 최초 상태를 복사해서 저장

1. 트랜잭션을 커밋하면 엔티티 매니저 내부에서 flush()가 호출

2. 엔티티와 스냅샷을 비교해서 변경된 엔티티를 찾음

3. 변경된 엔티티가 있으면 수정 쿼리를 생성해서 쓰기 지연 SQL저장소에 보냄

4. 쓰기 지연 저장소의 SQL을 데이터베이스에 보냄

5. 데이터베이스의 트랜잭션을 커밋

- **변경 감지는 영속성 컨텍스트가 관리하는 영속 상태의 엔티티에만 적용**

- JPA의 기본 전략은 엔티티의 모든 필드를 업데이트 함
    
    - 모든 필드를 사용하면 수정 쿼리가 항상 같음
    
    - 데이터베이스에 동일한 쿼리를 보내면 데이터베이스는 이전에 한 번 파싱된 쿼리를 재사용할 수 있음
    
    - 필드가 많거나 저장되는 내용이 너무 크면 확장 기능을 통해 동적으로 UPDATE SQL을 생성하는 전략 선택 → @org.hibernate.annotations.DynamicUpdate

### 엔티티 삭제

- 엔티티를 삭제하려면 먼저 삭제 대상 엔티티를 조회해야 함

```java
Member member = em.find(Member.class, "memberA");
em.remove(member);
```

### 플러시 (flush)

**플러시는 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영한다.**

1. 변경 감지가 동작해서 영속성 컨텍스트에 있는 모든 엔티티를 스냅샷과 비교해서 수정된 엔티티를 찾음

2. 수정된 엔티티는 수정 쿼리를 만들어 쓰기 지연 SQL 저장소에 등록

3. 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송

**플러시 하는 방법**

- em.flush() 를 직접 호출

- 트랜잭션 커밋 시 자동 호출

- JPQL 쿼리 실행시 플러시 자동 호출

```java
em.persist(memberA);
em.persist(memberB);
em.persist(memberC);

//중간에 JPQL 실행
query = em.createQuery("select m from Member m", Member.class);
List<Member> memberList = query.getResultList();
```

- em.persist() 호출해서 memberA, memberB, memberC 를 영속 상태로 만듬 → 데이터베이스에 반영 되기 전

- 이후 JPQL 을 실행하면 SQL로 변환하여 데이터베이스에서 엔티티를 조회 → 하지만 memberA, memberB, memberC 는 아직 데이터베이스에 반영되기 전이므로 쿼리 결과로 조회되지 않음

- 그렇기 때문에 JPA 는 이러한 문제를 예방하기 위해 **JPQL 실행 시 플러시를 자동 호출 함**

- 참고로 find() 호출 시에는 flush가 실행되지 않음

**플러시 모드 옵션**

- 엔티티 매니저에 플러시 모드를 직접 지정하려면 javax.persistence.FlushModeType 을 사용

    - FlushModeType.AUTO : 커밋이나 쿼리를 실행할 때 flush (기본값)

    - FlushModeType.COMMIT : 커밋할 때만 flush

- 플러시라는 이름으로 인해 영속성 커넥스트에 보관된 엔티티를 지운다고 생각하면 안된다 → 동기화 

### 준영속

- 영속성 컨텍스트가 관리하는 영속 상태의 엔티티가 영속성 컨텍스트에서 분리된것을 준영속 상태

- **준영속 상태의 엔티티는 영속성 컨텍스트가 제공하는 기능을 사용할 수 없다.**

- **엔티티를 준영속 상태로 만드는 방법**

    - em.detach(entity) : 특정 엔티티만 준영속 상태로 전환

    - em.clear() : 영속성 컨텍스트를 완전히 초기화 한다.

    - em.close(): 영속성 컨텍스트를 종료한다.

#### detach

```java
public void testDeatached() {
	Member member = new Member();
	member.setId("memberA");
	member.setUsername("회원A");

	em.persist(member);

	em.detach(member);
	
	transaction.commit();
}
```

<img width="800" alt="detach" src="https://github.com/user-attachments/assets/575cc151-f03a-4768-92f2-fc5023441b05">

#### clear

- em.clear()는 영속성 컨텍스트를 초기화해서 해당 영속성 컨텍스트의 모든 엔티티를 준영속 상태로 만든다.

```java
// 영속상태
Memebr memebr = em.find(Member.class, "memberA");

// 영속성 컨텍스트 초기화
em.clear();

// 준영속 상태 --> 변경 감지가 동작하지 않는다.
member.setUserName("changeName");
```

<img width="800" alt="영속성 컨텍스트 초기화 전" src="https://github.com/user-attachments/assets/f649d47b-e635-43d1-a3e3-60f59f92f411">

<img width="800" alt="영속성 컨텍스트 초기화 후" src="https://github.com/user-attachments/assets/6b57bb8b-84da-4f9f-a683-9a25c9b8cd1f">

#### close

```java
public void closeEntityManager() {
	
	EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpa");
	EntityManager em = emf.createEntityManager();
	EntityTransaction transaction = em.getTransaction();

	transaction.begin();
	
	Member memberA = em.find(Member.class, "memberA");
	Member memberB = em.find(Member.class, "memberB");

	trasaction.commit();
	
	em.close();
}
```

<img width="800" alt="영속성 컨텍스트 제거 전" src="https://github.com/user-attachments/assets/d76b2601-39f5-443b-b005-86f7aef0d6b8">

<img width="800" alt="영속성 컨텍스트 제거 후" src="https://github.com/user-attachments/assets/90682147-f567-4fdd-954c-30f4a448019d">

#### 준영속 상태의 특징

- 거의 비영속 상태에 가깝다
    
    - 영속성 컨텍스트가 관리하지 않으므로 1차 캐시, 쓰기 지연, 변경 감지, 지연 로딩 등 어떠한 기능도 동작하지 않는다.

- 식별자 값을 가지고 있다.
    
    - 식별자 값이 없을 수도 있지만 준영속 상태는 이미 한 번 영속 상태였으므로 식별자 값을 가지고 있다.

- 지연 로딩 할 수 없다.
    
    - 지연로딩(lazy loading) 은 실제 객체 대신 프록시 객체를 로딩해두고 해당 객체를 사용할 때 영속성 컨텍스트를 통해 데이터를 불러오는 방법

#### 병합 : merge()

- 준영속 상태의 엔티티를 다시 영속 상태로 변경하려면 병합을 사용

- merge()는 준영속 상태의 엔티티를 받아서 그 정보로 새로운 영속 상태의 엔티티를 반환

```java
Member mergeMember = em.merge(member);
```

```java
package org.example.CH02;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class MergeMain {

    static EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpa");

    public static void main(String[] args) {
        Member member = createMember("memberA", "회원1", 10);

        // 준영속 상태에서 변경
        member.setUsername("회원명변경");

        mergeMember(member);
    }

    static Member createMember(String id, String username, Integer age) {
        EntityManager em1 = emf.createEntityManager();
        EntityTransaction tx1 = em1.getTransaction();

        tx1.begin();

        Member member = new Member();
        member.setId(id);
        member.setUsername(username);
        member.setAge(age);

        em1.persist(member);
        tx1.commit();

        // member 는 준영속 상태가 된다.
        em1.close();

        return member;
    }

    static void mergeMember(Member member) {
        EntityManager em2 = emf.createEntityManager();
        EntityTransaction tx2 = em2.getTransaction();

        tx2.begin();
        Member mergeMember = em2.merge(member);
        tx2.commit();

        // 준영속 상태
        System.out.println("member = "+ member.getUsername());
        // 영속 상태
        System.out.println("mergeMember = "+ member.getUsername());

				// 파라미터로 넘어온 엔티티는 병합 후에도 준영속 상태로 남아있다.
        System.out.println("em2 contains member = "+ em2.contains(member));
        System.out.println("em2 contains mergeMember = "+ em2.contains(mergeMember));

        em2.close();
    }

}
```

<img width="800" alt="준영속 병합" src="https://github.com/user-attachments/assets/6966cca6-7b12-4243-a6c0-26e865f73fb3">

1. merge() 실행

2. 1차 캐시에서 엔티티를 조회
    
    - 만약 1차 캐시에 엔티티가 없으면 DB에서 조회 후 1차 캐시에 저장

3. 조회한 영속 엔티티(mergeMember)에 member 엔티티의 값을 채워넣는다 → 이때 이름이 변경됨

4. mergeMember 를 반환

5. 커밋을 호출하여 데이터베이스에 반영됨

**비영속 병합**

- 병합은 비영속 엔티티도 영속 상태로 만들수 있음

```java
Member member = new Member();
Memebr newMember = em.merge(member);
tx.commit();
```

- 파라미터로 넘어온 엔티티의 식별자 값으로 영속성 컨텍스트를 조회하고 찾는 엔티티가 없으면 데이터베이스에서 조회

- 만약 데이터베이스에서도 발견하지 못하면 새로운 엔티티를 생성해서 병합함

- save or update

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]
