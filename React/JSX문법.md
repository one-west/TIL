# JSX

>  JSX란 Javascript Syntax eXtension 즉 자바스크립트를 확장한 문법이다.

-  React에서는 본질적으로 렌더링 로직이 UI 로직(이벤트가 처리되는 방식, 시간에 따라 state가 변하는 방식, 화면에 표시하기 위해 데이터가 준비되는 방식 등)과 연결된다.
 
-  React는 JSX 사용이 필수가 아니지만, 대부분 JavaScript 코드 안에서 UI 관련 작업을 할 때 JSX를 사용한다.  

-  또한 React가 더욱 도움이 되는 에러 및 경고 메시지를 표시할 수 있게 해줌.  

<br>

## JSX의 역할

- JSX는 내부적으로 XML/HTML 코드를 자바스크립트로 변환

- React가 createElement함수를 사용하여 자동으로 스크립트로 변환해 줌

- JS로 작업할 경우 직접 createElement함수를 사용해야한다.

- 결국 JSX는 가독성을 높여주는 역할을 한다.

<br>

## JSX의 장점

- 코드가 간결하다.

- 가독성이 좋아 유지보수가 용이하다.

- Injection Attact 해킹방법을 방어함으로써 보안에 강하다.

<br>

## JSX 사용법

- 모든 자바스크립트 문법을 지원한다.

- 자바스크립트 문법에 XML과 HTML을 섞어서 사용한다.

- HTML이나 XML에 자바스크립트 코드를 사용하고 싶으면 {}중괄호를 사용한다.

  ### JSX에 표현식 포함하기

  - JSX의 {} 안에는 유효한 모든 JavaScript 표현식을 넣을 수 있다.  

  - 예를 들어 2 + 2, user.firstName 또는 formatName(user) 등은 모두 유효한 JavaScript 표현식이다.

  - 아래 예시에서는 JavaScript 함수 호출의 결과인 formatName(user)을 `<h1>` Element에 포함했습니다.

  ```jsx
  function formatName(user) {
    return user.firstName + ' ' + user.lastName;
  }

  const user = {
    firstName: 'Harper',
    lastName: 'Perez'
  };

  const element = (
    <h1>Hello, {formatName(user)}!</h1>
  );
  ```

  ### JSX도 표현식이다

  - 컴파일이 끝나면, JSX 표현식이 정규 JavaScript 함수 호출이 되고 JavaScript 객체로 인식된다.

  - 즉, JSX를 if 구문 및 for loop 안에 사용하고, 변수에 할당하고, 인자로서 받아들이고, 함수로부터 반환할 수 있다.

  ```jsx
  function getGreeting(user) {
    if (user) {
      return <h1>Hello, {formatName(user)}!</h1>;
    }
    return <h1>Hello, Stranger.</h1>;
  }
  ```
  
  ### JSX 속성 정의

  - 속성에 따옴표를 이용해 문자열 리터럴을 정의.

  ```jsx
  const element = <a href="https://www.reactjs.org"> link </a>;
  // 중괄호를 사용하여 어트리뷰트에 JavaScript 표현식을 삽입할 수도 있음.

  const element = <img src={user.avatarUrl}></img>;
  // 속성에 JavaScript 표현식을 삽입할 때 중괄호 주변에 따옴표를 입력X.
  // 따옴표 또는 중괄호 중 하나만 사용하고, 동일한 속성에 두 가지를 동시에 사용하면 안됨.
  ```

  > **경고**  
  > JSX는 HTML보다는 JavaScript에 가깝기 때문에, React DOM은 HTML 속성 이름 대신 camelCase 프로퍼티 명명 규칙을 사용.
  > 예를 들어, JSX에서 class는 className가 되고 tabindex는 tabIndex가 된다.

  <br>

  ### JSX로 자식 정의
  - 태그가 비어있다면 XML처럼 /> 를 이용해 바로 닫아줄 수 있다.

  ```jsx
  const element = <img src={user.avatarUrl} />;
  // JSX 태그는 자식을 포함할 수 있음.

  const element = (
    <div>
      <h1>Hello!</h1>
      <h2>Good to see you here.</h2>
    </div>
  );
  ```

  ### JSX는 주입 공격을 방지한다.

  - JSX에 사용자 입력을 삽입하는 것은 안전하다.

  ```jsx
  const title = response.potentiallyMaliciousInput;
  // 출력안되지롱~ㅎㅎ
  const element = <h1>{title}</h1>;
  ```

  - 기본적으로 React DOM은 JSX에 삽입된 모든 값을 렌더링하기 전에 이스케이프 하므로, 애플리케이션에서 명시적으로 작성되지 않은 내용은 주입되지 않는다.

  - 모든 항목은 렌더링 되기 전에 문자열로 변환된다. 이런 특성으로 인해 XSS (cross-site-scripting) 공격을 방지할 수 있다.

  ### JSX는 객체를 표현합니다.

  - Babel은 JSX를 React.createElement() 호출로 컴파일합니다.

  - 아래 두 코드는 동일한 기능임
   
  ```jsx
  const element = (
    <h1 className="greeting"> Hello, world! </h1>
  );

  const element = React.createElement(
    'h1',
    {className: 'greeting'},
    'Hello, world!'
  );
  ```

  - React.createElement()는 버그가 없는 코드를 작성하는 데 도움이 되도록 몇 가지 검사를 수행하며, 기본적으로 다음과 같은 객체를 생성함.

  ```jsx
  // 주의: 다음 구조는 단순화되었습니다
  const element = {
    type: 'h1',
    props: {
      className: 'greeting',
      children: 'Hello, world!'
    }
  };
  ```

  - 이러한 객체를 "React 엘리먼트"라고 하며, React는 이 객체를 읽어서, DOM을 구성하고 최신 상태로 유지하는 데 사용한다.

### 팁

- ES6 및 JSX 코드가 올바르게 표시되도록 편집기에 "Babel" 언어 설정을 사용하는 것을 권장

### 참고자료
- https://legacy.reactjs.org/docs/introducing-jsx.html
- https://chanhuiseok.github.io/posts/react-3/
