# JPA 연관관계 매핑

## 목차
- [1. 단방향 연관관계](#단방향-연관관계)
- [2. 연관관계 사용](#연관관계-사용)
- [3. 양방향 연관관계](#양방향-연관관계)
- [4. 양방향 연관관계의 주의점](#양방향-연관관계의-주의점)

## 단방향 연관관계

### N : 1 (다 대 일)

- 팀원과 팀이 있을 때 팀원은 하나의 팀에만 소속되기에 N : 1 관계

![image](https://github.com/user-attachments/assets/9eaf4ce0-868c-4f72-b0b9-d2d7bdc3f05c)

- 객체 연관관계

    - 팀원 객체는 Member.team 팀 필드로 팀 객체와 연관관계이다.
 
    -  팀원과 팀은 `단방향` 관계 (팀원은 Member.team을 통해 팀을 알 수 있지만 팀은 팀원을 알 수 없기 때문)
 
- 테이블 연관관계

    - 팀원 테이블은 TEAM_ID(FK) 로 팀 테이블과 연관관계
 
    - 팀원 테이블과 팀 테이블은 `양방향` 관계 (Member Join Team 과 Team Join Member 의 결과는 같다)
 
- **객체 연관관계와 테이블 연관관계의 차이**

    - 참조를 통한 연관관계는 언제나 단방향이다.

    - 객체간에 연관관계를 양방향으로 만들고 싶은면 반대쪽에도 필드를 추가해서 참조를 보관해야 한다.
    
        → 정확하게 얘기하면 양방향 관계가 아니라 서로 다른 단방향 관계 2개
    
    - 반면 테이블은 외래 키 하나로 양방향으로 조인할 수 있다.
    
    - 객체는 참조(주소)로 연관관계 맺는다.
    
    - 테이블은 외래 키로 연관관계 맺는다.

- 예시 코드

    ```java
    // 패키지와 import 생략
    
    @Entity
    @Getter
    @Setter
    public class Member {

        @Id
        @Column(name = "MEMBER_ID")
        private String id;

        private String username;

        @ManyToOne
        @JoinColumn(name = "TEAM_ID")
        private Team team;

        public Member(String id, String username) {
            this.id = id;
            this.username = username;
        }

		public Member() {}
    }
    ```

    ```java
    @Entity
    @Getter
    @Setter
    public class Team {

        @Id
        @Column(name = "TEAM_ID")
        private String id;
    
        private String name;

        public Team(String id, String name) {
            this.id = id;
            this.name = name;
        }

		public Team() {}
    }
    ```

- @ManyToOne : N : 1

| 속성 | 기능 | 기본값 |
| --- | --- | --- |
| optional | false로 설정하면 연관된 엔티티가 항상 있어야 한다. | true |
| fetch | 글로벌 패치 전략을 설정 | @ManyToOne=FetchType.EAGER , @OneToMany=FetchType.LAZY |
| cascade | 영속성 전이 기능 사용 |  |
| targetEntity | 연관된 엔티티의 타입 정보 설정 (거의 사용하지 않음) |  |
- @JoinColumn(name=”TEAM_ID”) : 외래 키 매핑할 때 사용하며, 생략 가능

| 속성 | 기능 | 기본값 |
| --- | --- | --- |
| name | 매핑할 외래 키 이름 | 필드명_참조하는테이블의 기본키 컬럼명 |
| referencedColumnName | 외래 키가 참조하는 대상 테이블의 컬럼명 | 참조하는 테이블의 기본 키 컬럼명 |
| foreignKey(DDL) | 외래 키 제약조건을 직접 지정할 수 있다. |  |

### 생략시 기본 전략

- `필드명` + `_` + `참조하는 테이블의 컬럼명`
- team_TEAM_ID

## 연관관계 사용

- JPA 에서 엔티티를 저장할 때 연관된 모든 엔티티는 영속상태여야 함


### 저장

```java
public static void testSave(EntityManager em) {

    Team team1 = new Team("team1", "팀1");
    em.persist(team1);

    Member member1 = new Member("member1", "회원1");
    member1.setTeam(team1);
    em.persist(member1);

    Member member2 = new Member("member2", "회원2");
    member2.setTeam(team1);
    em.persist(member2);

}
```

```java
SELECT MEMBER_ID, M.USERNAME, M.TEAM_ID, T.NAME AS TEAM_NAME
FROM MEMBER M JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID
```

### 조회

- 객체 그래프 탐색
    - member.getTeam() 을 사용해서 조회
    
    ```java
    Member member = em.find(Member.class, "member1");
    Team team = member.getTeam();
    System.out.println("Team = "+team.getName());
    ```
    
#### 객체지향 쿼리 사용(JPQL)

```java

public class Main {

    public static void main(String[] args) {

        // 엔티티매니저팩토리 생성
        EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("jpa");
        // 엔티티매니저 생성
        EntityManager entityManager = entityManagerFactory.createEntityManager();
        // 트랜잭션 획득
        EntityTransaction entityTransaction = entityManager.getTransaction();

        try {
            entityTransaction.begin();
            testSave(entityManager);
            queryJoin(entityManager);
            entityTransaction.commit();
        } catch (Exception e) {
            System.out.println(e.getMessage());
            entityTransaction.rollback();
        } finally {
            entityManager.close();
        }

        entityManagerFactory.close();
    }

    public static void testSave(EntityManager em) {

        Team team1 = new Team("team1", "팀1");
        em.persist(team1);

        Member member1 = new Member("member1", "회원1");
        member1.setTeam(team1);
        em.persist(member1);

        Member member2 = new Member("member2", "회원2");
        member2.setTeam(team1);
        em.persist(member2);
    }

    public static void queryJoin(EntityManager em) {
        String jpql = "select m from Member m join m.team t where t.name = :teamName";

        List<Member> members = em.createQuery(jpql, Member.class).setParameter("teamName", "팀1").getResultList();
        for(Member member : members) {
            System.out.println("[query] member.username="+ member.getUsername());
        }
    }
}
```

### 수정

```java
public static void updateRelation(EntityManager em) {
    Team team2 = new Team("team2", "팀2");
    em.persist(team2);

    Member member = em.find(Member.class, "member1");
    member.setTeam(team2);

}
```

```java
SELECT MEMBER_ID, M.USERNAME, M.TEAM_ID, T.NAME AS TEAM_NAME
FROM MEMBER M JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID
```

### 연관관계 제거

```java
public static void deleteRelation(EntityManager em) {
        Member member2 = em.find(Member.class, "member2");
        member2.setTeam(null); // 연관관계 제거
    }
```

### 연관된 엔티티 삭제

- 연관된 엔티티를 삭제하려면 기존에 있던 연관관계를 먼저 제거하고 삭제해야 한다.

- 그렇지 않으면 외래 키 제약조건으로 인해 데이터베이스에서 오류가 발생

```java
member1.setTeam(null);
member2.setTeam(null);

em.remove(team);
```

## 양방향 연관관계

- 회원 → 팀은 N : 1, 팀 → 회원은 1: N 이다.

- 이 때 팀 → 회원으로 접근하는 관계를 추가하여 양방향 연관관계로 매핑할 수 있다.

- 데이터베이스는 외래 키 하나로 양방향 이므로 따로 추가할 내용은 없다.

### 양방향 연관관계 매핑

```java
package org.example;

import java.util.ArrayList;
import java.util.List;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.OneToMany;

@Entity
public class Team {

    @Id
    @Column(name = "TEAM_ID")
    private String id;

    private String name;

    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<Member>();

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public List<Member> getMembers() {
        return members;
    }

    public void setMembers(List<Member> members) {
        this.members = members;
    }

    public Team(String id, String name) {
        this.id = id;
        this.name = name;
    }

		public Team() {}
}
```

### 일대다 컬렉션 조회

```java
public static void biDirection(EntityManager em) {

      Team team1 = new Team("team1", "팀1");
      em.persist(team1);

      Member member1 = new Member("member1", "회원1");
      member1.setTeam(team1);
      em.persist(member1);

      em.flush();
      em.clear();

      Team findTeam = em.find(Team.class, team1.getId());
      List<Member> members = findTeam.getMembers();

      for(Member member : members) {
          System.out.println("member.username = "+ member.getUsername());
      }
  }
```

### 연관관계의 주인

- 엄밀히 얘기하면 객체에는 양방향 연관관계라는 것이 없다.

- 서로 다른 단방향 연관관계 2개를 잘 묶어서 양방향인 것처럼 보이게 할 뿐이다.

	- 테이블은 외래 키 하나로 두 테이블의 연관관계 관리

	- 엔티티를 단방향으로 매핑하면 참조를 하나만 사용

	- 엔티티를 양방향 연관관계로 설정하면 객체의 참조는 둘인데 외래 키는 하나다. 따라서 둘 사이에 차이가 발생

	- 두 객체 연관관계 중 하나를 정해서 테이블의 외래키를 관리 → 연관관계의 주인 (Owner)

### 양방향 매핑의 규칙

- 두 연관관계 중 하나를 연관관계의 주인으로 정해야 한다

- 연관관계의 주인만이 데이터베이스 연관관계와 매핑되고 외래 키를 관리(등록, 수정, 삭제) 할수 있다.

- 반면 주인이 아닌쪽은 읽기만 할수 있다.

- `mappedBy` 속성
    
    - 주인은 mappedBy 속성을 사용하지 않는다.
        
        - 주인이 아니면 mappedBy 속성을 사용해 속성의 값으로 연관관계의 주인을 지정해야 한다.

- 연관관계의 주인을 정한다는 것은 사실 외래 키 관리자를 선택하는 것

### 연관관계의 주인은 외래 키가 있는 곳

- 연관관계의 주인은 테이블에 외래 키가 있는 곳으로 정해야 한다

- Member 테이블이 외래 키를 가지고 있으므로 Member.team 의 주인이 된다.

- Team.members 에는 mappedBy = "team" 속성을 사용해 주인이 아님을 설정

- 주인이 아닌 반대편은 읽기만 가능하고 외래 키 변경은 하지 못한다.

- 데이터베이스 테이블의 다대일, 일대다 관계에서는 다 쪽이 외래 키를 가진다.

### 양방향 연관관계 저장

```java
public static void testSave(EntityManager em) {

        Team team1 = new Team("team1", "팀1");
        em.persist(team1);

        Member member1 = new Member("member1", "회원1");
        member1.setTeam(team1);
        em.persist(member1);

        Member member2 = new Member("member2", "회원2");
        member2.setTeam(team1);
        em.persist(member2);

    }
```

## 양방향 연관관계의 주의점

- 양방향 연관관계를 설정하고 가장 흔히 하는 실수는 연관관계의 주인에는 값을 입력하지 않고 주인이 아닌 곳에만 값을 입력하는것

```java
public static void testSaveNonOwner(EntityManager em) {

    Member member1 = new Member("member1","USER1");
    em.persist(member1);
    Member member2 = new Member("member2","USER2");
    em.persist(member2);

    Team team1 = new Team("team1", "팀1");
    em.persist(team1);

    team1.getMembers().add(member1);
    team1.getMembers().add(member2);
}
```

### 순수한 객체까지 고려한 양방향 연관관계

- 정말 연관관계의 주인에만 값을 저장하고 주인이 아닌 곳에는 값을 저장하지 않아도 될까?

- **객체 관점에서는 양쪽 방향에 모두 값을 입력해주는 것이 가장 안전**

```java
public static void originalJava() {
    Member member1 = new Member("member1","USER1");
    Member member2 = new Member("member2","USER2");
    Team team1 = new Team("team1", "팀1");

    member1.setTeam(team1);
    member2.setTeam(team1);

    List<Member> members = team1.getMembers();
    System.out.println(members.size());
}
```

- JPA를 사용하지 않는 순수 객체의 경우 Member.team 은 설정했지만, 반대의 연관관계를 설정하지 않았다.

- members.size() → 0

- 양방향은 양쪽다 관계를 설정해야한다.

- 회원 → 팀  : member.setTeam();

- 팀 → 회원 : team.getMembers().addMember();

```java
member1.setTeam(team1);

team1.getMembers().add(member1);

member2.setTeam(team1);

team1.getMembers().add(member2);
```

### 연관관계 편의 메소드

- 양방향 연관관계는 양쪽을 다 신경써야 한다.

- 위의 코드처럼 작성하다 보면 나도 모르는 사이에 실수를 할 수 있을 것 같다.(오타, 착각 등..) 

- 양방향에서는 두 코드가 하나인 것처럼 사용하는 것이 안전하다.

```java
public void setTeam(Team team) {
	// 사실 이 코드로 문제가 있다...
	// 단순한 테스트용으로 작성하였다.
    if(this.team != null) {
        this.team.getMembers().remove(this);
    }
    this.team = team;
    team.getMembers().add(this);
}
```

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]

