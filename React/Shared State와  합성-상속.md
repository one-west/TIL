# 목록

- [Shared State](#Shared-State)
- [합성(Composition)](#합성Composition)
- [상속(Inheritance)](#상속Inheritance)
- [컨텍스트(Context)](#컨텍스트Context)

## Shared State

- Shared state는 state의 공유를 의미한다.

- 같은 부모 컴포넌트의 state를 자식 컴포넌트가 공유해서 사용하는 것이다.

- 다음 그림은 부모 컴포넌트가 섭씨 온도의 state를 갖고 있고, 이것을 컴포넌트 C와 컴포넌트가 공유해서 서용하는 것을 보여줌

  ![Shared State](https://ko.react.dev/_next/image?url=%2Fimages%2Fdocs%2Fdiagrams%2Fsharing_state_child.png&w=1080&q=75)

### Shared State 적용하기

```jsx
export default function TemperatureInput(props) {
  const handleChange = (event) => {
    props.onTemperatureChange(event.target.value);
  };
  const scaleNames = {
    c: "섭씨",
    f: "화씨",
  };
  return (
    <fieldset>
      <legend>온도를 입력해주세요(단위:{scaleNames[props.scale]}):</legend>
      <input value={props.temperature} onChange={handleChange} />
    </fieldset>
  );
}
```

```jsx
import { useState } from "react";
import TemperatureInput from "./TemperatureInput";

function BoilingVerdict(props) {
  if (props.celsius >= 100) {
    return <p>물이 끓습니다.</p>;
  }
  return <p>물이 끓지 않습니다.</p>;
}

function toCelsius(fahrenheit) {
  return ((fahrenheit - 32) * 5) / 9;
}

function toFahrenheit(celsius) {
  return (celsius * 9) / 5 + 32;
}

function tryConvert(temperature, convert) {
  const input = parseFloat(temperature);
  if (Number.isNaN(input)) {
    return "";
  }
  const output = convert(input);
  const rounded = Math.round(output * 1000) / 1000;
  return rounded.toString();
}

export default function Calculator() {
  const [temperature, setTemperature] = useState("");
  const [scale, setScale] = useState("c");

  const handleCelsiusChange = (temperature) => {
    setTemperature(temperature);
    setScale("c");
  };

  const handleFahrenheitChange = (temperature) => {
    setTemperature(temperature);
    setScale("f");
  };

  const celsius =
    scale === "f" ? tryConvert(temperature, toCelsius) : temperature;
  const fahrenheit =
    scale === "c" ? tryConvert(temperature, toFahrenheit) : temperature;

  return (
    <div>
      <TemperatureInput
        scale="c"
        temperature={celsius}
        onTemperatureChange={handleCelsiusChange}
      />
      <TemperatureInput
        scale="f"
        temperature={fahrenheit}
        onTemperatureChange={handleFahrenheitChange}
      />
      <BoilingVerdict celsius={parseFloat(celsius)} />
    </div>
  );
}
```

## 합성(Composition)

- 합성(Composition)은 여러 개의 컴포넌트를 합쳐서 새로운 컴포넌트를 만드는 것이다.

- 조합 방법에 따라 합성의 사용 기법은 다음과 같이 나눈 수 있습니다.

### Containment(담다. 포함하다. 격리하다.)

- 특정 컴포넌트가 하위 컴포넌트를 포함하는 형태의 합성방법이다.

- 컴포넌트에 따라서는 어떤 자식 엘리먼트가 들어올 지 미리 예상할 수 없는 경우가 있다.

- 범용적인 박스 역할을 하는 Sidebar 혹은 Dialog와 같은 컴포넌트에 특히 자주 볼 수 있습니다.

- 이런 컴포넌트에서는 children prop을 사용하여 자식 엘리먼트를 출력에 그대로 전달하는 것이 좋다.

- 다음과 같이 props.children을 사용하면 해당 컴포넌트의 하위 컴포넌트가 모두 children으로 들어오게 된다.

    ```jsx
    export default function FancyBorder(props) {
      return (
        <div className={`FancyBorder` + props.color}>{props.children}</div>
      );
    }
    ```

- 만일 여러 개의 children 집합이 필요할 경우는 별도로 props를 정의해서 각각 원하는 컴포넌트를 넣어준다.

### Specialization (특수화, 전문화)

- 범용적인 개념을 구별이 되게 구체화하는 것을 특수화라고 합니다.

- 객체지향 언어에서는 상속을 사용하여 특수화를 구현합니다.

- 리액트에서는 합성을 사용하여 특수화를 구현합니다.

  ```jsx
  function Dialog(props) {
    return (
      <FancyBorder color="blue">
        <h1 className="Dialog-title">{props.title}</h1>
        <p className="Dialog-message">{props.message}</p>
      </FancyBorder>
    );
  }
  ```

## 상속(Inheritance)

### Containment와 Specialization을 같이 사용하기

- Containment를 위해서 `prop.children`을 사용하고, Specialization을 위해 직접 정의한 `props`를 사용하면 된다.

    ```jsx
    export default function FancyBorder(props) {
      return (
        <div className={`FancyBorder` + props.color}>{props.children}</div>
      );
    }
    ```

    ```jsx
    import FancyBorder from "./FancyBorder";

    export default function WelcomeDialog(props) {
      return (
        <FancyBorder color="blue">
          <h1 className="Dialog-title">어서와.</h1>
          <p className="Dialog-message">내 사이트는 처음이지?</p>
          {props.children}
        </FancyBorder>
      );
    }
    ```

- 합성과 대비되는 개념으로 상속이 있다.

- 자식 클래스는 부모 클래스가 가진 변수나 함수 등의 속성을 모두 갖게 되는 개념.

- 하지만 리액트에서는 상속보다는 합성을 통해 새로운 컴포넌트를 생성.

**복잡한 컴포넌트를 쪼개 여러 개의 컴포넌트로 만들고, 만든 컴포넌트들을 조합하여 새로운 컴포넌트를 만들자**

## 컨텍스트(Context)

- 기존의 일반적인 리액트에서는 데이가 컴포넌트의 `props`를 통해 부모에서 자식으로 단방향으로 전달하였음.

- 컨텍스트는 리액트 컴포넌트들 사이에서 데이터를 기존의 `props`를 통해 전달하는 방식 대신 _'컴포넌트 트리를 통해 곧바로 컴포넌트에 전달하는 새로운 방식'_ 을 제공한다.

- 이것을 통해 어떤 컴포넌트라도 쉽게 데이터에 접근할 수 있다.

### 언제 컨텍스트를 사용해야 할까?

- 여러 컴포넌트에서 자주 필요로 하는 데이터

- Ex) 로그인 여부, 로그인 정보, UI테마(다크/라이트 모드), 현재 선언된 언어 등

- `props`를 통해 데이터를 전달하는 방식은 계속 props로 넘겨줘야 하는 Props drilling을 해줘야 하기 때문에 불편하다(가독성 저하).

- **Props drilling**: 중첩된 여러 계층의 컴포넌트들에게 `props`를 전달해 주는 것

### 컨텍스트를 사용하기 전에 고려할 점

- 컨텍스트는 다른 레벨의 많은 컴포넌트가 특정 데이터를 필요로 하는 경우에 주로 사용한다.

- 하지만 무조건 컨텍스트를 사용하는 것이 좋은 것은 아니다.

- 컴포넌트와 컨텍스트가 연동되면 재사용성이 떨어지지 때문이다.

- 다른 레벨의 많은 컴포넌트가 데이터를 필요로 하는 경우가 아니면 `props`를 통해 데이터를 전달하는 컴포넌트 합성 방법이 더 적합하다.

### React.createContext

- Context 객체를 컴포넌트에서 렌더링할 때 React는 트리 상위에서 가장 가까이 있는 짝이 맞는 `Provider`로부터 현재값을 읽는다.

- `defaultValue` 매개변수는 트리 안에서 적절한 `Provider`를 찾지 못했을 때만 쓰이는 값입니다.

- `Provider`를 통해 `undefined`을 값으로 보낸다고 해도 컴포넌트들이 `defaultValue`` 를 읽지는 않는다는 점에 주의할 것

### Context.Provider

- `Provider`는 context를 사용하는 컴포넌트들에게 context의 변화를 알리는 역할을 한다.

- `Provider`` 컴포넌트는 value prop을 받아서 이 값을 하위에 있는 컴포넌트에게 전달한다.

- 값을 전달받을 수 있는 컴포넌트의 수에 제한은 없습니다.

- `Provider` 하위에 또 다른 `Provider`를 배치하는 것도 가능하며, 이 경우 하위 Provider의 값이 우선시됩니다.

- `Provider` 하위에서 context를 사용하는 모든 컴포넌트는 `Provider`의 value prop가 바뀔 때마다 다시 렌더링 됩니다.

### Class.contextType

- `React.createContext()`로 생성한 Context 객체를 원하는 클래스의 contextType 프로퍼티로 지정할 수 있습니다.

- 클래스 타입은 더 이상 사용하지 않음.

### Context.Consumer

- Context.Consumer의 자식은 함수여야한다.

- 이 함수는 context의 현재값을 받고 React 노드를 반환한다.

- 이 함수가 받는 value 매개변수 값은 해당 context의 `Provider`` 중 상위 트리에서 가장 가까운 Provider의 value prop과 동일하다.

- 상위에 `Provider`가 없다면 value 매개변수 값은 createContext()에 보냈던 defaultValue와 동일할 것이다.

### Context.displayName

- Context 객체는 `displayName` 문자열 속성을 설정할 수 있다.

- React 개발자 도구는 이 문자열을 사용해서 context를 어떻게 보여줄 지 결정합니다.

### 여러 개의 컨텍스트 사용하기

- 여러 개의 컨텍스트를 동시에 사용하려면 Context.Provider를 중첩해서 사용.

- 하지만 두 개 또는 그 이상의 컨텍스트의 값이 자주 함께 사용될 경우 모든 값을 한 번에 제공해주는 별도의 render prop 컴포넌트를 직접 만드는 것이 더 좋음

### useContext

- 함수형 컨포넌트에서 컨텍스트를 사용하기 위해 컴포넌트를 매번 Consumer 컴포넌트로 감싸주는 것보다 더 좋은 방법이 있다. 바로 Hook이다.

- 상위 컴포넌트의 data가 필요한 하위 컴포넌트들은 `useContext()` 훅을 사용해서 해당 데이터를 받아오기만 하면 된다.

- useContext는 Context로 분류한 데이터를 쉽게 받아올 수 있게 도와주는 역할을 한다.

- context는 꼭 필요할 때만 사용한다.

- context를 사용하면 컴포넌트를 재사용하기 어려워질 수 있다.

- context의 주된 목적인 다양한 레벨이 있는 많은 컴포넌트들에게 전역적인 데이터를 전달하기 위함이다.

- 리액트 공식 문서에는 context를 사용하는 이유가 단지 prop drilling을 핗기 위한 목적이라면 component composition(컴포넌트 합성)이 더 간단한 해결책이라고 제시하고 있다.
