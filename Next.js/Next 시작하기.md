# Next.js 시작하기

> 학습 출처 : [교재] 실전에서 바로 쓰는 Next.js [저자] : 미셸 리바 (Michele Riva)

Next.js는 리액트를 위해 만든 오픈소스 자바스크립트 웹 프레임워크

개발: Vercel

- 리액트를 확장한 다양한 기능 제공

    - 서버 사이드 렌더링(SSR: Server Side Rendering)
        > 페이지 요청이 발생할 때마다 사전 렌더링  
        > Fetching 요소 적용하여 렌더링
    
    - 정적 사이드 생성(SSG: Static Site Generation)
        > 미리 만들어 놓은 페이지를 서비스 하기 때문에 속도는 빠르나 한번 생성하고 나면 수정이 불가능하다.
    
    - 증분 정적 재생성(ISR: Increamental Static Regeneration)
        > SSG의 단점을 보완하였다.  
        > 이미 생성된 페이지를 일정 시간이 지난 후 다시 생성함(최신 데이터로 업데이트)

- CoC : Convention over Configuration
    - 개발자가 해야 할 결정의 수를 줄이면서, 유연성은 잃지 않도록 하는 설계 패러다임

### Pages Router vs App Router

- React로 개발하다 Next를 사용하면 제일 신기한 기능 Router
    
- 교재는 Next.js 13.1 버전이기에 Pages Router로 작성되어 있다.

- Next.js 13.4 부터 App Router가 stable하게 지원하기 시작했다.

- 필자는 App Router로 작성을 진행할 예정

#### [Pages Router]

- pages 디렉토리가 root, index.js가 index page

- about.js는 /about, team.js는 /team 경로로 라우팅

- 클라이언트 중심의 라우팅

#### [App Router]

- app 디렉토리가 root, page.js가 index page

- /about/page.js는 /about, /login/page.js는 /login 경로로 라우팅

- 서버 중심의 라우팅

- 번들 사이즈가 작다.  
    >※번들(bundle) : 의존성 관계가 있는 여러 파일을 하나로 결합하는 것

### Next.js 13 vs Next.js 14

- Pages Router => App Router

- Data Fetching : 

    - v13까지는 getServerSideProps, getStaticProps 메소드를 이용하여 구현
 
    - v14부터는 use 메서드를 이용하여 구현하거나, 컴포넌트 내부에서 비동기 함수(async)를 호출하고, await을 사용하여 데이터를 페칭할 수 있습니다

- Tubopack : webpack에서 Tubopack으로 변경

- Tubopack의 장점

    - 빠른 빌드 속도
 
    - 코드 스플리팅 및 성능 최적화
 
    -  번들링 과정 간소화

- 이미지 최적화 : 13까지는 **도구** 사용 / 14부터는 **자체적**으로 지원
    - lazy loading : 지연
    - 이미지 사이즈 최적화 : webp 변환
    - placeholder : 영역 확보

- 보안 강화 : XSS 공격에 대한 보호 기능이 강화, 보안 관련 헤더 설정을 더욱 쉽게 할 수 있다.
