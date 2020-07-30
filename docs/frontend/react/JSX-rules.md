# JSX의 기본 규칙

`JSX`는 리액트에서 생김새를 정의할 때 사용하는 문법이다.    
HTML 같지만 실제로는 Javascript이다. 

리액트 컴포넌트 파일에서 XML 형태로 코드를 작성하면 `Babel`이 JSX를 Javascript로 변환해준다.    

> **✨ Babel**
> - Javascript의 문법을 확장해주는 도구이다.    
> - 최신 문법, 편의상 사용하는 문법, 실험적 문법들을 정식 자바스크립트 형태로 변환해줌으로써, 구형 브라우저 환경에서도 제대로 실행할 수 있게 해주는 역할을 한다. 
> - [바벨 컴파일 확인](https://babeljs.io/repl#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Creact%2Cstage-2%2Cenv&prettier=false&targets=&version=7.10.5&externalPlugins=)


JSX가 Javascript로 제대로 변환되려면 아래와 같이 지켜야 하는 규칙들이 있다!



## 1. 태그 닫기

태그는 꼭 닫혀야 한다.    
태그와 태그 사이에 내용이 들어가지 않을 땐, 열리고 바로 닫히는 `Self Closing` 태그를 사용해야 한다. 

```js
<div>Hello</div>

// Self Closing Tag
<input />
<Hello /> 
```



## 2. 태그 감싸기

두 개 이상의 태그는 꼭 하나의 태그로 감싸야 한다.

```js
return (
  <div>
    <Hello />
    <div>안녕</div>
  </div>
);
```

위처럼 단순히 `Hello`와 `div`태그를 감싸기 위해 다시 한 번 `div`태그로 감싸는게 불필요할 수 있다.    
그럴 땐, 리액트의 `Fragment`를 사용하면 된다. 

이름 없이 태그를 작성하게 되면 `Fragment`가 만들어지게 되고, 이는 브라우저 상에서 별도의 엘리먼트로 나타나지 않는다. 

```js
return (
  <>
    <Hello />
    <div>안녕</div>
  </>
);
```



## 4. 자바스크립트 값 사용하기

JSX 내부에 자바스크립트 변수의 값을 보여줘야 할 땐, 변수를 `{}`로 감싸서 보여준다. 

```js
function App() {
  const name = 'react';
  return (
    <>
      <Hello />
      <div>{name}</div>
    </>
  );
}
```



## 5. 스타일 적용하기 

JSX에서 style 속성과 CSS class를 설정하는 방법이다. 


### 5.1 인라인 스타일

인라인 스타일은 객체로 작성해야 하며,    
`border-color`처럼 `-`로 구분되어 있는 속성은 `borderColor`처럼 `camelCase` 형태로 네이밍 해야 한다. 

```js
function App() {
  const style = {
    backgroundColor: 'gray',
    fontSize: 24, // 기본 단위 px
    padding: '1rem' // px가 아닌 다른 단위 사용시 문자열로 작성
  }

  return (
    <>
      <Hello />
      <div style={style}>react</div>
    </>
  )
}
```

`style`이라는 변수에 객체로 인라인 스타일을 설정하고, `div`태그의 `style` 속성에 전달한다.     
이 때 전달되는 객체는 자바스크립트 값이기 때문에 `{}`를 통해 전달한다.  


### 5.2 CSS class

CSS class는 기존 HTML처럼 `class=`가 아닌 `className=`에 설정해야 한다.    

`App.css`의 `.gray-box` 클래스를 적용하고 싶다면 아래와 같은 과정을 따라야 한다. 

```css
/* App.css */
.gray-box {
  background: gray;
}
```

```js
// App.js
import './App.css';

function App() {
  return <div className="gray-box"></div>
}
```



## 6. 주석

JSX 내부에서 주석은 `{/* ... */}` 의 형태로 작성한다.    
열리는 태그 내부에서 주석은 `// ...` 의 형태로 작성한다. 

```js
return (
  <div>
    {/* 주석1 */}
    <input 
      // 주석2
    /> 
  </div>
)
```
