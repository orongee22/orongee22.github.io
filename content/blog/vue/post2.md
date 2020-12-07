---
title: '[Vue.JS] 1. 뷰 시작하기'
date: 2019-10-10
category: vue
draft: false
---

# Vue.js

## 시작하기

Vue의 장점은 리액트와 앵귤러의 장점들을 적절히 융합했다는 것에 있음. 리액트처럼 가상 돔을 사용하기에 빠른 DOM 렌더링이 가능함. 뷰는 리액트와 다르게 싱글 페이지로 구성됨. `html` `css` `js` 의 코드를 한 파일 내에서 관리하기에 알아보기도 매우 편리하고 관리하기에도 용이함.

### 설치

```shell
npm i vue
```

혹은 CDN으로 손쉽게 가져와서 바로 적용이 가능함.

```html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

### 적용

```html
<div id="app">
  <p>{{message}}</p>
</div>

<script>
  new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue.js!',
    },
  })
</script>
```

## Vue 인스턴스

vue 객체를 사용할 땐 새롭게 객체를 인스턴스화하여 사용함.

```js
var app = new Vue({ ... });
```

인스턴스를 생성할 땐 새로운 `Vue` 객체 속에 `data`, `template`, `el`, `method`, `callback` 등을 담은 **option**객체를 전달함. 인스턴스 생성자를 통해 컴포넌트 생성자를 만들어 확장할 수 있음.
vue 또한 모듈 단위의 컴포넌트 트리로 구성 됨.

### 속성

인스턴스 data는 속성을 담아 데이터를 화면에 출력하거나 어딘가로 전달해주는 역할을 함.
data 객체 속 모든 속성은 프록시 처리 됨. 그니까 외부의 객체 속성을 그대로 가져와 쓸 수 있다는 뜻인듯.

```js
var data { a : 1 }

var vm = new Vue({
	data: data
});

// data.a == vm.a (true)
```

Vue 인스턴스의 속성과 사용자 정의 속성을 구분하기 위해 Vue인스턴스의 속성은 `$`를 붙임

```js
var data = { a: 1 }
var vm = new Vue({
  el: '#app',
  data: data,
})

vm.$data === data
vm.$el === document.getElementById('#app')
```

data는 인스턴스가 생성될 때 존재한 것들만 화면에 렌더링 됨. 새 속성을 추가해봤자 화면은 변경되지 않기 때문에 이럴 경우엔 초기값으로 미리 지정 해놔야 함.

_이런 식으로:_

```js
data: {
	 newTodoText: '',
	 visitCount: 0,
	 hideCompletedTodos: false,
	 todos: [],
	 error: null
}
```

속성들의 값이 변화하면 Vue는 변화를 감지하고 화면을 다시 렌더링 시켜 변화된 값을 다시 뿌려줌. 만약 기존 속성이 변경되는 것을 막고 싶다면 `Object.freeze()`를 이용하면 됨.

### 라이프사이클

Vue는 단계별 라이프사이클 메소드들이 존재함. DOM에 마운트 될 때, 화면이 업데이트될 때, 인스턴스가 생성될 때 등등.
모든 라이프사이클 메소드는 `this` 컨텍스트가 호출하는 해당 **Vue인스턴스를 가리킴**. 그렇기에 라이프사이클 메소드를 호출할 때 화살표 함수를 이용해선 안됨. 화살표 함수는 부모의 컨텍스트를 바인딩하기 때문에 원래대로 vue인스턴스를 가리키지 못함. 오류가 발생하기 때문에 사용은 금물.

- beforeCreated : 화면이 생성되기 전 단계. `data`, `method` 정의 X , DOM접근도 불가.
- created : 화면에 마운딩되기 전이기에 template 속성에만 접근 불가능함.
-

## 템플릿

Vue는 html 기반의 템플릿 구문을 사용함. 굳이 템플릿이 아니더라도 `render()`함수나 `jsx`또한 지원한다.
템플릿 내에서 데이터를 바인딩 하기 위해선 ``{{ 내용 }}` 이중 보간법을 이용.
보간법 내부에서 js 모든 표현식 가능. 그러나 **if문은 불가. 삼항식으로 사용** 해야함.

```html
<div id="app">
  <p>{{message}}</p>
  <p>{{message.split('').reverse().join('')}}</p>
  <p>{{ok ? return message : return false}}</p>
</div>

<script>
  new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue.js!',
    },
  })
</script>
```

`message`의 내용이 변화하면 그 즉시 갱신됨. html 코드 내부에서는 이용할 수 있지만 속성으로 사용은 불가.
속성 내에선 `v-bind` 디렉티브를 이용

```html
<span v-bind:title="title">title 확인하기</span>
```

이중 보간법은 내부의 내용이 text로 출력되는데, 만약 `html` 코드로 출력하고 싶다면 `v-html` 디렉티브를 사용하면 됨.

### 디렉티브

`v-`가 붙어있는 특수한 속성. 디렉티브의 속성을 통해 DOM을 반응적으로 적용시킴.

- v-bind
- v-if
- v-on
- v-for
- v-model

등등이 있음.

#### 전달인자

`:속성`으로 표현. `v-bind`나 `v-on`디렉티브와 함께 사용함.

```html
<a v-bind:href="url"></a>
<a :href="url"></a>
<!-- href 속성의 url 값을 바인딩-->

<a v-on:click="doSomething"></a>
<a @click="doSomething"></a>
<!-- 클릭 이벤트에 함수 바인딩 -->
```

## Computed

템플릿 안에 복잡한 로직을 넣는 것은 좋지 않음! 그럴 땐 **computed** 속성을 이용하자!
`computed`라는 속성에 함수를 적용하면 해당 함수는 getter 함수로 쓰이게 됨.
`method` 속성 또한 함수를 저장하는 용도로 쓰임. 그러나 `method`는 data가 변화할 때만 함수를 호출하는 것과 다르게 `computed`는 의존대상(data)의 값을 저장하고 함수를 호출함. 이걸 **캐싱**이라고 함.

```html
<div id="example">
  <p>원본 메시지: "{{ message }}"</p>
  <p>역순으로 표시한 메시지: "{{ reversedMessage }}"</p>
</div>
```

```js
var vm = new Vue({
	 el: '#example',
	 data: {
		 message: '안녕하세요'
	 },
	 computed: {
		 // 계산된 getter
		 reversedMessage: function () {
			 // `this` 는 vm 인스턴스
			 return this.message.split('').reverse().join('')
		 }
	 }
}
```

`reversedMessage`는 자동적으로 `message`를 의존하고 있음. 이건 우리 직접 의존설정으로 만들어줬기에 가능한 것임. message의 값이 바뀌면 `reversedMessage`는 변화한 값을 인지하여 새롭게 언데이트 할 것임.

> message : '반갑습니다!'
> => !다니습갑반
