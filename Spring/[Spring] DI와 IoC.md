# DI 개념
> **DI (Dependency Injection) : 의존성 주입**

의존성 주입이란 필요한 (의존하는) 객체를 직접 생성하지 않고 외부에서 주입하는 방식 

new CallAdd(), new CalSub(), new CalMul(), new CalDiv() 는 메서드 실행헤 필요한 객체를 직접생성

→ MyCalculator는 자신이 직접 연산하지 않고 각각의 연산 객체들에게 연산 업무를 전달

→ MyCalculator 는 CalAdd에 의존한다.

A 객체가 B 객체를 이용한다는 것은 A객체가 B객체에 의존한다.

![DI](https://file.notion.so/f/f/9b26350c-aa9b-4c90-88a3-2b752d5d66dd/9c24e3cc-cc6d-488d-828a-ec2a91665c48/Untitled.png?id=6733e26c-b0ac-41a8-8bd1-3c6f076f708c&table=block&spaceId=9b26350c-aa9b-4c90-88a3-2b752d5d66dd&expirationTimestamp=1710633600000&signature=BWhBnICKxYTak30zNuYAmlfECiU4VrDpCnRcqbLTFHU&downloadName=Untitled.png)
