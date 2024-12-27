# Next Props 관리

## Props Drilling 이란?

> Props Drilling은 props를 오로지 하위 컴포넌트로 전달하는 용도로만 쓰이는 컴포넌트들을 거치면서 React Component 트리의 한 부분에서 다른 부분으로 데이터를 전달하는 과정을 말한다.

### Props의 흐름

- Next.js의 데이터 흐름은 단방향으로 이루어 짐

- 즉, parents에서 child component의 방향으로 props의 흐름이 이루어짐

- 계층 구조가 복잡해지면 Props Drilling 문제가 발생

- Props Drilling은 여러 개의 component를 지나 props가 전달 되면서 발생하는 문제

### Props Drilling의 문제

- 중간 컴포넌트에 불필요한 프로퍼티 전달

- 프로퍼티 데이터 형식 변경의 불편함

- 누락된 프로퍼티 인지의 어려움

- 프로퍼티 이름 변경 추적의 어려움

- 코드의 가독성, 코드의 유지보수가 어렵다

![Prop Drilling](https://camo.githubusercontent.com/4ac4257da5fd712840df56fa36fd0ec439e35a166439778ee7a6042cf057bfc2/68747470733a2f2f626c6f672e6a6f7368736f6674776172652e636f6d2f77702d636f6e74656e742f75706c6f6164732f323032342f30352f70726f702d6472696c6c696e672d76732e2d72656163742d636f6e746578742e6a70673f773d31303234)

## Prop drilling 해결하는 방법

### Context API

- 일반적으로 props는 부모에서 자식으로 전달되는 단방향 통신이지만, React의 Context API를 사용하여 데이터를 전역적으로 공유할 수 있다.

- Context를 생성하고 값을 제공하는 컴포넌트를 작성한 다음, 필요한 컴포넌트에서 useContext 훅을 사용하여 해당 값을 직접 접근할 수 있음

    > 이를 통해 중간 컴포넌트를 거치지 않고도 데이터를 전달할 수 있다.

- React는 16.3 버전부터 정식적으로 context api를 지원

- Context API는 createContext, Provider, useContext 개념만 알면 적용이 가능

|특징|Consumer|useContext|
|--|--|--|
|사용 방식|렌더 프로프 패턴을 사용 (Context.Consumer)|훅을 사용 (useContext)|
|컴포넌트 유형|클래스형 및 함수형 컴포넌트 모두 사용 가능|함수형 컴포넌트에서만 사용 가능|
|코드 가독성|다소 복잡하고 중첩되는 구조가 될 수 있음|코드가 간결하고 직관적|
|컨텍스트 값 접근|`Context.Consumer` 내부에서 콜백 함수로 값 접근|`useContext(Context)`로 직접 값 접근|
|리렌더링|컨텍스트 값이 변경되면 Consumer가 포함된 컴포넌트만 리렌더링|컨텍스트 값이 변경되면 해당 훅을 사용하는 컴포넌트만 리렌더링|

### Redux

- Slice

    - Redux Toolkit에서 사용되는 용어로, 특정 기능과 관련된 상태와 Reducer를 한 곳에서 정의하는 곳

- Provider

    - Redux Provider는 Redux의 상태 등을 공급하기 위한 파일

    - Provider를 사용하고자 하는 Page에서 사용하면 됨

    - 다만 전역적으로 사용할 때는 layout 파일에 정의하면 를 사용해야 하기 때문에 별도위 컴포넌트를 만들어서 사용하는 것이 좋음

### 예시 코드 (다크모드 / 라이트모드)

- src/contexts/ThemeContext.jsx

```jsx
"use client";

import { createContext, useEffect, useState } from "react";

export const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");

  useEffect(() => {
    const initialTheme = theme === "light" ? "light" : "dark";
    setTheme(initialTheme);
    document.body.className = initialTheme;
  }, []);

  const toggleTheme = () => {
    const newTheme = theme === "light" ? "dark" : "light";
    setTheme(newTheme);
    document.body.className = newTheme;
  };

  return <ThemeContext.Provider value={{ theme, toggleTheme }}>{children}</ThemeContext.Provider>;
};
src/components/ThemeToggleButton.jsx
"use client";

import { ThemeContext } from "@/contexts/ThemeContext";
import { useContext } from "react";

const ThemeToggleButton = () => {
  const { theme, toggleTheme } = useContext(ThemeContext); // ThemeContext로부터 값 가져오기

  return <button onClick={toggleTheme}>{theme === "light" ? "Dark Mode" : "Light Mode"}</button>;
};

export default ThemeToggleButton;
src/app/layout.tsx
import ThemeToggleButton from "@/components/ThemeToggleButton";
import { ThemeProvider } from "@/contexts/ThemeContext";
import "@/app/styles/global.css";

/* ...생략 */

export default function RootLayout(props: { children: React.ReactNode }) {
  const { children } = props;
  return (
    <html lang="ko" suppressHydrationWarning className={`${geistMono.variable} ${geistSans.variable}`}>
      <body>
        <ThemeProvider>
          <ThemeToggleButton />
          {children}
        </ThemeProvider>
      </body>
    </html>
  );
}
```

### Context API 와 Redux 비교

- **Context API**

    - React에서 기본으로 제공하는 상태 관리 도구로, 외부 라이브러리 설치 없이 사용 가능

    - Context API는 주로 전역 상태를 관리하는 데 사용

    - React.createContext()로 생성한 Context 객체와 Provider

    - 장점
 
        - 내장된 기능
      
        - 간단한 설정
      
        - 컴포넌트 단위 상태 관리
      
        - 경량

    - 단점

        - 리렌더링 문제
        
        - 구조적 복잡성
        
        - 디버깅 도구 부족

- **Redux**

  - Redux는 전역 상태를 관리하기 위한 독립적인 state 관리 라이브러리

  - 상태의 변경을 예측 가능하게 하고, 전역 state 관리를 더 구조적으로 지원

  - store, reducer, action 등의 개념을 사용해 state와 state dispatch를 관리

  - 장점

    - 명확한 상태 관리 구조

    - 미들웨어 지원

    - 디버깅 도구

    - 모든 프레임워크와 호환

  - 단점

    - 설정 코드의 복잡도

    - 추가 라이브러리 필요

    - 작은 애플리케이션에는 과한 설정
