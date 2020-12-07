---
title: '[JavaScript] this의 정체'
date: 2019-11-05
category: javascript
draft: false
---

# this??

함수가 호출될 때, 전달받은 인자값에서 암묵적으로 전달받는 값인 `this`
보통 자바에선 `this`가 자기 자신을 가리키는 참조변수이지만, 자바스크립트에서의 개념은 조금 다르다. 함수가 호출되는 방식에 따라 자기 자신을 가리킬 수도, 상위의 어떤 객체를 가리키기도 하고, 또 엄격 모드와 비엄격 모드에서의 차이가 있기도 함. 암튼 이 `this`란 녀석의 개념은 좀 복잡하기에 이 글을 쓴다.

---

## 전역 this

일단 아무 함수에서도 속하지 않은 전역에서 `this`를 호출하면, 최상위 전역 객체인 **window**를 가리킨다.

```js
console.log(this === window) // true
```

---

## 함수에서의 암묵적인 this 바인딩

위에서도 말했듯 `this`는 함수의 호출 방식에 따라 바인딩되는 객체가 이리저리 바뀜. 내가 직접 할당하는 것도 아니고 동적으로 결정되는 부분이기에 `this`의 개념을 확실히 잡아두는 편이 좋음! 함수 호출에 따른 `this`의 구분은 이러하다.

### 1. 단순 함수 내 호출

일반적인 함수 안에서의 `this` 또한 전역인 **window**를 가리킨다.

```js
function func() {
  return this
}

func() === window // true
```

> 허나 **use strict** 엄격 모드에선 전역이 아닌 `undefined`가 뜬다.

### 2. 메소드 내 호출

어떠한 객체의 메소드로 호출할 경우, `this`는 그 **객체**를 가리킴.

```js
var obj = {
  val: 'text',
  foo: function() {
    return this.val
  },
}

obj.foo() // 'text'
```

혹은 프로토타입 체인을 통해 만든 메소드의 `this` 바인딩 또한 결과는 같음.

### 3. 생성자 함수 내 호출

> !참고 : 자바스크립트에서는 함수에 `new` 키워드만 붙이면 전부 생성자 함수가 됨. 일반 함수와 차이를 두기 위해 함수명의 첫글자를 대문자로 작성한다.

`new` 키워드를 통해 생성한 생성자 함수의 `this`는 새로 만든 객체 **인스턴스**를 가리킴.

```js
function A() {
  this.a = 10
}

var obj = new A()

console.log(obj.a) // 10
```

### 4. 이벤트 함수 내 호출

HTML 내에서 인라인으로 이벤트 함수를 걸 경우, 이벤트 함수 내에 `this`는 **window**다. 왜냐? 전역에서 호출한 함수기 때문에 그렇다.

```js
// html
;<button onClick="clickMe()">this?</button>

// script
function clickMe() {
  console.log(this) // window
}
```

그런데! 스크립트 내부에서 `addEventListener`를 통해 연결한 이벤트 함수에서는 `this`가 이벤트를 실행한 요소를 가리킨다.

```js
function fnc(e) {
  console.log(this === e.currentTarget)
}

var elem = document.getElementById('element1')

elem.addEventListener('click', fnc, false)

// elem 클릭 시,true 값
```

### 5. 화살표 함수 내 this

화살표 함수에서의 this는 다른 일반 함수들과는 좀 다르다. 위에선 `this`의 바인딩이 동적으로 결정되지만, 화살표 함수에서 `this`는 무조건 자신을 감싸고 있는 **상위 스코프 객체**를 가리키게 된다.

```js
var obj = {
  bar: function() {
    var x = () => this
    return x
  },
}

var fn = obj.bar()

console.log(fn() === obj) // true
```

해당 `this`는 밑에서 얘기할 명시적 this 바인딩이 통하지 않음. this의 값을 바꾸지 못한다.

```js
window.x = 1
const arrow = () => this.x
console.log(arrow.call({ x: 10 })) // x : 1 (바꾸지 못함)
```

---

## 명시적 this 바인딩

this에 바인딩 되는 값은 호출방식에 암묵적으로 천차만별 갈라진다. 하지만 자바스크립트는 프로토타입 객체 메소드인 **call**, **apply**, **bind** 를 통해 명시적으로 `this`를 원하는 객체로 바인딩 시키는 방법을 제공한다.

### 1. apply / call

둘은 모두 함수를 호출시킬 때 사용한다. 첫번째 매개변수에 `this`를 바인딩 시킬 객체를, 두번째 매개변수에는 함수에 전달할 인수를 넣는다. 둘의 역할은 같으나 차이점이라 한다면, `call`은 두번째 매개변수에 단일 인자로 각각 전달한다면, `apply`는 배열의 형태로 함수에 인자를 전달한다.

```js
function fnc(a, b) {
  return this.a + this.b
}

var obj = { a: 1, b: 4 }

fnc.apply(obj, [10, 20])
fnc.call(obj, 10, 20)

// 5
// 5
```

### 2. bind

`bind`는 `apply`, `call`과 다르게 함수를 호출하지 않고, `this`를 바인딩시킨 새로운 함수를 리턴한다. 즉, 메소드 이름 그대로 bind만 시킨다.
`객체.bind( this를 바인딩할 객체 )` 로 표기한다.

```js
var obj = {
  x: 10,
  getX: function() {
    return this.x
  },
}

var unbound = obj.getX // undefined

var bound = unbound.bind(obj) // 10
```
