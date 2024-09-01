# SOLID (객체 지향 설계)

로버트 C. 마틴이 2000년대 초반에 명명한 객체 지향 프로그래밍 및 설계의 다섯 가지 기본 원칙

프로그래머가 시간이 지나도 유지 보수와 확장성을 높이는데 중요한 개념이다.

불필요한 복잡성을 제거해  리팩토링에 소요되는 시간을 줄임으로써 프로젝트 개발의 생산성을 높일 수 있다.

## 의존 역전 원칙 - DIP (Dependency Inversion Principle)

- 어떤 클래스를 참조해서 사용해야하는 상황이 생긴다면, 해당 클래스를 직접 참조하는 것이 아니라 그 대상의 상위 요소(추상 클래스 or 인터페이스)로 참조하라는 원칙

- 규칙
    - 상위 모듈은 하위 모듈에 의존해서는 안된다. 상위 모듈과 하위 모듈 모두 추상화에 의존해야 한다.
    - 추상화는 세부 사항에 의존해서는 안된다. 세부사항이 추상화에 의존해야 한다.

- DIP가 지켜지지 않았을 때 하위 모듈의 구체적인 내용에 클라이언트가 의존하게 되어 하위 모듈에 변화가 있을 때마다 클라이언트나 상위 모듈의 코드를 자주 수정하게 된다.

## Ex

```java
public class Hat {
    private final String name;
    private final int price;

    public Hat(String name, int price) {
        this.name = name;
        this.price = price;
    }

    public int payment() {
        return price;
    }
}

public class Shoes {
    // ...
}

public class Pants {
    // ...
}

public class Shirts {
    // ...
}
```

```java
public class Human {
    private final String name;
    private final int money;
    Hat accessory; // 의존 저수준 객체

    public Human(String name, int money, Hat accessory) {
        this.name = name;
        this.money = money;
        this.accessory = accessory;
    }

    public int payment() {
        return accessory.payment(); // 의존 객체에서 메서드를 실행
    }

    public void chageAccessory(Hat accessory) {
        this.accessory = accessory;
    }

    public void getInfo() {
        System.out.println("이름: " + name);
        System.out.println("가격: " + money);
        System.out.println("장신구: " + accessory);
    }
}
```

- 위 코드의 문제는 이미 완전하게 구현된 하위 모듈 `Hat`을 의존하고 있다는 점이다.

### 수정 후

```java
// 고수준 모듈
public interface Accessories {
    int payment();
}

public class Hat implements Accessories {
    private final String name;
    private final int money;

    public Hat(String name, int money) {
        this.name = name;
        this.money = money;
    }

    public int payment() {
        return money;
    }
}

public class Shoes implements Accessories {
	// ...
}


public class Pants implements Accessories {
	// ...
}

public class Shirts implements Accessories {
	// ...
}
```

```java
public class Human {
    private final String name;
    private final int money;
    private Accessories accessory; // 의존 고수준 객체

    public Human(String name, int money, Accessories accessory) {
        this.name = name;
        this.money = money;
        this.accessory = accessory;
    }

    public int payment() {
        return accessory.payment();
    }

    public void chageAccessory(Accessories accessory) {
        this.accessory = accessory;
    }

    public void getInfo() {
        System.out.println("이름: " + name);
        System.out.println("가격: " + money);
        System.out.println("장신구: " + accessory);
    }
}
```

### 참고 자료

- https://ko.wikipedia.org/wiki/%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84_%EC%97%AD%EC%A0%84_%EC%9B%90%EC%B9%99

- https://yozm.wishket.com/magazine/detail/2479/

- https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%EC%84%A4%EA%B3%84%EC%9D%98-5%EA%B0%80%EC%A7%80-%EC%9B%90%EC%B9%99-SOLID
