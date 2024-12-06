# JPA 고급매핑

## 목차

- [상속 관계 매핑](#상속-관계-매핑)
- [@MappedSuperClass](#@MappedSuperClass)
- [복합 키와 식별 관계 매핑](#복합-키와-식별-관계-매핑)
- [조인 테이블](#조인-테이블)

## 상속 관계 매핑

- 관계형 데이터베이스에는 객체지향 언어에서 다루는 상속이라는 개념 없음.
- 대신 슈퍼타입 서브타입 관계 (Super-Type Sub-Type Relationship) 라는 모델링 기법이 객체의 상속 개념과 가장 유사

![상속 관계](https://github.com/user-attachments/assets/e9ac2089-1e51-4708-a42d-b42f007cf9ba)

- 슈퍼타입 서브타입 논리 모델을 실제 논리 모델인 테이블로 구현할 때는 3가지 방법
    - 각각의 테이블로 변환 : 각각을 모두 테이블로 만들고 조회할 때 조인을 사용 → 조인 전략
    - 통합 테이블로 변환 : 테이블을 하나만 사용해서 통합 → 단일 테이블 전략
    - 서브타입 테이블로 변환 : 서브 타입마다 하나의 테이블을 만든다 → 구현 클래스마다 테이블 전략

### 조인 전략

엔티티 각각을 모두 테이블로 만들고 자식 테이블이 부모 테이블의 기본 키를 받아서 기본 키 + 외래 키로 사용하는 전략

- 객체는 타입으로 구분할 수 있지만 테이블은 타입의 개념이 없다 → DTYPE 컬럼을 구분 컬럼으로 사용

![items 테이블 상속관계](https://github.com/user-attachments/assets/d7d1a983-34dd-4956-bdcd-dcc043ed978e)

```java
package org.example;

import javax.persistence.Column;
import javax.persistence.DiscriminatorColumn;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.Inheritance;
import javax.persistence.InheritanceType;

@Entity
@Inheritance(strategy = InheritanceType.JOINED)
@DiscriminatorColumn(name = "DTYPE")
public abstract class Item {

    @Id @GeneratedValue
    @Column(name = "ITEM_ID")
    private Long id;

    private String name;
    private int price;
}
```

```java
package org.example;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@DiscriminatorValue("A")
public class Album extends Item{

    private String artist;
}
```

```java
package org.example;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@DiscriminatorValue("M")
public class Movie extends Item{

    private String director;
    private String actor;
}
```

```java
package org.example;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@DiscriminatorValue("B")
@PrimaryKeyJoinColumn(name = "BOOK_ID")
public class Book extends Item{

    private String author;
    private String isbn;
}
```

- @Inheritance(strategy = InheritanceType.JOINED)
    - 상속 매핑시 부모 클래스에 @Inheritance 사용해야 함
    - 여기서는 조인 전략을 사용하므로 InheritanceType.JOINED

- @DiscriminatorColumn(name = "DTYPE")
    - 부모 클래스에 구분 컬럼 지정
    - 기본값이 DTYPE

- @DiscriminatorValue("A")
    - 엔티티를 저장할 때 구분 컬럼에 입력할 값을 지정

- @PrimaryKeyJoinColumn(name = "BOOK_ID")
    - 기본값으로 자식 테이블은 부모 테이블의 ID 컬럼명을 그대로 사용하는데, 만약 자식 테이블의 기본 키 컬럼명을 변경하고 싶으면 사용

### 장점

- 테이블이 정규화 된다.
- 외래 키 참조 무결성 제약 조건을 활용할 수 있다.
- 저장공간을 효율적으로 사용한다.

### 단점

- 조회할 때 조인이 많이 사용되므로 성능이 저하될 수 있다.
- 조회 쿼리가 복잡하다.
- 데이터를 등록할 INSERT SQL을 두 번 실행한다.

### 특징

- JPA 표준 명세는 구분 컬럼을 사용하도록 한다
- 하지만 하이버네이트를 포함한 몇 몇 구현체는 구분 컬럼 없이도 동작한다.

```java
public static void main(String[] args) {
        // 엔티티매니저팩토리 생성
        EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("jpa");
        // 엔티티매니저 생성
        EntityManager entityManager = entityManagerFactory.createEntityManager();
        // 트랜잭션 획득
        EntityTransaction entityTransaction = entityManager.getTransaction();

        try {
            entityTransaction.begin();

            Album album = new Album();
            album.setName("앨범1");
            album.setPrice(1000);
            album.setArtist("가수명");
            entityManager.persist(album);

            entityTransaction.commit();

        } catch (Exception e) {
            System.out.println(e.getMessage());
            entityTransaction.rollback();
        } finally {
            entityManager.close();
        }
        entityManagerFactory.close();
```

### 단일 테이블 전략 (Single-Table Strategy)

![단일 테이블](https://github.com/user-attachments/assets/00a392fd-5c0e-4815-95c9-636fc48162dc)

- 테이블을 하나만 사용
- 구분 컬럼(DTYPE) 으로 어떤 자식 데이터가 저장되었는지 구분한다.
- 조회할 때 조인을 사용하지 않으므로 일반적으로 가장 빠르다
- 자식 엔티티가 매핑한 컬럼은 모두 null을 허용 해야 한다.

```java
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
```

### 장점

- 조인이 필요 없으므로 일반적으로 조회 성능이 빠르다.
- 조회 쿼리가 단순하다.

### 단점

- 자식 엔티티가 매핑한 컬럼은 모두 null을 허용해야 한다.
- 단일 테이블에 모든 것을 저장하므로 테이블이 커질 수 있다. → 상황에 따라서는 조회 성능이 오히려 느려질 수 있다.

### 특징

- 구분 컬럼을 꼭 사용해야 한다.

### 구현 테이블마다 테이블 전략 (Table-per-Concrete-Class Strategy)

![구현 테이블마다 테이블](https://github.com/user-attachments/assets/0521fbb0-2fed-4779-aacb-b77bec7288d6)

- 자식 엔티티마다 테이블을 만든다.
- 일반적으로 추천하지 않는다.

```java
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
```

### 장점

- 서브 타입을 구분해서 처리할 때 효과적이다.
- not null 제약 조건을 사용할 수 있다.

### 단점

- 여러 자식 테이블을 함께 조회할 때 성능이 느리다
- 자식 테이블을 통합해서 쿼리하기 어렵다.

### 특징

- 구분 컬럼을 사용하지 않는다.

## @MappedSuperClass

부모 클래스는 테이블과 매핑 하지 않고 부모 클래스를 상속 받는 자식 클래스에게 매핑 정보만 제공하고 싶을 때 사용

@Entity는 실제 테이블과 매핑되지만 @MappedSuperClass는 실제 테이블과 매핑되지 않는다. → 단순 매핑 정보를 상속할 목적으로만 사용 

![공통 속성 분리 테이블](https://github.com/user-attachments/assets/34e1c7a0-77ec-4f36-95bd-8dccafbbaf9c)

```java
package org.example;

import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.MappedSuperclass;

@MappedSuperclass
public abstract class BaseEntity {

    @Id @GeneratedValue
    private Long id;
    private String name;
}
```

```java
package org.example;

import javax.persistence.Entity;

@Entity
public class Member extends BaseEntity{

    private String email;
}
```

```java
package org.example;

import javax.persistence.Entity;

@Entity
public class Seller extends BaseEntity{

    private String shopName;
}
```

- BaseEntity는 테이블과 매핑할 필요가 없고 자식 엔티티에게 공통으로 사용되는 매핑 정보만 제공
- 부모로 부터 물려받은 매핑정보를 재정의하렴면 @AttributeOverrides 나 @AttributeOverride
- 연관관계를 재정의하려면 @AssociationOverrides나 @AssociationOverride

```java
@AttributeOverride(name = "id", column = @Column(name = "MEMBER_ID"))
```

```java
@AttributeOverrides({
    @AttributeOverride(name = "id", column = @Column(name = "MEMBER_ID")),
    @AttributeOverride(name = "name", column = @Column(name = "MEMBER_NAME"))
})
```

- @MappedSuperClass로 지정한 클래스는 엔티티가 아니므로 em.find()나 JPQL에서 사용할 수 없다.
- 이 클래스를 직접 생성해서 사용할 일이 거의 없으므로 추상 클래스로 만드는 것을 권장

## 복합 키와 식별 관계 매핑

### 식별 관계 vs 비식별 관계

- 데이터베이스 테이블 사이에 관계는 외래 키가 기본 키에 포함되는지 여부에 따라 식별관계와 비식별관계로 구분

### 식별관계

- 부모 테이블의 기본 키를 내려받아서 자식 테이블의 기본 키 + 외래 키로 사용하는 관계

![식별관계](https://github.com/user-attachments/assets/17f76f90-fcf5-42c1-a115-9ef00a1698e8)

### 비식별관계

- 부모 테이블의 기본 키를 받아서 자식 테이블의 외래 키로만 사용하는 관계

![비식별관계](https://github.com/user-attachments/assets/02c5f8bc-2704-4f13-b2e2-7f0264c268db)

- 비식별 관계는 외래 키에 NULL을 허용하는지에 따라 필수적 비식별 관계와 선택적 비식별 관계로 나눈다.
- 필수적 비식별관계 (Mandatory) : 외래 키에 NULL을 허용 안함. 연관관계 필수
- 선택적 비식별관계 (Optional) : 외래 키에 NULL 허용.

### 복합 키 : 비식별 관계 매핑

- 기본 키를 구성하는 컬럼이 하나면 단순하게 매핑

```java
@Entity
public class Hello {
	@Id
	private String id;
}
```

- 둘 이상의 컬럼으로 구성된 복합키 → 별도의 식별자 클래스 필요

```java

// 이렇게 작성하면 에러남

@Entity
public class Hello {
	@Id
	private String id1;

	@Id
	private String id2; // 실행 시점에 매핑 예외 발생
}
```

- JPA는 영속성 컨텍스트에 엔티티를 보관할 때 엔티티의 식별자를 키로 사용
- 식별자를 구분하기 위해 equals와 hasCode를 사용해서 동등성 비교
- 식별자 필드가 2개 이상이면 별도의 식별자 클래스를 만들고 그곳에 equals와 hashCode 를 구현해야 한다.

### @IdClass

- 복합 키 테이블은 비식별 관계이고 PARENT 는 복합 기본 키를 사용

![@IdClass를 사용한 복합키 구현](https://github.com/user-attachments/assets/ce3854cd-340c-40a8-9662-bf40d2c3c658)

```java
package org.example;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.IdClass;

@Entity
@IdClass(ParentId.class)
public class Parent {

    @Id
    @Column(name = "PARENT_ID1")
    private String id1;

    @Id
    @Column(name = "PARENT_ID2")
    private String id2;
}
```

```java
package org.example;

import java.io.Serializable;
import java.util.Objects;

public class ParentId implements Serializable {

    private String id1;
    private String id2;
		

    public ParentId() {
    }

    public ParentId(String id1, String id2) {
        this.id1 = id1;
        this.id2 = id2;
    }

    @Override
    public boolean equals(Object o) {
        ...
    }

    @Override
    public int hashCode() {
        ...
    }
}
```

- 식별자 클래스 조건
    - 식별자 클래스의 속성명과 엔티티에서 사용하는 식별자의 속성명이 같아야 한다.
        - ParentId.id1, ParentId.id2 와 Parent.id1, Parent.id2
    - Serializable 인터페이스 구현
    - eqlaus, hashCode 구현
    - 기본 생성자가 있어야 한다.
    - 식별자 클래스는 public  이어야 한다.

```java
Parent parent = new Parent();
parent.setId1("myId1");
parent.setId2("myId2");
parent.setName("myName");
entityManager.persist(parent);

ParentId parentId = new ParentId("myId1", "myId2");
Parent findParent = entityManager.find(Parent.class, parentId);

System.out.println("findParent ::"+ findParent.getName());
```

### @EmbeddedId

@IdClass가 데이터베이스에 맞춘 방법이라면 @EmbeddedId는 좀 더 객체지향적인 방법

```java
package org.example;

import javax.persistence.EmbeddedId;
import javax.persistence.Entity;

@Entity
public class Parent {

    @EmbeddedId
    private ParentId parentId;

    public ParentId getParentId() {
        return parentId;
    }

    public void setParentId(ParentId parentId) {
        this.parentId = parentId;
    }

    private String name;

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

import java.io.Serializable;
import java.util.Objects;
import javax.persistence.Column;
import javax.persistence.Embeddable;

@Embeddable
public class ParentId implements Serializable {

    @Column(name = "PARENT_ID1")
    private String id1;
    @Column(name = "PARENT_ID2")
    private String id2;

    public ParentId() {
    }

    public ParentId(String id1, String id2) {
        this.id1 = id1;
        this.id2 = id2;
    }

    @Override
    public boolean equals(Object o) {
        ...
    }

    @Override
    public int hashCode() {
        ...
    }
}
```

- @EmbededId를 적용한 식별자 클래스 조건
    - @Embeddable 어노테이션을 붙여줘야 한다.
    - Serializable 인터페이스 구현
    - eqlaus, hashCode 구현
    - 기본 생성자가 있어야 한다.
    - 식별자 클래스는 public  이어야 한다.
    

### 복합 키 : 식별 관계 매핑

![식별 관계 매핑](https://github.com/user-attachments/assets/628fdf9a-4a15-4a02-9404-bc39d6cfd03a)

### @IdClass

```java
package org.example;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Parent {

    @Id @Column(name = "PARENT_ID")
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
@Entity
@IdClass(ChildId.class)
public class Child {

    @Id
    @ManyToOne
    @JoinColumn(name = "PARENT_ID")
    public Parent parent;

    @Id
    @Column(name = "CHILD_ID")
    private String childId;

    private String name;
}
```

```java
package org.example;

import java.io.Serializable;
import java.util.Objects;

public class ChildId implements Serializable {

    public String parent;
    private String childId;

    @Override
    public boolean equals(Object o) {
        ...
    }

    @Override
    public int hashCode() {
        ...
    }
}
```

```java
public class GrandChildId implements Serializable {

    private ChildId childId;
    private String id;

		@Override
    public boolean equals(Object o) {
        ...
    }

    @Override
    public int hashCode() {
        ...
    }
}
```

### @EmbeddedId

```java
@Entity
public class Parent {

    @Id @Column(name = "PARENT_ID")
    private String id;

    private String name;
}
```

```java
@Entity
public class Child {

    @EmbeddedId
    private ChildId id;

    @MapsId("parentId") // ChildId.parentId 매핑
    @ManyToOne
    @JoinColumn(name = "PARENT_ID")
    private Parent parent;

    private String name;
}
```

```java
@Embeddable
public class ChildId implements Serializable {

    public String parentId; // @MapsId("parentId")로 매핑

    @Column(name = "CHILD_ID")
    private String id;

		@Override
    public boolean equals(Object o) {
        ...
    }

    @Override
    public int hashCode() {
        ...
    }
}
```

```java
@Entity
public class GrandChild {

    @EmbeddedId
    private GrandChildId id;
    
    @MapsId("childId") // GrandChildId.childId 매핑
    @ManyToOne
    @JoinColumns({
        @JoinColumn(name = "PARENT_ID"),
        @JoinColumn(name = "CHILD_ID")
    })
    private Child child;

    private String name;
}
```

```java
@Embeddable
public class GrandChildId implements Serializable {

    private ChildId childId; // @MapsId("childId")

    @Column(name = "GRANDCHILD_ID")
    private String id;

		@Override
    public boolean equals(Object o) {
        ...
    }

    @Override
    public int hashCode() {
        ...
    }
}
```

- 특이점은 @Id 대신 @MapsId를 사용
- @MapsId 는 외래키와 매핑한 연관관계를 기본키에도 매핑하겠다는 뜻

## 조인 테이블

데이터베이스 테이블의 연관관계를 설계하는 방법은 크게 2가지

- 조인 컬럼 사용 (외래 키) : @JoinColumn 으로 매핑
- 조인 테이블 사용 (테이블 사용)
    - @JoinTable로 매핑
    - 주로 다대다 관계를 일대다, 다대일 관계로 풀어내기 위해 사용

### 일대일 조인 테이블

![일대일 조인 테이블](https://github.com/user-attachments/assets/ce946a7c-3ca9-4e17-943f-e6e3b2884349)

```java
@Entity
public class Parent {

   @Id @GeneratedValue
   @Column(name = "PARENT_ID")
   private Long id;

   private String name;

   @OneToOne
   @JoinTable(name = "PARENT_CHILD",
       joinColumns = @JoinColumn(name = "PARENT_ID"),
       inverseJoinColumns = @JoinColumn(name = "CHILD_ID"))
   private Child child;

}
```

```java
@Entity
public class Child {
  @Id @GeneratedValue
  @Column(name = "CHILD_ID")
  private Long id;
  private String name;
}
```

### 일대다 조인 테이블

![일대다 조인 테이블](https://github.com/user-attachments/assets/68a75782-0130-4953-afdf-190f99fef5c5)

```java
@Entity
public class Parent {

   @Id @GeneratedValue
   @Column(name = "PARENT_ID")
   private Long id;

   private String name;

   @OneToMany
   @JoinTable(name = "PARENT_CHILD",
       joinColumns = @JoinColumn(name = "PARENT_ID"),
       inverseJoinColumns = @JoinColumn(name = "CHILD_ID"))
   private List<Child> child = new ArrayList<Child>();

}
```

### 다대일 조인 테이블

다대일, 일대다 양방향 관계 → 다대일은 일대다에서 방향만 반대이다

```java
@Entity
public class Parent {

   @Id @GeneratedValue
   @Column(name = "PARENT_ID")
   private Long id;

   private String name;

   @OneToMany(mappedBy = "parent")
   private List<Child> child = new ArrayList<Child>();

}
```

```java
@Entity
public class Child {
  @Id @GeneratedValue
  @Column(name = "CHILD_ID")
  private Long id;
  private String name;

	@ManyToOne(optional = false)
	@JoinTable(name = "PARENT_CHILD",
       joinColumns = @JoinColumn(name = "CHILD_ID"),
       inverseJoinColumns = @JoinColumn(name = "PARENT_ID"))
	private Parent parent;
	
}
```

### 다대다 조인 테이블

![다대다 조인 테이블](https://github.com/user-attachments/assets/eb416f1b-5aed-4207-b895-6b44548b28e3)

```java
@Entity
public class Parent {

   @Id @GeneratedValue
   @Column(name = "PARENT_ID")
   private Long id;

   private String name;

   @ManyToMany
   @JoinTable(name = "PARENT_CHILD",
       joinColumns = @JoinColumn(name = "PARENT_ID"),
       inverseJoinColumns = @JoinColumn(name = "CHILD_ID"))
   private List<Child> child = new ArrayList<Child>();

}
```

```java
@Entity
public class Child {
  @Id @GeneratedValue
  @Column(name = "CHILD_ID")
  private Long id;
  private String name;
}
```

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]
