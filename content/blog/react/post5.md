---
title: '[React] 3. 리액트 라이프사이클'
date: 2019-11-09
category: react
draft: false
---

리액트 컴포넌트는 수명 주기란 것이 존재한다. 그리고 수명 주기는 크게 세 가지로 분류됨.

1. 컴포넌트가 페이지에 새로 렌더링 될 때 - **마운트**
2. 컴포넌트가 업데이트될 때 - **업데이트**
3. 컴포넌트가 페이지에서 사라질 때 - **언마운트**

---

## 라이프사이클 종류

### 마운트

```
constructor -> getDerivedStateFromProps -> render -> componentDidMount
```

- #### constructor(props)

컴포넌트를 만들 때, 가장 처음으로 실행되는 생성자 메소드. `state`의 기본값을 이 곳에서 설정함.

- #### getDerivedStateFromProps()

  `props`로 받아온 값을 `state`에 동기화 시킴. 마운트 혹은 업데이트 시 호출.

- #### render()

  컴포넌트 정의 메소드. 리액트의 **필수 메소드**임. `render` 메소드는 요소를 반환하는 역할을 하기에, state를 변경하거나, DOM에 접근은 불가하다(하면 안됨)

- #### componentDidMount()
  화면 렌더링이 끝나면 호출되는 메소드. 페이지에 마운트가 끝난 후 실행하기 때문에, 다른 JS 라이브러리를 호출하거나, 혹은 이벤트를 등록하거나, 비동기 작업 등을 처리할 때 사용한다.

### 업데이트

```
getDerivedStateFromProps -> shouldComponentUpdate -> render -> getSnapshotBeforeUpdate -> componentDidUpdate
```

- #### shouldComponentUpdate(nextProps, nextState)

`props`나 `state` 값이 변경될 때, 새로 렌더링을 할 지 말 지 결정하는 메소드. 반드시 **true 혹은 false 값을 반환** 해야함. 성능을 위해 컴포넌트를 최적화시킬 경우, 리렌더링 방지를 위해 사용하게 될 것!
언제나 기본값은 `true` 상황에 따라 `false`를 반환하도록 해야한다.
현재 props나 state는 `this.props or state`로 접근, 새로 설정될 값은 `next.props or state`로 접근하면 된다.

- #### getSnapshotBeforeUpdate

render 메소드 다음, DOM을 변경하기 바로 직전에 호출되는 메소드. 업데이트하기 직전 값을 참고해야할 일이 있을 때 활용됨.

> ex) 스크롤바 위치 유지

반환값은 `componentDidUpdate`의 파라미터인 `snapshot`으로 전달.

- #### componentDidUpdate(prevProps, prevState, snapshot)

리렌더링(업데이트)를 마친 후 실행. `componentDidMount`메소드처럼 DOM 처리가 가능하다. 해당 메소드에서는 변경 직전의 `state`와 `props`의 데이터에 접근할 수 있음.

### 언마운트

```
componentWillUnMount
```

- #### componentWillUnmount

컴포넌트를 제거할 때 실행. `componentDidMount`에서 등록했던 이벤트나 타이머 혹은 직접 생성한 DOM을 제거해야함.

### example

_LifeCycle.js :_

```jsx
import React, { Component } from 'react'

class LifeCycleSample extends Component {
  state = {
    number: 0,
    color: null,
  }

  // ref를 설정
  // 직접 DOM에 접근할 경우, ref를 이용한다.
  myRef = null

  // 1. 첫번째로 호출되는 메소드
  constructor(props) {
    super(props)
    console.log('constructor')
  }

  // 2. 받아온 props를 state에 동기화
  // 만약 다음에 받아올 color값이 변한다면 다음 color값으로 return
  static getDerivedStateFromProps(nextProps, prevState) {
    if (nextProps.color !== prevState.color) {
      return { color: nextProps.color }
      console.log('getDerivedStateFromProps')
    }
    console.log('no!!!!')
    // 반환값이 없다면 return 값은 null

    return null
  }

  componentDidMount() {
    console.log('componentDidMount')
  }

  // 4. 리렌더링 할 지 말 지 결정. 리턴값은 boolean
  shouldComponentUpdate(nextProps, nextState) {
    console.log('shouldComponentUpdate', nextProps, nextState)

    // 리턴값이 4가 아니라면 true, 4라면 false
    return nextState.number % 10 !== 4
  }

  componentWillUnmount() {
    console.log('componentWillUnmount')
  }

  handleClick = () => {
    this.setState({
      number: this.state.number + 1,
    })
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    console.log('getSnapshotBeforeUpdate')
    if (prevProps.color !== this.props.color) {
      return this.myRef.style.color
    }
    return null
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    console.log('componentDidUpdate', prevProps, prevState)
    if (snapshot) {
      console.log('업데이트 되기 직전 색상 : ', snapshot)
    }
  }

  render() {
    console.log('render')
    const style = {
      color: this.props.color,
    }

    return (
      <div>
        <h1 style={style} ref={ref => (this.myRef = ref)}>
          {this.state.number}
        </h1>
        <p>color: {this.state.color}</p>
        <button onClick={this.handleClick}>더하기</button>
      </div>
    )
  }
}

export default LifeCycleSample
```

_App.js :_

```jsx
import React, { Component } from 'react'
import LifeCycleSample from './LifeCycleSample'

function getRandomColor() {
  return '#' + Math.floor(Math.random() * 16777215).toString(16)
}

class App extends Component {
  state = {
    color: '#000000',
  }

  handleClick = () => {
    this.setState({
      color: getRandomColor(),
    })
  }

  render() {
    return (
      <div>
        <button onClick={this.handleClick}>랜덤 색상</button>
        <LifeCycleSample color={this.state.color} />
      </div>
    )
  }
}

export default App
```
