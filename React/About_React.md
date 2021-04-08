# React.js

## What is React.js?

- 웹/앱(RN)의 View를 개발할 수 있도록 하는 라이브러리

## Features of React

### 1. JSX 문법

**[mean]**

- JSX(JavaScript eXtension): Javascript 안에서 HTML 문법을 사용해서 view를 구성할 수 있게 도와주는 Javascript 문법

- build 시 Babel(Javascript Compiler)에 의해 변환
- HTML 변수에 저장 가능, 함수 인자로 가능

```react
const hi = <p>Hi</p>
const myFavorite = {
    food: <li>샐러드</li>
    animal: <li>dog</li>
    hobby: <li>programming</li>
};
```

```react
class HelloMessage extends React.Component {
  render() {
    return (
      <div>
        Hello {this.props.name}
      </div>
    );
  }
}
```
<br/>   

**[attributes]**

(1)  속성은 항상 쌍따옴표로 감싼다.   
ex: class를 주고 싶은 경우 속성명은 class이지만 JSX에서는 className을 사용

```react
const myFaborite = {
    food: <li>샐러드</li>
    hobby: <li className="list-item">programming</li>
};
```

<br/>   

(2) 태그는 항상 닫혀있어야 한다.   
ex: HTML에서 input, br 태그는 닫지 않는 경우가 있다. 하지만 react는 반드시 closing tag를 써줘야 한다.

```react
import React, {component} from 'react';
class App extends Component {
    render(){
        return (
            <div>
                <input type="text" />
            </div>
        );
    }
}

export default App;
```

<br/>

(3) 어떤 태그라도 Self-closing tag가 가능하다

```react
<input> => <input />
<div></div> => <div />
```


<br/>   

(4) 형제 노드를 작성할 수 없다.   
ex: div가 이어진 형태로는 작성할 수 없다.   
    따라서 div나 Fragment로 감싸진 형태여야 한다.

```react
import React, {Component} from 'react';

class App extends Component {
    render(){
        return(
            <div></div>
            <div></div>
        );
    }
}

export default App;
```

=> 안된다.
<br/>
<br/>   


```react
import React, {Component, Fragment} from 'react';

class App extends Component {
    render(){
        return(
            <Fragment>
                <div></div>
                <div></div>
            </Fragment>
        );
    }
}

```

=> Fragment로 감싸져서 된다.

<br/>

(5) JSX 안에 자바스크립트 값은 중괄호로 감싼형태로 표현한다.

```react
import React, {Component, Fragment} from 'react'

class App extends Component {
    render() {
        const name = 'Edie';
        const hello = 'hello :)';
        return (
            <Fragment>
                <div>{name}</div>
                <div>{hello}</div>
            </Fragment>
        );
    }
}

export default App;
```


<br/>
<br/>

### 2. Component 기반

- React는 컴포넌트 기반 라이브러리

- 기존의 웹 페이지를 작성할 때 하나의 HTML 코드를 집어 넣고 하는 것이 아닌, 여러 부분을 분할해 코드의 재사용성, 유지보수성을 증가시켜 줌

  > MyComponent.js

  ```react
  import React, { Component } from 'react';
  
  class MyComponent extends Component
  {
      render(){
          return(
                  <h1>Hello React World!!</h1>
          );
      }
  }
  
  export default MyComponent;
  ```

  > app.js

  ```react
  import React, { Component } from 'react';
  import MyComponent from "./MyComponent"; // component import
  
  class App extends Component {
    render() {
      return (
      <div className="App">
        <MyComponent /> /* component 사용*/
        <MyComponent />
        <MyComponent />
      </div>
      );
    }
  }
  
  export default App;
  ```

  
<br/>
<br/>

### 3. Virtual DOM

**[before the concepts ]**

- DOM(Document Object Model): 문서 객체 모델, HTML 단위 하나하나를 객체로 한 모델

  ![DOM](https://miro.medium.com/max/2502/1*Vvi4_infsE8Q0uAStZmiWw.png)

  - 기존 DOM의 문제점: DOM의 요소를 수정하는 함수를 만들고 실행시킬 때 렌더 트리를 재생성하고 레이아웃을 만들고 페인팅하는 등 불필요한 연산이 매번 일어남
  - 한 부분만 업데이트하기 위해서는 업데이트 하지 않은 부분과 업데이트된 부분을 인지해야 함

**[mean]**

- 변화를 가상 돔에서 미리 인지해 변화
- 실제 DOM이 아니기 때문에 렌더링 X
- 가상 DOM의 변화를 마지막에 실제 DOM에게 주어 모든 변화를 한 번에 렌더링
- 업데이트된 부분과 아닌 부분을 자동으로 감지해 업데이트

> 출처: https://berkbach.com/%EA%B8%B0%EC%B4%88%EB%B6%80%ED%84%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-react-js-1531b18f7bb2
>
> https://velog.io/@edie_ko/React-JSX%EB%9E%80-%EB%A0%8C%EB%8D%94%EB%A7%81-Rendering%EC%9D%B4%EB%9E%80
<br/>
<br/>
<br/>
<br/>

## 특정부분을 나누어 개발하는 방법론

어플리케이션을 만들기 위해서는 

1. 사용자가 조작하기 위한 UI(User-Interface)

2. UI를 컨트롤하기 위한 로직
3. 데이터를 처리하는 비즈니스 로직

등 3가지 부분으로 개발이 필요


### MVC 패턴



### MVVM 패턴
> 출처: https://medium.com/react-native-seoul/react-%EB%A6%AC%EC%95%A1%ED%8A%B8%EB%A5%BC-%EC%B2%98%EC%9D%8C%EB%B6%80%ED%84%B0-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90-01-react-js%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-ad8ba252ee28

