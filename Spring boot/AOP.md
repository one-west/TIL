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

```java

```
