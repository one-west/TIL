# React 컴포넌트와 Props의 개념

## Props

1. Props의 개념
    
    - props는 prop(property:속성, 특성)의 준말이다.
    
    - props가 컴포넌트의 속성
    
    - 컴포넌트에 어떤 속성, props를 넣느냐에 따라서 속성이 다른 엘리먼트가 출력
    
    - props는 컴포넌트에 전달 할 다양한 정보를 담고 있는 JS 객체이다

2. Props의 특징
    
    - 읽기 전용
    
    - 속성이 다른 엘리먼트를 생성하려면 새로운 props를 컴포넌트에 전달하면 된다.
   
    ### Pure 함수 VS Impure 함수
    
    - Pure함수는 인수로 받은 정보가 함수 내부에서 <u>변하지 않는 함수</u>.
    
    - Impure함수는 인수로 받은 정보가 함수 내부에서 <u>변하는 함수</u>.
        ```jsx
        // Pure 함수
        function sum(a, b) {
            return a + b
        }
        // Impure 함수
        function withraw(account, amount) {
            account.total -= amount
        }
        ```

3. Props 사용법
    - jsx에서는 key-value쌍으로 props를 구성
   
        ```jsx
        function App(props) {
            return (
                <Profile
                    name="한서"
                    introduction = "안녕하세요, 한서입니다."
                    viewCount={1500}
                />
            );
        }
        ```

    위 코드는
    - Profile 컴포넌트로 name, introduction, viewCount를 전달한다.
    - 이때 전달되는 props 는 다음과 같은 자바스크립트 객체이다.
    - JSX에서는 중괄호를 사용하면 JS코드를 넣을 수 있다.
    - 다음 코드처럼 props를 통해서 value를 할당할 수도 있고, 직접 중괄호를 사용하여 할당할 수도 있다.

        ```jsx
        function App(props) {
            return (
            <Layout
                width = {2500}
                height = {1440}
                header = {
                    <Header title = "한서의 학습자료입니다." />
                }
                footer = {
                    <Footer />
                }
            />
            );
        }
        ```
        
    - JSX를 사용하지 않는 경우 props의 전달 방법은 createElement() 함수를 사용하는 것이다.
    - createElement() 함수의 두번째 매개변수가 props이다.


## 컴포넌트 만들기

