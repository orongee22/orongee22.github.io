---
title: '[ReactJS] 1. 리액트 시작하기'
date: 2019-10-05
category: react
draft: false
---

# React

## 설치

리액트 프로젝트 도구인 `create-react-app`을 모든 디렉토리에서 사용할 수 있게 전역에 설치시킨다.

#### 1. 전역 설치

```node
npm install -g create-react-app
```

#### 2. 프로젝트를 생성

```node
create-react-app hello-react
```

#### 3. 프로젝트 실행

```cli
npm start
```

`npm start` 명령어는 프로젝트를 개발서버에서 실행시킨다.
http://localhost:3000 페이지에서 확인 가능함.

## JSX

JS 코드를 확장시켜 html 형태로 생성한 문법. 자바스크립트이므로 속성과 모든 이름은 camelCase로 사용하는 것이 규칙.

---

### 기본 문법

- 컴포넌트 안에 여러 요소가 있다면 반드시 부모 요소로 한꺼번에 감싸야함. 불필요한 부모 요소 생성을 방지하기 위해 `fragment`를 사용할 수도 있음 (v16 이상)

```jsx
class App extends Component {
  render() {
    return (
      <Fragment>
        <h1>헬로 리액트</h1>
        <p>이것은 리액트 튜토리얼</p>
      </Fragment>
    )
  }
}
```

- jsx 내부에서 자바스크립트 표현식 사용 가능. 표현식은 `{}` 로 감싸면 됨.

```jsx
class App extends Component {
  render() {
    const text = '리액트'
    return (
      <Fragment>
        <h1>헬로 {text}</h1>
        <p>이것은 {text} 튜토리얼</p>
      </Fragment>
    )
  }
}
```

- jsx 내부에서 if문 사용 X 대신 삼항식을 사용하거나, 혹은 jsx 외부에서 if문을 사용하자.
- 인라인 스타일을 사용하려면 스타일 속성을 camelCase로 작성함.

```jsx
	const style = {
		backgroundColor = 'gray',
		WebkitTransition: 'all',
		MozTransition: 'all',
		msTransition: 'all'
	}
```

- HTML코드는 모든 태그를 항상 마지막에 닫아줘야함. `input`, `br` 포함.

## Component

### 1. props

컴포넌트 속성을 설정할 때 사용함.
부모 컴포넌트에서 props 값을 설정하면, 자식컴포넌트에서 `this.props.속성`으로 jsx 내부에서 렌더링하는 방식.
오직 **부모 -> 자식** 방향만 가능함.

_App.js :_

```jsx
<MyComponent name="리액트" />
// 혹은
<MyComponent age={20} />
/* 문자열을 제외한 값을 전달할 때는 {}로 감싸주어야 함. */
```

_MyComponent.js :_

```jsx
return <div>{this.props.name}</div>
```

#### **defaultProps**

props의 기본 값 설정도 가능하다.

_MyComponent.js :_

```jsx
class MyComponent extends Component{
	static defaultProps = {
		name = '디폴트값'
	}
	render(){
		return (
			<div>{this.props.name}</div>
		)
	}
}
// => '디폴트값' 출력
```

#### **propsTypes**

필수 props를 지정하거나, props 타입이 제대로 들어왔는 지 확인하기 위해 사용함.
defaultProps와 사용은 비슷하나, 가장 먼저 `prop-types`를 **import** 시켜야함.

```jsx
import React, {Component} from 'react';
import PropTypes from 'prop-types';

class MyComponent extends Component{

// class 내부에선 static으로 설정.
static PropTypes = {
	name: '기본 이름'
}
/* ...코드 생략... */
}

// class 외부에서도 설정 가능.
MyComponent.propTypes = {
name: PropTypes.string // propsType을 문자열로 지정함.
age: PropTypes.number.isRequired // propType을 숫자로 지정, 필수값 설정.
}

export default MyComponent;
```

- propsType의 종류 : `array`, `bool`, `func`, `number`,`object`,`string`,`element`,`any` 등등...

### State

컴포넌트 내부에서 업데이트 되는 값을 사용하려면 **state**를 이용하자. **state**는 항상 기본 값을 미리 설정해야 하며, `this.setState()` 메소드로 업데이트함.
state 설정은 생성자 내부에서!

```jsx
class MyComponent extends Component {
  constructor(props) {
    super(props)
    this.state = {
      number: 0,
    }
  }

  static propTypes = {
    name: propTypes.string,
    age: propTypes.number,
  }

  render() {
    var component = '컴포넌트'
    return (
      <div>
        hello {component} {this.props.name}입니다.
        <p>나이는 {this.props.age}입니다.</p>
        <p>{this.state.number}</p>
        <button
          onClick={() => {
            this.setState({
              number: this.state.number + 1,
            })
          }}
        >
          +
        </button>
        <button
          onClick={() => {
            this.setState({
              number: this.state.number - 1,
            })
          }}
        >
          -
        </button>
      </div>
    )
  }
}
export default MyComponent
```
