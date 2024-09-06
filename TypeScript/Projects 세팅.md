# Typescript 프로젝트 세팅

## Node.js 설치

- https://nodejs.org/en

## 프로젝트 생성

```shell
npx i create-react-app myproject --template typescript
```

### 트러블 슈팅

```shell
$ npx create-react-app daelimx --template typescript
npm error code ENOENT
npm error syscall lstat
npm error path C:\Users\user\AppData\Roaming\npm
npm error errno -4058
npm error enoent ENOENT: no such file or directory, lstat 'C:\
npm error enoent This is related to npm not being able to find
npm error enoent
npm error A complete log of this run can be found in: C:\Users
```

- 가끔 node.js 설치 시 `C:\Users\user\AppData\Roaming\npm` 해당 경로에 npm 폴더가 생성되지 않아 이러한 에러가 발생한다.

- 해당 경로에 npm 폴더 생성 후 명령어 재입력을 하니 해결

## 모듈 다운

```shell
npm i styled-components

npm i @types/styled-components

npm i react-router-dom
```

## 작성한 코드

```tsx
import React from 'react';
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
import styled from 'styled-components';
import Home from './screens/home';
import Profile from './screens/profile';
import Signin from './screens/signin';
import Signup from './screens/signup';

const Cotainer = styled.div`
  background-color: forestgreen;
  width: 300px;
  height: 300px;
`;

// React-Router-Dom 을 활용해 사이트의 Page를 관리
// - Page : home, profile, signin, signup
const router = createBrowserRouter([{
  path: "/",
  children: [{
    // home
    path: "",
    element: <Home />
  }, {
    // profile
    path: "profile",
    element: <Profile />
  }]
}, {
  // signin
  path: "/signin",
  element: <Signin />
}, {
  // signin
  path: "/signup",
  element: <Signup />
}
])

function App() {
  return (
    <div className="App">
      <Cotainer className="App-header">
        <RouterProvider router={router}></RouterProvider>
      </Cotainer>

    </div>
  );
}

export default App;
```

```tsx
import styled from "styled-components"

const Container = styled.div``;
const Title = styled.h1``;

export default () => {
    return <Container>
        <Title>Home Page.</Title>
    </Container>
}

// 참조해서 
```

### 참고 자료

- 대학 수업 중
