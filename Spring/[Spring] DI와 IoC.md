# DI 개념
> **DI (Dependency Injection) : 의존성 주입**

의존성 주입이란 필요한 (의존하는) 객체를 직접 생성하지 않고 외부에서 주입하는 방식 

A 객체가 B 객체를 이용한다는 것은 A객체가 B객체에 의존한다.

![DI](https://images.velog.io/images/gillog/post/08489bda-549e-4dae-851b-8ae1734bf85e/21373937580AEF9B37.jpg)
# 계산기 프로그램으로 DI 방식 이해

### MyCalculator.java
- ICalculator.java

```java
public interface ICalculator {

    public int doOperation(int firstNum, int secondNum);

}
```

- CalAdd.java

```java
public class CalAdd implements ICalculator{

    @Override
    public int doOperation(int firstNum, int secondNum) {
        return firstNum + secondNum;
    }
}
```

- CalSub.java

```java
public class CalSub implements ICalculator{

    @Override
    public int doOperation(int firstNum, int secondNum) {
        return firstNum - secondNum;
    }
}
```

- CalMul.java

```java
public class CalMul implements ICalculator{

    @Override
    public int doOperation(int firstNum, int secondNum) {
        return firstNum * secondNum;
    }
}
```

- CalDiv.java

```java
public class CalDiv implements ICalculator{

    @Override
    public int doOperation(int firstNum, int secondNum) {
        return firstNum / secondNum;
    }
}
```

### 인터페이스를 활용

```java
public class MyCalculator {

//    public void calAdd(int fNum, int sNum, CalAdd calAdd) {
//        int value = calAdd.doOperation(fNum, sNum);
//        System.out.println("result : "+ value);
//    }
//    public void calSub(int fNum, int sNum, CalSub calSub) {
//        int value = calSub.doOperation(fNum, sNum);
//        System.out.println("result : " + value);
//    }
//
//    public void calMul(int fNum, int sNum, CalMul calMul) {
//        int value = calMul.doOperation(fNum, sNum);
//        System.out.println("result : " + value);
//    }
//
//    public void calDiv(int fNum, int sNum, CalDiv calDiv) {
//        int value = calDiv.doOperation(fNum, sNum);
//        System.out.println("result : " + value);
//    }

    public void calculator(int fNum, int sNum, ICalculator calculator) {
        int value = calculator.doOperation(fNum, sNum);
        System.out.println("result : "+ value);
    }
}
```

### MainClass.java

```java
public class MainClass {

    public static void main(String[] args) {

        MyCalculator calculator = new MyCalculator();
        calculator.calAdd(10,5, new CalAdd()); // 객체 주입
        calculator.calSub(10,5, new CalSub());
        calculator.calMul(10,5, new CalMul());
        calculator.calDiv(10,5, new CalDiv());
    }

}
```

📌 필요한 객체를 외부에서 주입한다 → 프로그램의 유연성을 확보하고 객체 간의 결합도를 낮출 수 있게 해준다.

# IoC 개념

> IoC (Inversion of Control) : 제어의 역전

프로그램의 제어권을 개발자가 컨트롤 하는 것이 아니라 외부에서 컨트롤하는 방식

![IoC](https://velog.velcdn.com/images%2Fgillog%2Fpost%2F41f2eb24-fce2-4b7e-b9ac-d5c3ce97d213%2F22535642580C4AF12C.jpg)

# 계산기 프로그램으로 IoC방식 이해

## CalAssembler 클래스

프로그램 실행에 필요한 객체를 생성하는 클래스 코드 작성

```java
public class CalAssembler {

    MyCalculator calculator;
    CalAdd calAdd;
    CalSub calSub;
    CalMul calMul;
    CalDiv calDiv;

    public CalAssembler() {
        calculator = new MyCalculator();
        calAdd = new CalAdd();
        calSub = new CalSub();
        calMul = new CalMul();
        calDiv = new CalDiv();
    }

    public void assemble() {
        calculator.calculator(10, 5, calAdd);
        calculator.calculator(10, 5, calSub);
        calculator.calculator(10, 5, calMul);
        calculator.calculator(10, 5, calDiv);
    }
}

```

```java
public class MainClass {

    public static void main(String[] args) {
        new CalAssembler().assemble();
    }

}
```

- 객체를 생성하고 조립하는 특별한 공간을 스프링에서는 IoC컨테이너라고 한다.
- IoC 컨테이너의 객체를 스프링에서는 `빈(Bean)` 이라고 한다.  

→ 스프링의 IoC 컨테이너는 빈을 생성하고 필요한 곳에 주입(DI)하는 특별한 공간

💡 스프링은 IoC 컨테이너에서 프로그램에 필요한 객체를 싱글턴 형태로 만든다.  
  → 빈은 한번 쓰고 버려지는 것이 아니라 계속 재사용된다.


# 자료 출처
https://velog.io/@gillog/Spring-DIDependency-Injection  
https://docs.spring.io/spring-framework/reference/index.html  
