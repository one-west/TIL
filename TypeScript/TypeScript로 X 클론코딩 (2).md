# TypeScript로 X(구: 트위터) 클론코딩하기 (2)

[~ Firebase로 로그인 구현에서 이어짐](https://github.com/one-west/TIL/blob/main/TypeScript/Firebase%EB%A5%BC%20%EC%A0%81%EC%9A%A9%ED%95%98%EC%97%AC%20%EB%A1%9C%EA%B7%B8%EC%9D%B8.md)

## Loading Screen 구현

### 로딩 스피너의 중요성

- 웹 페이지나 웹 애플리케이션에서 사용자에게 컨텐츠가 로딩 중임을 시각적으로 알려주는 로딩 스피너는 사용자 경험(UX)을 향상시키는 데 중요한 역할을 한다.
- 특히, 네트워크 속도가 느릴 때 사용자는 페이지 로딩이 진행 중임을 알 수 있어야 한다.

### 코드  

#### loading-screen.tsx
```tsx
import styled, { keyframes } from "styled-components";

const Container = styled.div`
  height: 100vh;
  background-color: black;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
`;

// animation
const BouncdAnim = keyframes`
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
`;

// dot
const Dot = styled.div`
  width: 8px;
  height: 8px;
  background-color: white;
  border-radius: 50%;
  margin: 0px 4px;
  animation: ${BouncdAnim} 1s infinite ease-in-out;
  &:nth-child(1) {
    animation-delay: 0s;
  }
  &:nth-child(2) {
    animation-delay: 0.25s;
  }
  &:nth-child(3) {
    animation-delay: 0.5s;
  }
`;

export default function LoadingScreen() {
  return (
    <Container>
      <Dot />
      <Dot />
      <Dot />
    </Container>
  );
}
```

#### App.tsx
```tsx
import React, { useEffect, useState } from "react";
import { createBrowserRouter, Outlet, RouterProvider } from "react-router-dom";
import styled, { createGlobalStyle } from "styled-components";
import Home from "./screens/home";
import Profiler from "./screens/profiler";
import Signin from "./screens/signin";
import Signup from "./screens/signup";
import reset from "styled-reset";
import { auth } from "./firebaseConfig";
import LoadingScreen from "./screens/loading-screen";
import ProtectedRouter from "./component/protected-router";
import Layout from "./screens/layout";

// React-Router-Dom 을 활용해 사이트의 Page를 관리
// - Page : home, profile, signin, signup
const router = createBrowserRouter([
  {
    path: "/",
    element: (
      <ProtectedRouter>
        <Layout />
      </ProtectedRouter>
    ),
    children: [
      {
        // home
        path: "",
        element: (
          <ProtectedRouter>
            <Home />
          </ProtectedRouter>
        ),
      },
      {
        // profile
        path: "profile",
        element: (
          <ProtectedRouter>
            <Profiler />
          </ProtectedRouter>
        ),
      },
    ],
  },
  {
    // home
    path: "/home",
    element: (
      <ProtectedRouter>
        <Home />
      </ProtectedRouter>
    ),
  },
  {
    // signin
    path: "/signin",
    element: <Signin />,
  },
  {
    // signin
    path: "/signup",
    element: <Signup />,
  },
]);

const Container = styled.div`
  height: 100vh;
  display: flex;
  justify-content: center;
`;

// 공통적으로 전역에서 사용할 Global css style
// `안녕하세요 제 이름은 ${name}입니다.`;
const GlobalStyle = createGlobalStyle`
  ${reset}
  body {
    background-color: black;
    color: white;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }
`;

function App() {
  // 로그인 여부 파악을 위한 로딩
  const [loading, setLoading] = useState(true);
  // Server(Firebase)를 통해 현재 로그인한지 안 한지 확인
  // - Server API ... 속도의 차이 => 비동기형 함수

  const init = async () => {
    // 로딩 Start
    // Firebase 가 로그인 인증 여부 파악
    await auth.authStateReady();

    // 로딩 Finish
    setLoading(false);
  };

  // 실행 : 페이지가 렌더링될 때 (=접속했을 때) 실행되는 함수
  useEffect(() => {
    // 로그인 여부 파악(1번만)
    init();
  }, []);
  // Page Rendering Area
  // - A. 로그인을 한 경우 > Home화면으로 이동
  // - B. 로그인을 하지 않은 경우 > Login화면으로 이동
  // + C. 로딩하는 동안 보여줄 loading-screen 필요
  return loading ? (
    <LoadingScreen />
  ) : (
    <Container className="App">
      <GlobalStyle />
      <RouterProvider router={router} />
    </Container>
  );
}

export default App;
```

### 자료 및 소스코드

- 이미지
    - pixabay : https://pixabay.com/
    - Heroicons : https://heroicons.com/

- 전체 소스
    - https://github.com/one-west/TypeScript