1. 컴포넌트의 종류
    
    - 리액트 초기 버전을 사용할 때는 클래스형 컴포넌트를 주로 사용했다.
    
    - 이후 Hook이라는 개념이 나오면서 최근에는 함수형 컴포넌트를 주로 사용
    
    - 예전에 작성된 코드나 문서들이 클래스형 컴포넌트를 사용하고 있기 때문
    
    - 클래스형 컴포넌트와 컴포넌트의 생명주에 관해서도 공부해두자.  

    ![컴포넌트 종류](https://velog.velcdn.com/images/sjmh0507/post/1491b6fb-0cff-4c03-81ee-1928df942ff3/image.png)

2. 함수형 컴포넌트
    
    - 모든 리액트 컴포넌트는 퓨어함수같은 역할을 해야한다. 리액트의 컴포넌트를 일종의 함수처럼 생각해도 된다.

   ```jsx
    function Welcome(props) {
	    return <h1>안녕, {props.name}</h1>;
    }
    ```
    
3. 클래스형 컴포넌트
    
    - 클래스 컴포넌트의 class는 ES6의 그 class이다. 함수 컴포넌트보다 몇 가지 더 추가 기능을 가지고 있다.
    
    ```jsx
    class Welcome extends React.Component {
	    return <h1>안녕, {this.props.name}</h1>;
    }
    ```

    - 위 코드는 함수형 컴포넌트의 클래스 컴포넌트 버전이다.
    
    - 리액트의 모든 클래스 컴포넌트는 React.Component를 상속받아서 만든다.
    
    - '상속'이라는 개념은 객체지향 프로그래밍에서 나오는 개념인데, 한 클래스의 변수들과 함수들을 상속받아 새로운 자식 클래스를 만드는 방법이다.

    > 컴포넌트 이름을 소문자로 하면 돔 태그로 인식(내장 컴포넌트)하기 때문에 대문자로 시작해서 리액트 컴포넌트로 인식하도록 해야한다.
    
4. 컴포넌트 명명 규칙
    
    - 이름은 항상 대문자로 시작
    
    - 리액트는 소문지로 시작하는 컴포넌트를 DOM 태그로 인식하기 때문이다.
    
    - ※ 컴포넌트 파일 이름과 컴포넌트 이름은 같게 한다.

5. 컴포넌트의 렌더링

    ```jsx
    function Welcome(props) {
      return <h1>안녕, {props.name}</h1>
    }
    const element = <Welcome name="한서" />
    ReactDOM.render(
        element, 
        document.getElementById('root')
    )
    ```

7. 컴포넌트 합성
    
    - 합성 컴포넌트란 소프트웨어 개발에서 재사용이 가능한 구성 요소를 만들기 위해 여러 개의 다른 컴포넌트를 조합하는 디자인 패턴이다.
    
    - 복합적인 기능을 가진 큰 컴포넌트를 작은 단위의 컴포넌트들로 구성함으로써 코드의 유지보수성과 확장성을 높일 수 있다.
    
    - 합성 컴포넌트 패턴에서는 컴포넌트들이 계층 구조로 구성된다.
    
    - 각 컴포넌트는 하위 컴포넌트들을 가지며, 최상위 컴포넌트는 하위 컴포넌트들의 조합으로 이루어진다.
    
    - 이로 인해 단일 컴포넌트를 사용하는 것보다 더 복잡하고 다양한 기능을 제공할 수 있다.

      ```jsx
        function Welcome(props) {
	        return <h1>Hello, {props.name}</h1>;
        }
        function App(props) {
            return (
            <div>
                <Welcome name="Mike" />
                <Welcome name="Steve" />
                <Welcome name="Jane" />
            </div>
            )
        }
        ReactDOM.render (
            <App />,
            document.getElementById('root')
        );
      ```
    
    위 코드는 props 의 값을 다르게 해서 Welcome Component 를 여러번 사용한다.  
    -> App 이라는 Component 는 Welcome 이라는 Component 를 3개 포함하고 있는 Component 다.
   
8. 컴포넌트 추출
    
	- Component 추출을 사용하게 되면 Component 의 재사용성이 올라간다.
    
    - Component 가 작아질 수록 기능이 명확해지고, prop 도 단순해 지기 때문에, 다른 곳에서 사용하기 좋아진다.
    
    - 재사용성이 높아지면서 개발 속도도 올라간다.
    
    - 아래 코드는 댓글을 표현하기 위한 Component 이다.

        ```jsx
        function Comment(props) {
            return (
    	        <div className="comment">
                    <div className="user-info">
                    <img className="avatar"
                        src={props.author.avatarUrl}
                        alt={props.author.name}
                    />
                    <div className="user-info-name">
                        {props.author.name}
                    </div>
                </div>
            
                <div className="comment-text">
            	    {props.text}
                </div>
            
                <div className="comment-date">
            	    {formatDate(props.date)}
                </div>
            </div>
            );
        }
        ```
  
	- Avatar 추출하기

        ```jsx
        function Avatar(props) {
            return (
    	        <img className="avatar"
                    src={props.user.avatarUrl}
                    alt={props.user.name}
                />
            );
        }
        ```
        
        - 위와 같이 Avatar 을 추출해 준 뒤, Comment Component 에 img 태그 대신
        
        	```jsx
        	<Avatar user={props.author} />
        	```
        
        위 코드를 넣어주면 된다.

    - UseInfo 추출하기

        ```jsx
        function UserInfo(props) {
            return (
    	        <div className="user-info">
                    <Avatar user={props.user} />
                    <div className="user-info-name">
            	        {props.user.name}
                    </div>
                </div>
            );
        }
        ```
        
        ```jsx
        <UserInfo user={props.author} />
        ```

    - 사용자정보를 class 로 가진 div 대신에 위 코드를 넣어주면 된다.
    - 결과

        ```jsx
        function Comment(props) {
            return (
    	        <div className="comment">
                    <UserInfo user={props.author} />
                    <div className="comment-text">
            	        {props.text}
                    </div>
                    <div className="comment-date">
            	        {formatDate(props.date)}
                    </div>
                </div>
            );
        }
        ```

9. 생명주기(Lifecycle)

	- 생명주기는 컴포넌트의 생성 시점, 사용 시점, 종료 시점을 나타냄

	- constructor가 실행되면서 컴포넌트가 생성된다.

	- 생성 직후 compomentDidMount() 함수가 호출

	- 컴포넌트가 소멸하기 전까지 여러 번 랜더링 한다.

	- 렌더링은 props, setState(), forceUpdate()에 의해 상태가 변경되면 이루어짐

	- 렌더링이 끝나면 compomentDidUpdate()가 호출

	- 마지막으로 컴포넌트가 언마운트 되면 compomentWillUnmount()함수 가 호출됨

	![마운팅,업데이팅,언마운팅1.jpg](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FzNVQp%2FbtsDfPeNMg9%2FhkXlv1oQnXVHlDZnkJ47TK%2Fimg.png)
	![마운팅,업데이팅,언마운팅2.jpg](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FAgYkW%2FbtsDLKxfeHN%2FacLRPKVYV36YLWeXCPTvZ1%2Fimg.png)

### 참고자료
- https://ko.legacy.reactjs.org/docs/components-and-props.html
- https://www.freecodecamp.org/korean/news/how-to-use-props-in-react/
- [교재]소플의 처음 만난 리액트
