---
title: '[ReactJS] 2. 리액트 이벤트 핸들링'
date: 2019-11-09
category: react
draft: false
---

- 자바스크립트의 이벤트 연결과 달리 함수 그대로 값을 넣어 전달함. (화살표 함수 형태)
- 이벤트는 반드시 DOM요소에 직접 설정
- 이름은 camelCase로 작성

```jsx
import React, { Component } from 'react'

class EventPractice extends Component {
  constructor(props) {
    super(props)
    this.state = {
      text: '',
      username: '',
    }

    // state값을 setting하기 위해 bind 작업이 필요함.
    // bind(this)가 없으면 undefined를 가리킨다.
    // 반드시 생성자에서 바인딩 해주어야 함!
    this.handleChange = this.handleChange.bind(this)
    this.handleClick = this.handleClick.bind(this)
  }

  handleChange(e) {
    this.setState({
      [e.target.name]: e.target.value,
    })
  }

  handleClick() {
    alert(this.state.username + ' : ' + this.state.text)

    this.setState({
      text: '',
      username: '',
    })
  }

  render() {
    return (
      <div>
        <h1>이벤트 연습</h1>
        <textarea
          name="text"
          placeholder="입력하세요."
          onChange={this.handleChange}
        ></textarea>

        <input
          type="text"
          name="username"
          placeholder="유저명"
          onChange={this.handleChange}
        ></input>
        <button onClick={this.handleClick}>확인</button>
        <p>{this.state.text}</p>
      </div>
    )
  }
}

export default EventPractice
```
