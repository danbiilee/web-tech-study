# 조건부 렌더링

조건부 렌더링이란 특정 조건에 따라 다른 결과물을 렌더링 하는 것을 의미한다. 

아래는 `App` 컴포넌트에서 전달한 `isSpecial` 값에 따라     
`Hello` 컴포넌트에서 `<b>*</b>`를 조건부 렌더링하는 예제이다. 

```js
// App.js
function App() {
  return <Hello isSpecial={true} />
}
```

> [❗❗❗]
> - `true`도 자바스크립트 값이므로 `{}`로 감싸야 한다.   
> - boolean 값을 전달할 때 값을 생략하면 `true`가 기본값으로 전달된다.
>   - `<Hello isSpecial />` 도 위의 코드와 동일하다. 



조건부 렌더링을 하는 방법엔 두 가지가 있다. 



## 1. 삼항연산자 사용

`{}` 안에 삼항연산자를 사용한다. 

> [❗❗❗]   
> JSX에서 `false`, `null`, `undefined` 값은 렌더링되지 않지만    
> `0`은 그대로 문자열 0으로 렌더링 된다. 


```js
// Hello.js
function Hello({ isSpecial }) {
  return (
    <div>
      { isSpecial ? <b>*<b> : null }
      안녕하세요!
    </div>
  );
}
```



## 2. && 연산자 사용

삼항연산자는 보통 내용이 달라져야 할 때 사용하고,   
조건에 따라 단순히 내용을 보이고 숨길 땐 `&&` 연산자를 사용하는 게 간편하다.

```js
// Hello.js
function Hello({ isSpecial }) {
  return (
    <div>
      { isSpecial && <b>*<b> }
      안녕하세요!
    </div>
  );
}
```



