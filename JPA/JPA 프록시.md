# JPA 프록시

## 목차

- [프록시(Proxy)](#프록시(Proxy))
- [즉시 로딩과 지연 로딩](#즉시-로딩과-지연-로딩)
- [지연 로딩 활용](#지연-로딩-활용)
- [영속성 전이 : CASCADE](#영속성-전이--cascade)
- [고아 객체](#고아-객체)

## 프록시(Proxy)

- 엔티티를 조회할 때 연관된 엔티티들이 항상 사용되는 것은 아님

- 예를 들어 회원 엔티티를 조회할 때 연관된 팀 엔티티는 비즈니스 로직에 따라 사용될 때도 있지만, 그렇지 않을 때도 있다.

```java
@Entity
public class Memebr {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	private String username;

	@ManyToOne
	private Team team;
}

@Entity
public class Team {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	private String name;
}

```

```java
public static void printUserAndTeam(EntityManager entityManager, Long memberId) {
	Member member = em.find(Member.class, memberId);
	Team team = member.getTeam();
	System.out.println("회원 이름 : "+ member.getUserName());
	System.out.println("소속팀: " + team.getName());
}

public static void printUser(EntityManager entityManager, Long memberId) {
	Member member = em.find(Member.class, memberId);
	System.out.println("회원 이름 : "+ member.getUserName());
}
```

- printUserAndTeam() 메소드에서는 memberId 로 회원 엔티티를 찾아서 회원과 연관된 팀의 이름도 출력

- 반면 printUser() 메소드에서는 회원 엔티티만 출력하는데 사용하고 팀 엔티티는 전혀 사용 X

- 이렇게 회원 엔티티만 사용하는 로직에서 em.find()로 회원 조회 시 회원과 연관된 팀 엔티티를 데이터베이스에서 함께 조회하는 것은 효율적이지 않음

- JPA는 이런 문제를 해결하려고 엔티티가 실제 사용될 때까지 데이터베이스 조회를 지연하는 방법을 제공
    - → 지연로딩 : team.getName() 처럼 팀 엔티티의 값이 실제로 사용하는 시점에 데이터베이스에서 팀 엔티티에 필요한 데이터를 조회하는 것 

- 이렇게 지연로딩을 사용하려면 실제 엔티티 객체대신 데이터베이스 조회를 지연할 수 있는 가짜 객체가 필요한데 이것을 `프록시 객체`라고 한다. 

> JPA 표준 명세는 지연로딩의 구현 방법을 구현체에 위임하였음.
> 따라서 이후 내용은 하이버네이트 구현체에 대한 내용

### 프록시 기초

- JPA에서 식별자로 엔티티 하나를 조회할 때는 em.find() 를 사용

- 이 메소드는 영속성 컨텍스트에 엔티티가 없으면 데이터베이스를 조회

- 엔티티를 실제 사용하는 시점까지 데이터베이스 조회를 미루고 싶으면 `em.getReference()` 를 사용

- 이 메소드를 호출할 때 JPA 는 데이터베이스를 조회하지 않고 실제 엔티티 객체도 생성하지 않음

- 대신에 데이터베이스 접근을 위임한 프록시 객체를 반환함

![프록시 개체 생성](https://github.com/user-attachments/assets/2e210dbd-2537-454f-bba2-d5742d34c00d)

### 프록시의 구조

- 프록시 클래스는 실제 클래스를 상속 받아서 만들어지므로 실제 클래스와 겉 모양이 같음

- 사용하는 입장에서는 이것이 진짜 객체인지 프록시 객체인지 구분하지 않고 사용하면 됨

![프록시의 구조](https://github.com/user-attachments/assets/dc7b6752-bde3-4d8f-971e-1636927b2cb8)

- 프록시 객체는 실제 객체에 대한 참조(target)를 보관

- 그리고 프록시 객체의 메소드를 호출하면 프록시 객체는 실제 객체의 메소드를 호출

![프록시의 위임](https://github.com/user-attachments/assets/5883ec4f-007d-4765-84fe-8682c088bb77)

### 프록시 객체의 초기화

- 프록시 객체는 em.find() 처럼 실제 사용될 때 데이터베이스를 조회해서 실제 엔티티 객체를 생성 → 프록시 객체의 초기화

```java
// MemberProxy 반환
Member member = em.getReference(Member.class, 1L);
// 1. 실제 데이터 조회
member.getUserName();
```

```java
class MemberProxy extends Member {
	
	Member target = null; // 실제 엔티티 참조
	
	public String getUserName() {
		
		if(target == null) {
			// 2. 초기화 요청
			// 3. DB 조회
			// 4. 실제 엔티티 생성 및 참조 보관
			this.target = ...;
		}

		// 5. target.getUserName();
		return target.getUserName();
	}
}
```

1. 프록시 객체에  member.getUserName() 을 호출해서 실제 데이터를 조회

2. 프록시 객체는 실제 엔티티가 생성되어 있지 않으면 영속성 컨텍스트에 실제 엔티티 생성을 요청 → 초기화

3. 영속성 컨텍스트가 데이터베이스를 조회해서 실제 엔티티 객체를 생성

4. 프록시 객체는 생성된 실제 엔티티 객체의 참조를 Member target 멤버변수에 보관

5. 프록시 객체는 실제 엔티티 객체의 getUserName() 을 호출해서 결과를 반환

### 프록시 특징

- 프록시 객체는 처음 사용할 때 한 번만 초기화

- 프록시 객체를 초기화한다고 프록시 객체가 실제 엔티티로 바뀌는 것은 아님

- 프록시 객체가 초기화되면 프록시 객체를 통해서 실제 엔티티에 접근할 수 있음

- 프록시 객체는 원본 엔티티를 상속받은 객체이므로 타입 체크 시에 주의해서 사용해야 함

- 영속성 컨텍스트에 찾는 엔티티가 이미 있으면 데이터베이스를 조회할 필요가 없으므로 em.getReference()를 호출해도 프록시 객체가 아닌 실제 엔티티를 반환

- 초기화는 영속성 컨텍스트의 도움을 받아야 가능 → 준영속 상태의 프록시를 초기화하면 문제 발생

### 준영속 상태와 초기화

```java
// MemberProxy 반환
Member member = em.getReference(Member.class, 1L);
transaction.commit();
em.close(); // 영속성 컨텍스트 종료

member.getUserName(); // 준영속 상태 초기화 시도
											// org.hiberante.LazyInitializationExpcetion 예외 발생
```

### 프록시와 식별자

- 엔티티를 프록시로 조회할 때 식별자(PK) 값을 파라미터로 전달 → 프록시 객체는 이 식별자 값을 보관

```java
Team team = em.getReference(Team.class, 1L); // 식별자 보관
team.getId(); // 초기화 되지 않음
```

- 프록시 객체는 식별자 값을 가지고 있으므로 식별자 값을 조회하는 team.getId()를 호출해도 프록시를 초기화 하지 않음

    > → 단, 엔티티 접근 방식을 @Access(AccessType.PROPERTY)로 설정한 경우에만 초기화하지 않음

- @Access(AccessType.FILED)로 설정하면  getId() 메소드가 id만 조회하는 메소드인지 다른 필드까지 활용해서 어떤 일을 하는 메소드인지 알지 못하므로 프록시 객체를 초기화

```java
Member member = em.find(Member.class, 1L);
Team team = em.getReference(Team.class, 1L);// SQL을 실행하지 않음
member.setTeam(team);
```

### 프록시 확인

- JPA가 제공하는 PersistenceUnitUtil.isLoaded(Object entity) 메소드르 사용하면 프록시 인스턴스의 초기화 여부를 알 수 있음

    - 아직 초기화 되지 않은 프록시 인스턴스는 false 를 반환
    
    - 이미 초기화 되었거나 프록시 인스턴스가 아니면 true 반환

- 조회한 엔티티가 진짜 엔티티 인지 프록시로 조회한 것인지 확인하려면 클래스명을 직접 출력해보면 됨

클래스명 뒤에 `..javassist..` 라 되어 있으면 **프록시**
 
```java
System.out.println("memberProxy :" + member.getClass().getName());
```

## 즉시 로딩과 지연 로딩

### 즉시 로딩 (EAGER LOADING)

- 엔티티를 조회할 때 연관된 엔티티도 함께 조회한다.

- @ManyToOne(fetch = FetchType.EAGER)

```sql
select
    member0_.member_id as member_i1_0_0_,
    member0_.team_id as team_id3_0_0_,
    member0_.username as username2_0_0_,
    team1_.team_id as team_id1_1_1_,
    team1_.name as name2_1_1_ 
from
    member member0_ 
left outer join
    team team1_ 
        on member0_.team_id=team1_.team_id 
where
    member0_.member_id=?
```

### 지연 로딩 (LAZY LOADING)

- 연관된 엔티티를 실제 사용할 때 조회한다.
- @ManyToOne(fetch = FetchType.LAZY)

```java
Member findMember = em.find(Member.class, "member1");
Team team = findMember.getTeam(); // 객체 그래프 탐색 -> 프록시 객체
team.getName(); // 팀 객체 실제 사용
```

em.find() 실행 시 회원만 조회하고 팀은 조회하지 않는다

대신 조회한 회원의 team 멤버변수에 프록시 객체를 넣어둔다.

team.getName()처럼 실제 사용될 때 까지 데이터 로딩을 미룬다.

```java
Member findMember = em.find(Member.class, "member1");
Team team = findMember.getTeam(); // 객체 그래프 탐색 -> 프록시 객체
System.out.println(team.getClass().getName());
//org.example.Team$HibernateProxy$ixMBWqaO
```

## 지연 로딩 활용

![image](https://github.com/user-attachments/assets/0f976640-d463-475a-8821-60879ad017a6)

- 회원은 팀 하나에만 소속될 수 있다 (N:1)
- 회원은 여러 주문 내역을 가진다. (1:N)
- 주문 내역은 상품 정보를 가진다. (N:1)

<br>

- Member와  Team 은 자주 함께 사용되어 즉시 로딩으로 설정
- Member와 연관된 Order는 가끔 사용되어 지연 로딩으로 설정
- Order와 연관된 Product 는 자주 함께 사용되어 즉시 로딩으로 설정

1. 신규 프로젝트 생성 : CH08
2. resources 폴더에 META-INF > persistence.xml 복사
3. pom.xml 에 dependecies 부분 복사
4. h2 데이터베이스 생성
5. persistence.xml 수정 

```java
package org.example;

import javax.persistence.*;
import java.util.List;

@Entity
public class Member {

    @Id
    private String id;
    private String username;
    private Integer age;

    @ManyToOne(fetch = FetchType.EAGER)
    private Team team;

    @OneToMany(mappedBy = "member", fetch = FetchType.LAZY)
    private List<Order> orders;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Team getTeam() {
        return team;
    }

    public void setTeam(Team team) {
        this.team = team;
    }

    public List<Order> getOrders() {
        return orders;
    }

    public void setOrders(List<Order> orders) {
        this.orders = orders;
    }
}
```

```java
package org.example;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Team {

    @Id
    private String id;
    private String name;

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
}
```

```java
package org.example;

import javax.persistence.*;

@Entity
@Table(name = "orders")
public class Order {

    @Id
    @GeneratedValue
    private Long id;

    @ManyToOne
    @JoinColumn(name = "MEMBER_ID")
    private Member member;

    @ManyToOne(fetch = FetchType.EAGER, optional = false)
    @JoinColumn(name = "PROUDCT_ID")
    private Product product;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public Member getMember() {
        return member;
    }

    public void setMember(Member member) {
        this.member = member;
    }

    public Product getProduct() {
        return product;
    }

    public void setProduct(Product product) {
        this.product = product;
    }
}
```

```java
package org.example;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Product {

    @Id
    @GeneratedValue
    @Column(name = "PRODUCT_ID")
    private Long id;

    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

```java
package org.example;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class Main {
    public static void main(String[] args) {
        EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("jpa");
        EntityManager em = entityManagerFactory.createEntityManager();
        EntityTransaction transaction = em.getTransaction();

        try {
            transaction.begin();

            Member member = new Member();
            member.setId("member1");
            member.setUsername("사용자1");

            Team team = new Team();
            team.setId("team1");
            team.setName("팀1");
            em.persist(team);

            member.setTeam(team);
            em.persist(member);

            Product product = new Product();
            product.setName("상품1");
            em.persist(product);

            Order order = new Order();
            order.setMember(member);
            order.setProduct(product);
            em.persist(order);

            em.flush();
            em.clear();

            Member findMember = em.find(Member.class, "member1");

            List<Order> orders = findMember.getOrders();
            System.out.println("orders = "+ orders.getClass().getName());
//            System.out.println("orders.size = "+ orders.size());

            transaction.commit();
        } catch (Exception e) {
            transaction.rollback();
        } finally {
            em.close();
        }
        entityManagerFactory.close();
    }
}
```

```java
select
        member0_.id as id1_0_0_,
        member0_.age as age2_0_0_,
        member0_.team_id as team_id4_0_0_,
        member0_.username as username3_0_0_,
        team1_.id as id1_2_1_,
        team1_.name as name2_2_1_ 
    from
        member member0_ 
    left outer join
        team team1_ 
            on member0_.team_id=team1_.id -> 즉시로딩 
    where
        member0_.id=?
```

```java
Member findMember = em.find(Member.class, "member1");

List<Order> orders = findMember.getOrders();
System.out.println("orders = "+ orders.getClass().getName());
// orders = org.hibernate.collection.internal.PersistentBag
```

```java
System.out.println("orders.size = "+ orders.size());
 ----
select
        orders0_.member_id as member_i2_1_0_,
        orders0_.id as id1_1_0_,
        orders0_.id as id1_1_1_,
        orders0_.member_id as member_i2_1_1_,
        orders0_.proudct_id as proudct_3_1_1_,
        product1_.product_id as product_1_2_2_,
        product1_.name as name2_2_2_ 
    from
        orders orders0_ 
    left outer join
        product product1_ 
            on orders0_.proudct_id=product1_.product_id 
    where
        orders0_.member_id=?
```

### 컬렉션 래퍼

- 컬렉션 래퍼 : 하이버네이트 엔티티를 영속 상태로 만들 때 엔티티에 컬렉션이 있으면 컬렉션을 추적하고 관리할 목적으로 원본 컬렉션을 하이버네이트가 제공하는 내장 컬렉션으로 변경 → 컬렉션 래퍼
- orders = org.hibernate.collection.internal.PersistentBag
- 엔티티를 지연 로딩하면 프록시 객체를 이용해서 지연 로딩을 수행하지만 주문 내역 같은 컬렉션은 컬렉션 래퍼가 지연 로딩을 처리해줌
- member.getOrders()를 호출해도 컬렉션은 초기화 되지 않는다. member.getOrders().get(0) 처럼 컬렉션에서 실제 데이터를 조회할 때 데이터베이스를 조회해서 초기화

### JPA 기본 fetch 전략

#### @ManyToOne, @OneToOne : 즉시 로딩 (FetchType.EAGER)
#### @OneToMany, @ManyToMany : 지연 로딩 (FetchType.LAZY)

- JPA 의 기본 fetch 전략은 연관된 엔티티가 하나면 즉시 로딩을, 컬렉션이면 지연 로딩을 사용

- 즉시 로딩의 문제 : 컬렉션을 로딩하는 것은 비용이 많이 들고 잘못하면 너무 많은 테이블을 로딩할 수 있음

> 추천하는 방법 : **모든 연관관계에 지연 로딩을 사용** 하고 개발 완료 단계에 왔을 때 실제 사용하는 상황을 보고 꼭 필요한 곳에만 즉시 로딩을 사용하도록 최적화

### 컬렉션에 FetchType.EAGER 사용 시 주의점

- 컬렉션을 하나 이상 즉시 로딩하는 것은 권장하지 않음
    - 컬렉션과 조인한다는 것은 데이터베이스 테이블로 보면 일대다 조인 → 일대다 조인은 결과 데이터가 다 쪽에 있는 수 만큼 증가
    - 문제는 서로 다른 컬렉션을 2개 이상 조인할 때 발생 → 예를 들어 A 테이블을 N, M 두 테이블과 일대다 조인하면 SQL  실행 결과가 N * M 이 되면서 너무 많은 데이터를 반환할 수 있고, 어플리케이션 성능이 저하될 수 있음

- 컬렉션 즉시 로딩은 항상 외부 조인(OUTER JOIN)을 사용
    - 다대일 관계인 회원테이블과 팀 테이블을 조인할 때 회원 테이블의 외래 키에 not null 제약조건을 걸어두면 모든 회원은 팀에 소속 되므로 항상 내부 조인 (INNER JOIN) 을 사용해도 됨
    - 반대로 팀 테이블에서 회원 테이블로 일대다 관계를 조인할 때 회원이 한명도 없는 팀을 내부 조인하면 팀까지 조회 되지 않는 문제가 발생
        
        ```java
        Team team2 = new Team();
        team2.setId("team2");
        team2.setName("팀2");
        em.persist(team2);
        
        SELECT * FROM TEAM T
        INNER JOIN MEMBER M ON T.id = M.TEAM_ID
        ```
        
        ![image](https://github.com/user-attachments/assets/95e4bfd6-ef25-44ed-9f0d-6efcf6d27ce4)

        ![image](https://github.com/user-attachments/assets/8b793306-c948-43c8-a15b-6a1c7307a8a5)

    - 이러한 상황 때문에 JPA 는 일대다 관계를 즉시 로딩할 때 항상 외부 조인을 사용한다.

| @ManyToOne, @OneToOne | optional = false : 내부조인 | optional = true : 외부 조인 |
| --- | --- | --- |
| @OneToMany, @ManyToMany | optional = false : 외부조인 | optional = true : 내부 조인 |

## 영속성 전이 : CASCADE

특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속 상태로 만들고 싶은면 영속성 전이 (transitive persistence) 기능을 사용하면 된다. → JPA 는 CASCADE 옵션으로 영속성 전이를 제공

→ 영속성 전이를 사용하면 부모 엔티티를 저장할 때 자식 엔티티도 함께 저장할 수 있다.

```java
package org.example;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.OneToMany;
import java.util.ArrayList;
import java.util.List;

@Entity
public class Parent {

    @Id
    @GeneratedValue
    private Long id;

    @OneToMany(mappedBy = "parent")
    private List<Child> children = new ArrayList<>();

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public List<Child> getChildren() {
        return children;
    }

    public void setChildren(List<Child> children) {
        this.children = children;
    }
}
```

```java
package org.example;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.ManyToOne;

@Entity
public class Child {

    @Id
    @GeneratedValue
    private Long id;

    @ManyToOne
    private Parent parent;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public Parent getParent() {
        return parent;
    }

    public void setParent(Parent parent) {
        this.parent = parent;
    }
}
```

```java
public static void saveNoCascade(EntityManager em) {
    Parent parent = new Parent();
    em.persist(parent);

    Child child1 = new Child();
    child1.setParent(parent);
    parent.getChildren().add(child1);
    em.persist(child1);

    Child child2 = new Child();
    child2.setParent(parent);
    parent.getChildren().add(child2);
    em.persist(child2);
}
```

- JPA 에서 엔티티를 저장할 때 연관된 모든 엔티티는 영속 상태여야 함

### 영속성 전이 : 저장

```java
@OneToMany(mappedBy = "parent", cascade = CascadeType.PERSIST)
private List<Child> children = new ArrayList<>();
```

- 영속성 전이는 연관관계를 매핑하는 것과는 아무 관련이 없음

### 4.2 영속성 전이 : 삭제

방금 저장한 부모 자식 엔티티를 모두 제거하려면 각각의 엔티티를 하나씩 제거해야 함

```java
public static void removeNoCascade(EntityManager em) {
    // Parent의 Cascade 옵션 제거 후 테스트
    Parent findParent = em.find(Parent.class, 3L);
    Child child1 = em.find(Child.class, 4L);
    Child child2 = em.find(Child.class, 5L);

    em.remove(child1);
    em.remove(child2);
    em.remove(findParent);
}
```

- DELETE SQL 을 3번 실행하면서 부모와 자식을 삭제
- 삭제 순서는 외래 키 제약조건을 고려해서 자식을 먼저 삭제하고 부모를 삭제

```java
public static void removeWithCascade(EntityManager em) {
    // Parent의 Cascade 옵션 추가 후 테스트 CascadeType.REMOVE
    Parent findParent = em.find(Parent.class, 3L);
    em.remove(findParent);
}
```

- CascadeType.REMOVE 를 설정하지 않고 실행하면 외래키 제약조건으로 예외 발생

### CASCADE 종류

```java
public enum CascadeType {
	ALL, // 모두 적용
	PERSISTE, // 영속
	MERGE, // 병합
	REMOVE, // 삭제
	REFRESH, // REFRESH
	DETACH, // DETACh
}

// 다음처럼 여러 속성을 같이사용할 수 있다.
casecade = {CascadeType.PERSISTE, CascadeType.REMOVE}
```

## 고아 객체

- 고아 객체 (ORPHAN) 제거 : 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제하는 기능 

- 이 기능을 사용하여 부모 엔티티의 컬렉션에서 자식 엔티티의 참조만 제거하면 자식 엔티티가 자동으로 삭제되도록 할 수 있음

```java
@OneToMany(mappedBy = "parent", cascade = CascadeType.ALL, orphanRemoval = true)
private List<Child> children = new ArrayList<>();
```

```java
public static void saveAndRemoveWithCascade(EntityManager em) {

    Child child1 = new Child();
    Child child2 = new Child();

    Parent parent = new Parent();
    child1.setParent(parent);
    child2.setParent(parent);
    parent.getChildren().add(child1);
    parent.getChildren().add(child2);

    // 부모만 저장, 연관된 자식들 저장
    em.persist(parent);

    // Parent의 Cascade 옵션 추가 후 테스트
    Parent findParent = em.find(Parent.class, parent.getId());
    findParent.getChildren().remove(0);
}
```

- 참조하는 곳이 하나일때 사용해야 함
- 특정 엔티티가 개인 소유일 때
- @OneToOne, @OneToMany 일때만 사용

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]
