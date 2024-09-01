# SOLID (객체 지향 설계)

로버트 C. 마틴이 2000년대 초반에 명명한 객체 지향 프로그래밍 및 설계의 다섯 가지 기본 원칙

프로그래머가 시간이 지나도 유지 보수와 확장성을 높이는데 중요한 개념이다.

불필요한 복잡성을 제거해  리팩토링에 소요되는 시간을 줄임으로써 프로젝트 개발의 생산성을 높일 수 있다.

## 인터페이스 분리 원칙 - ISP (Interface Segregation Principle)

- 클라이언트가 자신이 이용하지 않는 메서드에 의존하지 않아야 한다는 원칙이다.

- 인터페이스 분리 원칙은 큰 덩어리의 인터페이스들을 구체적이고 작은 단위들로 분리시킴으로써 클라이언트들이 꼭 필요한 메서드들만 이용할 수 있게 한다.

- SRP 원칙이 클래스의 단일 책임을 강조한다면, ISP는 인터페이스의 단일 책임을 강조한다고 보면 될 것 같다.

- 인터페이스 분리 원칙을 통해 시스템의 내부 의존성을 약화시켜 리팩토링, 수정, 재배포를 쉽게 할 수 있다.

- ISP 원칙의 주의해야 할점은 한번 인터페이스를 분리하여 구성해놓고 나중에 수정사항이 생겨 또 인터페이스들을 분리하는 행위를 지양해야 한다.
    > 처음에 설계 시 기능의 변화를 염두해두고 인터페이스를 설계해야 한다.  
    > 그러나 이 부분은 개발자의 역량이 중요한 것 같다. (경험과 시간을 들여서...)

## Ex

```java
public interface ISmartPhone {
    void call(String number); // 통화 기능
    void message(String number, String text); // 문제 메세지 전송 기능
    void wirelessCharge(); // 무선 충전 기능
    void AR(); // 증강 현실(AR) 기능
    void biometrics(); // 생체 인식 기능
}
```

```java
public class S20 implements ISmartPhone {
    public void call(String number) {
    }

    public void message(String number, String text) {
    }

    public void wirelessCharge() {
    }

    public void AR() {
    }

    public void biometrics() {
    }
}

public class S21 implements ISmartPhone {
    public void call(String number) {
    }

    public void message(String number, String text) {
    }

    public void wirelessCharge() {
    }

    public void AR() {
    }

    public void biometrics() {
    }
}
```

- 위 코드를 보면 최신 스마트폰 기종은 객체의 동작 모두가 필요하므로 ISP 원칙을 만족하지만, 구형 기종 스마트폰 클래스도 다뤄야 할 경우 문제가 생긴다.

```java
public class S3 implements ISmartPhone {
    public void call(String number) {
    }

    public void message(String number, String text) {
    }

    public void wirelessCharge() {
        System.out.println("지원 하지 않는 기능 입니다.");
    }

    public void AR() {
        System.out.println("지원 하지 않는 기능 입니다.");
    }

    public void biometrics() {
        System.out.println("지원 하지 않는 기능 입니다.");
    }
}
```

- S3 클래스는 무선 충전, 생체인식과 같은 최신 기능들을 지원하지 않지만, 추상 메서드 구현 규칙 때문에 필요하지도 않은 기능을 어쩔수없이 구현해야하는 낭비가 발생하였다.

### 수정 후

- 각각의 기능에 맞게 인터페이스를 잘게 분리하였다.

```java
public interface IPhone {
    void call(String number); // 통화 기능
    void message(String number, String text); // 문제 메세지 전송 기능
}

public interface WirelessChargable {
    void wirelessCharge(); // 무선 충전 기능
}

public interface ARable {
    void AR(); // 증강 현실(AR) 기능
}

public interface Biometricsable {
    void biometrics(); // 생체 인식 기능
}
```
```java
public class S21 implements IPhone, WirelessChargable, ARable, Biometricsable {
    public void call(String number) {
    }

    public void message(String number, String text) {
    }

    public void wirelessCharge() {
    }

    public void AR() {
    }

    public void biometrics() {
    }
}

public class S3 implements IPhone {
    public void call(String number) {
    }

    public void message(String number, String text) {
    }
}
```

- 분리된 인터페이스를 클래스가 지원되는 기능만을 골라 implements 하면 ISP 원칙이 지켜지게 된다.

### 참고 자료

- https://ko.wikipedia.org/wiki/%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4_%EB%B6%84%EB%A6%AC_%EC%9B%90%EC%B9%99

- https://yozm.wishket.com/magazine/detail/2479/

- https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%EC%84%A4%EA%B3%84%EC%9D%98-5%EA%B0%80%EC%A7%80-%EC%9B%90%EC%B9%99-SOLID
