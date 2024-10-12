# JPA 연관관계 매핑

## 목차

- [N : 1 [다대일]](#n--1-다대일)

- [1 : N [일대다]](#1--n-일대다)

- [1 : 1 [일대일]](#1--1-일대일)

- [N : N [다대다]](#n--n-다대다)

## N : 1 [다대일]

### 다대일 단방향 [N : 1]

![N:1](https://github.com/user-attachments/assets/f06f8b55-b63a-4765-ac67-c7f07f37f1fb)

- 회원은 Member.team 으로 팀 엔티티를 참조할 수 있지만, 반대로 팀에는 회원을 참조하는 필드가 없음

=> **다대일 단방향 연관관계**

```java
@ManyToOne
@JoinComlumn(name = "TEAM_ID")
private Team team;
```

### 다대일 양방향 [N : 1, 1 : N]

![N : 1 , 1 : N](https://github.com/user-attachments/assets/3598b6ed-57b5-40a8-a14d-d563b28cd646)


```java
@Entity
public class Member {
		@Id
    @Column(name = "MEMBER_ID")
    private String id;

    private String username;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;

    
    public void setTeam(Team team) {
        if(this.team != null) {
            this.team.getMembers().remove(this);
        }
        this.team = team;
        if(!team.getMembers().contains(this)) {
            team.getMembers().add(this);
        }
    }
```

- 양방향은 외래 키가 있는 쪽이 연관관계의 주인이다.
    
    - 1 : N과 N : 1의 연관관계는 N 쪽에 외래 키가 있다.
    
    - 주인이 아닌 쪽은 조회를 위한 JPQL이나 객체 그래프를 탐색할 때 사용한다.

- 양방향 연관관계는 항상 서로를 참조해야 한다.

## 1 : N [일대다]

- 일대다 관계는 엔티티를 하나 이상 참조할 수 있으므로 Collection, List, Set, Map 중에 하나 사용한다.

### 일대다 단방향 [1 : N]

- 하나의 팀은 여러 회원을 참조할 수 있다. => 1 : N

![1 : N](https://github.com/user-attachments/assets/3651f0a6-fe1d-4b54-9f26-7a4505c5949f)

- members로 회원 테이블의 TEAM_ID 외래키를 관리한다.

- 보통 자신이 매핑한 테이블의 외래 키를 관리하는데 이 매핑은 반대쪽 테이블에 있는 외래 키를 관리한다.

```java
@Entity
public class Team {

	@Id @GeneratedValue
	@Column(name = "TEAM_ID")
	private Long id;

	private String name;
	
	@OneToMany
	@JoinColmun(name = "TEAM_ID") // MEMBER 테이블의 TEAM_ID(FK)
	private List<Member> members = new ArrayList<>();
}
```

- 일대다 단방향 관계를 매핑할 때는 @JoinColumn 을 명시해야 한다.

- 일대다 단방향 매핑의 단점
    
    - 매핑한 객체가 관리하는 외래 키가 다른 테이블에 있다.
    
    - 본인 테이블에 외래 키가 있으면 엔티티의 저장과 연관관계 처리를 INSERT SQL 한번으로 끝낼수 있지만, 다른 테이블에 외래키가 있으면 UPDATE SQL 을 추가로 실행해야 한다.

- 일대다 단방향 매핑보다는 다대일 양방향 매핑을 사용하는 것이 좋을 것 같다.

### 일대다 양방향 [1 : N, N : 1]

- 일대다 양방향 매핑은 존재하지 않는다. 

- 양방향 매핑에서 @OneToMay는 연관관계의 주인이 될 수 없다. → 항상 다(N)쪽에 외래키가 있다.

- 따라서 @OneToMay, @ManyToOne 둘 중에 연관관계의 주인은 항상 다쪽인 @ManyToOne을 사용한 곳이다.

## 1 : 1 [일대일]

- 일대일 관계는 양쪽이 서로 하나의 관계만 가진다.

- 회원은 하나의 주소만을 사용하고 주소도 하나의 회원에 의해서만 사용된다.

- 일대일 관계는 주 테이블과 대상 테이블 둘 중 어느 곳에서나 외래 키를 가질 수 있다.

### 주 테이블에 외래키

#### - 단방향

![1 : 1_(주)_단방향](https://github.com/user-attachments/assets/3797636b-bf95-4d3d-8ec9-efa2b0285a24)

```java
@Entity
public class Member {
	@Id @GeneratedValue
	@Column(name = "TEAM_ID")
	private Long id;

	private String name;

	@OneToOne
	@JoinColumn(name ="LOCKER_ID")
	private Locker locker;
}

@Entity
public class Locker {
	@Id @GeneratedValue
	@Column(name = "LOCKER_ID")
	private Long id;

	private String name;
}
```

#### - 양방향

![1 : 1_(주)_양방향](https://github.com/user-attachments/assets/5d49b623-5df8-4742-abd5-165969a6d3e0)

```java
@Entity
public class Member {
	@Id @GeneratedValue
	@Column(name = "MEMBER_ID")
	private Long id;

	private String name;

	@OneToOne
	@JoinColumn(name ="LOCKER_ID")
	private Locker locker;
}

@Entity
public class Locker {
	@Id @GeneratedValue
	@Column(name = "LOCKER_ID")
	private Long id;

	private String name;

	@OneToOne(mappedBy = "locker") // 양방향 이므로 주인을 저해야 한다.
	private Member member;
}
```

### 대상 테이블에 외래 키

#### - 단방향

- 일대일 관계 중 대상 테이블에 외래키가 있는 단방향 관계는 JPA에서 지원하지 않는다.

- 단방향 관계를 Locker에서 Member 방향으로 수정하거나, 양방향 관계로 만들고  Locker를 연관관계 주인으로 설정해야 한다.

![1 : 1_(단)_단방향](https://github.com/user-attachments/assets/518e62b8-4b38-43d2-a7bc-942b261b4b2f)

#### — 양방향

```java
@Entity
public class Member {
	@Id @GeneratedValue
	@Column(name = "MEMBER_ID")
	private Long id;

	private String name;

	@OneToOne(mappedBy = "member")
	private Locker locker;
}

@Entity
public class Locker {
	@Id @GeneratedValue
	@Column(name = "LOCKER_ID")
	private Long id;

	private String name;

	@OneToOne
	@JoinColumn(name = "MEMBER_ID")
	private Member member;
}
```

![1 : 1_(단)_양방향](https://github.com/user-attachments/assets/925f305c-3aa1-4335-8991-9ad5095fa661)


- 일대일 매핑에서 대상 테이블에 외래 키를 두고 싶으면 양방향으로 매핑한다.

- Locker를 연관관계의 주인으로 만들어서 LOCKER 테이블의 외래 키를 관리한다.

## N : N [다대다]

- 관계형 데이터베이스는 정규화된 테이블 2개로 다대다 관계를 표현할 수 없다.

- 그렇기에 N : N 관계를 1 : N, N : 1 로 풀어내는 연결 테이블을 사용한다.

- Ex) 

    - 회원은 상품을 주문한다.
    
    - 상품은 회원들에 의해 주문 된다.
    
    - 회원과 상품은 다대다 관계

![N : N](https://github.com/user-attachments/assets/30904899-6e40-446a-85fd-36adc576ab7c)

- 하지만 객체는 테이블과 다르게 객체 2개로 다대다 관계를 만들 수 있다.

- @ManyToMany 사용

### 다대다 : 단방향

```java
@Entity
public class Member {
	@Id
	@Column(name = "MEMBER_ID")
	private String id;

	private String name;

	@ManyToMany
	@JoinTable(name = "MEMBER_PRODUCT")
		, joinColumns = @JoinColumn(name = "MEMBER_ID")
		, inverseJoinColumns = @JoinColumn(name = "PRODUCT_ID"))
	private List<Product> products = new ArrayList<Product>();
}

@Entity
public class Product {
	@Id
	@Column(name = "PRODUCT_ID")
	private String id;

	private String name;

}
```

- @ManyToMany와 @JoinTable 을 사용해서 연결 테이블을 바로 매핑 => 회원_상품(Member_Product) 엔티티 없이 매핑 가능 

- @JoinTable

    - name : 연결 테이블을 지정
    
    - joinColumns: 현재 방향인 회원과 매핑할 조인 컬럼 정보 지정
    
    - inverseJoinColumns : 반대 방향인 상품과 매핑할 조인 컬럼 정보 지정

#### 저장

```java
public void save() {
	
	Product productA = new Product();
	productA.setId("productA");
	productA.setName("상품A");
	em.persist(productA);

	Member member1 = new Member();
	member1.setId("member1");
	member1.setName("회원1");
	member1.getProducts().add(productA);
	em.persist(member1);
}
```

— INSERT INTO PRODUCT …

— INSERT INTO MEMBER …

— INSERT INTO MEMBER_PRODUCT …

#### 조회

```java
public void find() {
	Member member = em.find(Member.class, "member1");
	List<Product> products = member.getProducts();
	for(Product product : products) {
		System.out.println("product.name :"+ product.getName());
	}
}
```

```sql
SELECT * FROM MEMBER_PRODUCT MP
INNER JOIN PRODUCT P ON MP.PRODUCT_ID = P.PRODUCT_ID
WHERE MP.MEMBER_ID = ?
```

### 다대다 : 양방향

- 다대다 매핑이므로 역방향도 @ManyToMany 사용

- 양쪽 중 원하는 곳에 mappedBy로 연관관계의 주인 지정 `(mappedBy 가 없는 곳이 연관관계의 주인)`

```java

@Entity
public class Product {
	@Id
	@Column(name = "PRODUCT_ID")
	private String id;

	private String name;

	@ManyToMany(mappedBy = "products")
	private List<Member> members;

}
```

### 다대다 : 매핑의 한계와 극복, 연결 엔티티 사용

- @ManyToMany를 사용하면 연결 테이블을 자동으로 처리해주므로 도메인 모델이 단순해 지고 편리하다.

- 하지만 이 매핑을 실무에서 사용하기에 한계가 있다.

- 연결 테이블에 필요한 컬럼이 더 많이 있다면?

![N : N_매핑한계_극복](https://github.com/user-attachments/assets/1b5892d4-a047-4fe7-8112-29669fa7d151)

- 연결 테이블에 주문 수량 (ORDERAMOUNT) 와 주문 날짜 (ORDERDATE) 컬럼을 추가하면 더는 @ManyToMany 를 사용할 수 없다.

- 결국 연결 테이블을 매핑하는 연결 엔티티를 만들고 이곳에 추가한 컬럼을 매핑해야 한다.

```java
@Entity
public class Member {

	@Id
	@Column(name = "MEMBER_ID")
	private String id;

	private String name;

	@OneToMany(mappedBy = "member")
	private List<MemberProduct> memberProducts;
}
```

- 회원 상품 엔티티 쪽이 외래키를 가지고 있으므로 연관관계의 주인

```java
@Entity
public class Product {
	@Id
	@Column(name = "PRODUCT_ID")
	private String id;

	private String name;

}
```

```java
@Entity
@IdClass(MemberProductId.class)
public class MemberProduct {
	
	@Id
	@ManyToOne
	@JoinColumn(name = "MEMBER_ID")
	private Member member; // MemberProductId.member 와 연결

	@Id
	@ManyToOne
	@JoinColumn(name = "PRODUCT_ID")
	private Product product; // MemberProductId.product 와 연결

	private int orderAmount;
}
```

```java
public class MemberProductId implements Serializable {
	
	private Long member;
	private Long Product;

	@Override
	public boolean equals(Object o) { ... }

	@Override
	public int hashCode() { ... }
}
```

- 기본 키 매핑하는 @Id + 외래 키 매핑 @JoinColumn 을 동시에 사용해서 기본 키 + 외래 키를 한번에 매핑

- @IdClass 사용해 복합 키 매핑

#### 복합기본키

- 회원상품 엔티티는 기본 키가 MEMBER_ID, PRODUCT_ID 로 이루어진 복합 기본 키

- JPA 에서 복합 키를 사용하려면 별도의 식별자 클래스를 만들어야 한다. → @IdClass 를 사용해 식별자 클래스 지정

- 식별자 클래스 특징
    
    - 복합 키는 별도의 식별자 클래스로 만들어야 한다.
    
    - Serializable 을 구현해야 한다.
    
    - equals 와 hashCode 메소드를 구현해야 한다.
    
    - 기본 생성자가 있어야 한다.
    
    - 식별자 클래스는 public 이어야 한다.
    
    - @IdClass 를 사용하는 방법 외에 @EmbeddedId 를 사용하는 방법도 있다.

#### 식별관계

- 회원상품은 회원과 상품의 기본 키를 받아서자신의 기본 키로 사용한다.

- 이렇게 부모 테이블의 기본 키를 받아서 자신의 기본 키 + 외래 키로 사용하는 것 → 식별 관계 (Identifying Relationship)

```java
public void save() {
	
	Member member1 = new Member();
	member1.setId("member1");
	member1.setName("회원1");
	em.persist(member1);

	Product productA = new Product();
	productA.setId("productA");
	productA.setName("상품A");
	em.persist(productA);

	MemberProduct memberProduct = new MemberProduct();
	memberProduct.setMemebr(member1);
	memberProduct.setProudct(productA);
	memberPorduct.setOrderAmount(2);

	em.persist(memberProduct);
}
```

```java
public void find() {
	// 기본 키 값 생성
	MemberProductId memberProductId = new MemberProductId();
	memberProudctId.setMember("member1");
	memberProductId.setProduct("productA");
	
	MemberProduct memberProduct = em.find(MemberProduct.class, memberProductId);
	
	Member member = memberProduct.getMember();
	Product product = memberProduct.getProduct();
	
	System.out.println("member = "+ member.getName());
	System.out.println("product = "+ product.getName());
	System.out.println("orderAmount = "+ memberProduct.getOrderAmount());

}
```

### 다대다 : 새로운 기본 키 사용

- 추천하는 기본 키 생성 전략은 데이터베이스에서 자동으로 생성해주는 대리 키를 Long 으로 사용하는 것

![N : N_새로운 기본키](https://github.com/user-attachments/assets/d1a1ddb6-35ea-4ca5-a481-ba673f952444)

```java
@Entity
public class Order {
	
	@Id
	@GeneratedValue
	@Column(name = "ORDER_ID")
	private Long id;

	@ManyToOne
	@JoinColumn(name = "MEMBER_ID")
	private Member member;

	@ManyToOne
	@JoinColumn(name = "PRODUCT_ID")
	private Product product;

	private int orderAmount;
}
```

```java
public void save() {
	
	Member member1 = new Member();
	member1.setId("member1");
	member1.setName("회원1");
	em.persist(member1);

	Product productA = new Product();
	productA.setId("productA");
	productA.setName("상품A");
	em.persist(productA);

	Order order = new Order();
	order.setMemebr(member1);
	order.setProudct(productA);
	order.setOrderAmount(2);

	em.persist(memberProduct);
}
```

```java
public void find() {

	
	Order order = em.find(Order.class, 1L);
	
	Member member = order.getMember();
	Product product = order.getProduct();
	
	System.out.println("member = "+ member.getName());
	System.out.println("product = "+ product.getName());
	System.out.println("orderAmount = "+ memberProduct.getOrderAmount());

}
```

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]
