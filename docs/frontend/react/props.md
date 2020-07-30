# props를 통해 컴포넌트에 값 전달하기

`props`는 `properties`의 줄임말이다.    
특정 값을 컴포넌트에게 전달해야 할 때 아래와 같이 `props`를 사용한다. 


## 1. 기본 사용법 

아래는 `App` 컴포넌트에서 `Hello` 컴포넌트에 `name`이라는 값을 전달해주는 예제이다.

```js
// App.js
function App() {
  return (
    <Hello name="react" />
  );
}
```

그러면 `Hello` 컴포넌트에 `name`이라는 프로퍼티명으로 값이 담긴 `props` 파라미터가 전달된다.    
> (❗) `props`는 객체 형태이다.

```js
// Hello.js
function Hello(props) {
  return <div>안녕하세요 {props.name}</div>;
}
```


## 2. props의 구조 분해 할당

`Hello` 컴포넌트에 여러 개의 props를 전달할 때,    
ES6의 `구조 분해 할당` 문법을 이용해 코드를 간결히 작성할 수 있다. 

```js
// App.js
function App() {
  return (
    <Hello name="react" color="red" />
  );
}
```

`구조 분해 할당` 문법을 사용하면 `props.name`, `props.red`라고 매번 `props.`를 명시하지 않아도 된다. 

> (❗) 자바스크립트에서 객체의 `key`와 `value`가 같으면 생략 가능하다.   
> ➡ `{ color: color }` 와 `{ color }` 는 동일하다. 

```js
// Hello.js
function Hello({ name, color }) {
  return <div style={{ color }}>안녕하세요 {name}</div>;
}
```



## 3. defaultProps

전달받지 않은 `props`를 사용하고 싶을 땐 `defaultProps`를 이용해 기본값을 설정해두면 된다. 

```js
// App.js
function App() {
  return (
    <Hello color="red" />
  );
}
```

`App` 컴포넌트에서 `Hello` 컴포넌트에게 `name`이란 `props`를 전달하지 않았으므로,    
`Hello` 컴포넌트가 렌더링될 때 `{name}` 위치에 `이름없음` 이란 기본값이 들어가게 될 것이다. 

```js
// Hello.js
function Hello({ color }) {
  return <div style={{ color }}>안녕하세요 {name}</div>;
}

Hello.defaultProps = {
  name: '이름없음',
};
```



## 4. props.children

컴포넌트 태그 사이에 넣은 값은 `props`의 `children` 속성에 담겨 파라미터로 전달된다. 

아래의 경우 `Wrapper` 태그 사이에 넣은 `p` 태그와 `input` 태그가     
`props.children`에 담겨 `Wrapper` 컴포넌트의 파라미터로 전달된다. 

```js
return (
  <Wrapper>
    <p>안녕</p>
    <input />
  </Wrapper>
);
```

```js
// Wrapper.js
function Wrapper({ children }) {
  return <div>{children}</div>;
}
```

결과적으로 브라우저에는 `div > p + input` 의 구조로 엘리먼트가 렌더링될 것이다. 