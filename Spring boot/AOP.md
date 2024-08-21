# AOP

> 학습 출처 : 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 [김영한]

## AOP가 필요한 상황

- 공통 관심 사항 vs 핵심 관심 사항

- 메서드의 호출 시간 측정 ,회원 가입 시간, 회원 조회 시간 측정 등 등

    <img src="https://velog.velcdn.com/images/gmltn9233/post/61fe902d-5897-41a9-a0af-fdfded9f5b56/image.png">

### AOP 적용 전

```java
   /**
     * 회원 가입
     */
    public Long join(Member member) {
        
        long start = System.currentTimeMillis();
        
        try {
            validateDuplicateMember(member); // 같은 이름이 있는 중복 회원X
            memberRepository.save(member);
            return member.getId();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("join = " + timeMs + " ms");
        }
        
    }
    
    /**
     * 전체 회원 조회
     */
    public List<Member> findMembers() {

        long start = System.currentTimeMillis();

        try {
            return memberRepository.findAll();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("join = " + timeMs + " ms");
        }  
    }
```

**문제**

- 회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아님

- 시간을 측정하는 로직은 공통 관심 사항

- 시간을 측정하는 로직과 핵심 비즈니스 로직이 섞여 유지보수가 어려움

- 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어려움

- 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야하는 상황이 생길 수 있음

### AOP 적용 후

- AOP: Aspect Oriented Programming

- 공통 관심 사항(cross-cutting concern) vs 핵심 사항(core concern) 분리

<img src="https://velog.velcdn.com/images/gmltn9233/post/1c342f36-514c-488c-9c52-f94496cb6c05/image.png">
 
```java
package com.example.demo.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class TimeTraceAop {
    
    @Around("execution(* com.example.demo..*(..))")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();
        System.out.println("START: " + joinPoint.toString());
        try {
            return joinPoint.proceed();
        } finally {
            long endTime = System.currentTimeMillis();
            long timeMs = endTime - startTime;
            System.out.println("END: " + joinPoint.toString() + " TIME: " + timeMs + " ms");
        }
    }
}
```

- **해결**

- 회원가입, 회원 조회 등 핵심 관심사항과 시간을 측정하는 공통 관심 사항을 분리

- 시간을 측정하는 로직을 별도의 공통 로직으로 만듬

- 핵심 관심 사항을 깔끔하게 유지

- 변경이 필요하면 이 로직만 변경하면 됨

- 원하는 적용 대상을 선택할 수 있다.

### 스프링 AOP 동작 방식

<img src="https://velog.velcdn.com/images/gmltn9233/post/3ea2a131-4d4e-4682-80f0-5d86e1d2449d/image.png">

<img src="https://velog.velcdn.com/images/gmltn9233/post/801006fd-9d95-43c3-bf03-d403c4649f00/image.png">

<img src="https://velog.velcdn.com/images/gmltn9233/post/d4096995-00da-41c4-88f9-c77502eec707/image.png">
