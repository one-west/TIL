# Hook이란?

- 클래스형 컴포넌트에서 이용하던 코드를 작성할 필요없이 함수형 컴포넌트에서 다양한 기능을 사용할 수 있게 만들어준 라이브러리 [React 16.8 버전에 추가됨]

- 함수형 컴포넌트에 맞게 만들어진 것으로 함수형 컴포넌트에서만 사용 가능하다.
    
    - Hook은 일반적인 js 함수에서는 호출하면 안된다.
    
    - 같은 훅을 여러번 호출 가능하다.
    
    - custom hook에서는 호출 가능

- 최상위에서만 Hook을 호출해야한다.
    
    - 반복문, 조건문 혹은 중첩된 함수 내에서 호출하면 안된다.

- 블록 스코프 안에서는 사용 불가능

- 비동기 함수(async 키워드가 붙은 함수)는 콜백함수로 사용할 수 없다

- 훅의 이름은 모두 use로 시작해야함

### useState
- useState는 함수형 컴포넌트에서 state를 사용하기 위한 Hook이다.

- 현재 상태를 나타내는 state값과 이 상태를 변경하는 setState값을 한 쌍으로 제공한다.

- state는 초기값을 설정할 수 있으며, 초기값은 첫 렌더링 때 한번 사용된다. 

- useState로 선언된 변수는 상태(state)가 변할 때 마다 페이지를 리렌더링을 한다.

- 이때 함수는 비동기로 처리 되는데, 이는 함수가 호출 될 때 마다 화면을 다시 렌더링 하기 때문에 성능이슈가 발생 할 수 있기 때문이다.

- state는 객체일 필요 없이 문자열, 숫자, boolean, 배열, null, 객체 등의 여러가지 다양한 값을 넣을 수 있다.

    ```jsx
    import React, { useState } from 'react';

    function Example() {
        // "count"라는 새 상태 변수를 선언합니다
        const [count, setCount] = useState(0);

        return (
            <div>
                <p>You clicked {count} times</p>
                <button onClick={() => setCount(count + 1)}>Click me</button>
            </div>
        );
    }
    ```
### useEffect
- useState와 함께 가장 많이 사용하는 Hook이다.

- 클래스 컴포넌트의 생명주기 함수와 같은 기능을 하나로 통합한 기능을 제공
    - componentDidMount
    - componentDidUpdate
    - componentWillUnmount  

- Effect Hook을 사용하면 함수 컴포넌트에서 side effect를 수행할 수 있다.
- useEffect는 기본적으로 useEffect(function, deps)의 형태로 사용한다.

- sideEffect는 렌더링 외에 실핼해야 하는 부수적인 코드를 말한다.

- function에는 실행시킬 함수를 넣고, deps에는 의존성 배열을 담는다.

- 의존성 배열에 어떤 것이 담기느냐에 따라 부수적인 효과 함수가 실행된다.

- 의존성 배열 없이 useEffect를 실행시키면 페이지가 렌더링 될때마다 데이터를 불러온다.

- effect를 실행하고 정리(clean-up)하는 과정을 (마운트와 마운트 해제 시에)딱 한 번씩만 실행하려면, 빈 배열을 두 번째 인수로 넘기면 된다.

    ```jsx
    import React, { useState, useEffect } from 'react';

    function Example() {
        const [count, setCount] = useState(0);

        // componentDidMount, componentDidUpdate와 같은 방식으로
        useEffect(() => {
            // 브라우저 API를 이용하여 문서 타이틀을 업데이트합니다.
            document.title = `You clicked ${count} times`;
        });

        return (
            <div>
                <p>You clicked {count} times</p>
                <button onClick={() => setCount(count + 1)}>Click me</button>
            </div>
        );
    }
    ```
### useMemo
- useMemo() 훅은 memoized value를 리턴하는 훅이다.

- useMemo는 의존성이 변경되었을 때에만 메모이제이션된 값만 다시 계산한다.

- 이전 계산값을 갖고 있기 때문에 연산량이 많은 작업의 반복을 피할 수 있다.

- useMemo로 전달된 함수는 렌더링 중에 실행된다.

- 따라서 렌더링이 일어나는 동안 실행되서는 안될 작업을 넣으면 안된다.

- 배열이 없는 경우 매 렌더링 때마다 새 값을 계산하게 될 것이다.

    ```jsx
    import { useMemo } from 'react';
    
	// useMemo(calculateValue, dependencies) 형태
    
	function TodoList({ todos, tab, theme }) {
        const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  	// ...
	}
    ```

> ※ 메모이제이션(memoization): 컴퓨터 프로그램이 동일한 계산을 반복해야 할 때, 이전에 계산한 값을 메모리에 저장함으로써 동일한 계산의 반복 수행을 제거하여 프로그램 실행 속도를 빠르게 하는 기술이다.

### useCallback
- useCallback() 훅은 useMemo()와 유사한 역할을 한다.

- 차이점은 useMemo() 훅은 memoized value 반환하고, useCallback()은 memoized function을 반환한다.

- 의존성 값의 배열이 콜백에 인자로 전달되지는 않는다. 

- 그러나 개념적으로는, 이 기법은 콜백 함수가 무엇일지를 표현하는 방법이기 때문에 콜백 안에서 참조되는 모든 값은 의존성 값의 배열에 나타나야 한다.

- useCallback(fn, deps)은 useMemo(() => fn, deps)와 같습니다.

    ```jsx
    import { useCallback } from 'react';

	export default function ProductPage({ productId, referrer, theme }) {
		const handleSubmit = useCallback((orderDetails) => {
			post('/product/' + productId + '/buy', {
      			referrer,
      			orderDetails,
    		});
  		}, [productId, referrer]);
    // ...
    ```
    
### useRef
- useRef() 훅은 레퍼런스를 사용하기 위한 훅이다.

- 특정 DOM에 접근하여 DOM 조작을 가능하게 하는 훅이다.

- useRef는 .current 프로퍼티로 전달된 인자로 초기화된 변경 가능한 ref 객체를 반환, 반환된 객체는 컴포넌트의 전 생애주기를 통해 유지된다.

- useRef()는 순수 자바스크립트 객체를 생성하는데, useRef()와 {current: ...} 객체 자체를 생성하는 것의 유일한 차이점이라면 useRef는 매번 렌더링을 할 때 동일한 ref 객체를 제공한다.

- useRef는 내용이 변경될 때 변경점을 알려주지 않는 점을 주의해야함.

    ```jsx
    function TextInputWithFocusButton() {
        const inputEl = useRef(null);
        const onButtonClick = () => {
            // `current` points to the mounted text input element
            inputEl.current.focus();
        };
        return (
            <>
                <input ref={inputEl} type="text" />
                <button onClick={onButtonClick}>Focus the input</button>
            </>
        );
    }
    ```

### 참고 자료
- https://ko.legacy.reactjs.org/docs/hooks-intro.html
- https://choijying21.tistory.com/entry/React-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%9B%85%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0React-Hooks%EB%9E%80
- https://velog.io/@taek2yo/%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%9B%85React-Hook-%EC%9D%B4%EB%9E%80
