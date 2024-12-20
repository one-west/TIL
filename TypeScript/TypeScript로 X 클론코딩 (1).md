# TypeScript로 X(구: 트위터) 클론 코딩하기 (1)

## Firebase 란?

> 이 문서는 Firebase를 소개하고자 하는 것이 아니기에 간략히 설명하겠음

- 서비스를 개발하면서 공통적으로 사용되는 것들 ex) 유저 인증, API, DB.. 등 서비스 종류만 달라질 뿐 절차와 기능이 비슷한 그런 기능들을 정형화해 서비스로 제공하는 것이 파이어베이스이다.

- 파이어베이스를 이용하면 서버 없이(서버리스) 개발이 가능

## Firebase 적용하기

```shell
npm i firebase
```

- 아래 코드는 Firebase 설정파일

```ts
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAuth } from "firebase/auth";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
const firebaseConfig = {
  apiKey: "여기 내용은 프로젝트마다 다름",
  authDomain: "여기 내용은 프로젝트마다 다름",
  projectId: "여기 내용은 프로젝트마다 다름",
  storageBucket: "여기 내용은 프로젝트마다 다름",
  messagingSenderId: "여기 내용은 프로젝트마다 다름",
  appId: "여기 내용은 프로젝트마다 다름",
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);

// 파이어베이스 인증 정보
export const auth = getAuth(app);
```

### 로그인 로직

```tsx
/* eslint-disable import/no-anonymous-default-export */
import { createUserWithEmailAndPassword, updateProfile } from "firebase/auth";
import { useState } from "react";
import styled from "styled-components";
import { auth } from "../firebaseConfig";
import { FirebaseError } from "firebase/app";
import { useNavigate } from "react-router-dom";

const Container = styled.div`
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 80%;
  max-width: 400px;
  padding: 30px;
`;

const Title = styled.h1`
  font-size: 25px;
  font-weight: bold;
`;

// 로고 이미지
// Text 입력 필드 구역
const Form = styled.form`
  display: flex;
  flex-direction: column;
  margin-top: 30px;
  gap: 10px;
`;

const Input = styled.input`
  // Text 입력 칸
  border-radius: 10px;
  border: none;
  padding: 5px 20px;
  &::placeholder {
    font-size: 10px;
  }
  &[type="submit"] {
    cursor: pointer;
  }
`;

const SubTitle = styled.p`
  // Text 입력 칸
  font-size: 10px;
`;

const SignupBtn = styled.div`
  padding: 10px 20px;
  border-radius: 20px;
  background-color: #4387c2;
  font-size: 10px;
  font-weight: 600;
  color: white;
  display: flex;
  justify-content: center;
  cursor: pointer;
  margin-top: 20px;
`;

const ErrorMsg = styled.div`
  display: flex;
  justify-content: center;
  margin: 5px 0px;
  color: tomato;
  font-size: 11px;
  font-weight: bold;
`;

export default () => {
  // 회원가입을 위한 Process 작성
  // Hook 생성 : 페이지 이동을 위한
  const navi = useNavigate();

  // A. 입력한 회원 정보를 저장(State)
  const [nickName, setNickName] = useState("");
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");

  // B. 입력한 회원 정보를 가공 / 수정
  const onChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    // 2. 입력한 정보(입력값, 입력위치)
    // const name = event.target.name;
    // const value = event.target.value;
    const {
      target: { name, value },
    } = event;
    // 1. 입력한 정보를 분류(닉네임, 이메일, 비번)
    switch (name) {
      // Goal : 각각 정보를 State(닉네임, 이메일, 비번) 지정
      case "nickname":
        setNickName(value);
        break;
      case "email":
        setEmail(value);
        break;
      case "password":
        setPassword(value);
        break;
      default:
        break;
    }
  };

  // C. 가입버튼을 누른 경우, 입력한 회원정보를 SERVER에 전달 > 회원가입 처리
  const onSubmit = async () => {
    console.log("가입하기 버튼 눌림");
    // A. 방어코드 -- ex)입력을 안한 경우
    if (nickName === "" || email === "" || password === "") {
      alert("회원 정보를 모두 입력해주세요");
    }
    // B. 회원가입 프로세스 진행
    try {
      // b-1. 로딩 start
      setLoading(true);
      // b-2. 회원정보(닉네임, 이메일, 암호)를 모아서 서버(Firebase)에 전달(API)
      // 가입완료까지 기다리기(비동기)
      const credential = await createUserWithEmailAndPassword(auth, email, password);
      await updateProfile(credential.user, {
        displayName: nickName, // displayName: firebase user profile에 저장할 name
      });
      navi("/");
      // b-3. 서버에서 가입 진행
      // b-4. 가입완료 > 1. 로그인화면 or 2. 자동 로그인
    } catch (error) {
      // C. 예외적인 경우(Error) 중복계정, 잘못된 계정
      // c-0. 만일 Firebase 관련 Error인 경우에만
      // c-1. 에러메세지 출력
      if (error instanceof FirebaseError) {
        setError(error.code);
      }
    } finally {
      // D. 로딩 종료
      // always 에러가 나든 안나든 실행
      setLoading(false);
    }
  };
  return (
    <Container>
      <Title>Signup Page.</Title>
      <Form>
        <SubTitle>이름*</SubTitle>
        <Input name="nickname" onChange={onChange} type="text" placeholder="예) Daelim" />
        <SubTitle>이메일*</SubTitle>
        <Input name="email" onChange={onChange} type="email" placeholder="예) Daelim@email.daelim.ac.kr" />
        <SubTitle>비밀번호*</SubTitle>
        <Input name="password" onChange={onChange} type="password" placeholder="예) 6자리 이상 입력하세요" />
        <SignupBtn onClick={onSubmit}>{loading ? "로딩중..." : "가입하기"}</SignupBtn>
        <ErrorMsg>{errorMsgGroups[error]}</ErrorMsg>
      </Form>
    </Container>
  );
};

interface errorMsgGroupType {
  [key: string]: string;
}

// Firebase의 에러 Code를 식별하여 사용자 정의 메시지를 출력
const errorMsgGroups: errorMsgGroupType = {
  "auth/email-already-in-use": "이미 존재하는 회원입니다.",
  "auth/week-password": "비밀번호를 6자리 이상 입력하세요",
  "auth/invalid-email": "잘못된 이메일입니다.",
};
```

### 학습출처

- 러닝 타입스크립트(Josh Goldberg)[한빛미디어][2023]
