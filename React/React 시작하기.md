# React란?

- 리액트는 무엇인가?
  > 사용자 인터페이스를 만들기 위한 자바스크립트 라이브러리.

- 다양한 JavaScript UI Framworks
  - React.js
  - Express
  - Next.js
  - Vue.js
  - Anguler
  - Node.js
  - Svelte
  - NestJS
  - NuxtJS
  - Gatsby

- 리액트 개념 정리
    - 복잡한 사이트를 쉽고 빠르게 만들고, 관리하기 위해 만들어진 것이 바로 리액트
    - 다른 표현으로는 SPA를 쉽고 빠르게 만들 수 있도록 해주는 도구

## 리액트의 장점
  - 빠른 업데이트와 렌더링 속도
    - Virtual DOM 방식
      - DOM ( Document Object Model ) 이란 XML, HTML 문서의 각 항목을 계층으로 표현하여 생성, 변형, 삭제할 수 있도록 돕는 인터페이스. W3C의 표준이다.
      - Virtual DOM은 DOM 조작이 비효율적인 이유로 속도가 느리기 때문에 고안된 방법
      - DOM은 동기식, Virtual DOM 비동기식 방법으로 렌더링

      ![Virtual DOM 렌더링](https://velog.velcdn.com/images/dongjun187/post/d87f9425-b92b-4a96-8a53-d55fd87dbbc6/image.png)
      출처 : https://velog.io/@dongjun187/React-Virtual-DOM-%EA%B8%B0%EC%B4%88-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC
        
      - 브라우저의 동작 원리 : Gecko와 유사하다
      ![웹킷 동작 과정](https://d2.naver.com/content/images/2015/06/helloworld-59361-3.png)  
      출처 : https://d2.naver.com/helloworld/59361

  - 컴포넌트 기반 구조
    - 리액트의 모든 페이지는 컴포넌트로 구성
    - 하나의 컴포넌트는 다른 여러개의 컴포넌트의 조합으로 구성
    - 리액트로 개발을 하다보면 레고 블록을 조립하는 것처럼 컴포넌트를 조합해서 웹사이트를 개발하게 됨
    - 에어비앤비 사이트 화면의 컴포넌트 구조

  - 재사용성
    - 반복적인 작업을 줄여주기 때문에 생선성을 높여준다.
    - 유지보수가 용이하다.
    - 재사용이 가능 하려면 해당 모듈의 의존성이 없어야함
  
  - 모바일 앱 개발가능
    - 리액트 네이티브라는 모바일 환경 UI프레임워크를 사용하면 크로스 플랫폼 모바일앱을 개발할 수 있음

## 리액트의 단점
  - 방대한 학습량
    - 자바스크립트를 공부한 경우 빠르게 학습할 수 있다.

  - 높은 상태 관리 복잡도
    - state, component life cycle 등의 개념이 있지만 그리 어렵지 않다.

- 리액트 설치
  - 터미널에 npx create-react-app 폴더명
  - 오류 발생 시 조치사항
    - npm 버전이 최신인지 확인 `npm -v`
    - npm 최신버전 설치 후 실행 `npm install -g npm@latest`
    - npm 삭제 후 재설치
        ```
        npm uninstall -g create-react-app
        npm install -g create-react-app
        ```
    - npm 캐시 삭제 후 실행
        ```
        npm cache clean --force
        npm install
        ```

### 자료출처
- https://react.dev/learn
- [교재]Do it! 리액트 모던 웹 개발 with 타입스크립트
