# Sass(SCSS)

`Sass(Syntactically Awesome Style Sheets: 문법적으로 짱 멋진 스타일시트)`는 `CSS pre-processor`로서,  
복잡한 작업을 쉽게 할 수 있게 해줄 뿐만 아니라 코드의 재활용성을 높여주고, 코드의 가독성을 높여주어 유지보수를 쉽게 해준다.



## 1. 시작하기

리액트 프로젝트를 생성하고, 해당 프로젝트 디렉터리에 `node-sass` 라이브러리를 설치한다.

이 라이브러리는 Sass를 CSS로 컴파일해주는 역할을 한다.

```js
$ yarn add node-sass
```



## 2. 컴포넌트에 스타일 적용하기

Button이라는 컴포넌트를 만들고, SCSS를 사용해 스타일링을 해보자.

src 디렉터리에 components 디렉터리를 생성 후 그 안에 Button.js 파일을 생성한다.  
그리고 components 디렉터리에 Button.scss 파일도 생성한다.

```js
// components/Button.js

import React from "react";
import "./Button.scss"; // 1

function Button({ children }) {
  return (
    <button className="Button"> // 2
      {children}
    </button>
  );
}

export default Button;
```

```scss
// components/Button.scss

$blue: #228be6; //변수 선언

.Button { // 2
  display: inline-flex;
  color: white;
  font-weight: bold;
  outline: none;
  border-radius: 4px;
  border: none;
  cursor: pointer;

  height: 2.25rem;
  padding-left: 1rem;
  padding-right: 1rem;
  font-size: 1rem;

  background: $blue; // 변수 사용
  &:hover {
    background: lighten($blue, 10%); // 색상 10% 밝게
  }

  &:active {
    background: darken($blue, 10%); // 색상 10% 어둡게
  }
}
```

**1️⃣ scss 파일 임포트하기**

**2️⃣ 클래스 이름 지정하기**
- `button` 태그에 `className`이라는 속성으로 scss파일에서 클래스 선택자로 설정했던 이름(`.Button`)을 지정한다. 




## 3. className에 동적으로 CSS 클래스 이름 지정하기

이번엔 버튼의 크기를 `large`, `medium`, `small`로 설정할 수 있도록 구현해보자. 

먼저 `Button.js`에서 `defaultProps`를 통해 `size`의 기본값을 `medium`으로 설정하고,    
이 값을 button 태그의 `className`에 지정한다. 

```js
// components/Button.js

import React from 'react';
import './Button.scss'; 

function Button({ children, size }) {
  return (
    <button className='Button'> 
      {children}
    </button>
  );
}

Button.defaultProps = {
  size: 'medium',
};
```

이 때 `className`에 들어가게 될,   
`props`로 전달받은 `size`는 문자열이 아니고 동적으로 변하는 값이므로 아래와 같은 방법으로 처리해주어야 한다. 


### 3.1 join 함수 사용하기
```js
className = {['Button', size].join(' ')}
```

### 3.2 템플릿 리터럴 사용하기
```js
className={`Button ${size}`}
```

### 3.3 classnames 라이브러리 사용하기
조건부 스타일링을 할 때 위 방법처럼 문자열을 직접 조합해주는 것보다 `classnames`라는 라이브러리를 사용하는 게 훨씬 편하다.

함수의 인자에 문자열, 배열, 객체 등을 전달해 손쉽게 문자열을 조합할 수 있다.

우선 프로젝트에 설치를 하고,

```js
yarn add classnames
```

아래와 같이 사용하면 된다.
```js
classNames('Button', 'size'); // 'Button size'
classNames('Button', { size: true }); // 'Button size'
classNames({ 'button-size' : true }); // 'button-size'
classNames({ 'button-size' : false }); // ''
classNames({ Button: true }, { size: true }); // 'Button size'
classNames({ Button: true, size: true }); // 'Button size'
classNames(['Button', 'size']); // 'Button size'
classNames('Button', { size: true, color: false }, 'bold', { active: true }); // 'Button size bold active'

// false, null, 0, undefined는 무시된다.
classNames(null, false, 'Button', 0, 1, {size: null}); // 'Button 1'
```

---
이제 `props` 로 받은 `size` 값이 button 태그의 `className`으로 무사히 전달될 것이다.    
이에 따라 `Button.scss`에서 다른 크기를 지정해보자.

```scss
.Button {
  display: inline-flex;
  align-items: center;
  border-radius: 4px;
  border: none;
  color: white;
  font-weight: bold;
  outline: none;
  cursor: pointer;

  // 사이즈 관리
  &.large {
    height: 3rem;
    padding: 0 1rem;
    font-size: 1.25rem;
  }
  &.medium {
    height: 2.25rem;
    padding: 0 1rem;
    font-size: 1rem;
  }
  &.small {
    height: 1.75rem;
    padding: 0 1rem;
    font-size: 0.875rem;
  }
  
  // ...
}
```

위 코드에서 

```scss
.Button {
  &.large {

  }
}
```

가 의미하는 것은

```scss
.Button.large{

}
```

이다. 즉, `Button`과 `large` CSS 클래스가 함께 적용되어 있으면 해당 스타일을 적용하겠다는 것을 의미한다.  

> ➕ [SCSS 중첩 더 알아보기](./../SCSS/nesting.md)


그리고 `App.js`에서 버튼들을 렌더링할 때 `size` 값도 설정해준다.

```js
// App.js

import React from 'react';
import Button from './components/Button';

function App() {
  return (
    <div>
        <Button size="large">BUTTON</Button>
        <Button>BUTTON</Button>
        <Button size="small">BUTTON</Button>
    </div>
  );
}
```

버튼들끼리 함께 있을 때 여백을 주기 위해 `Button.scss`에 아래와 같은 스타일을 추가한다. 

```scss
.Button {
  & + & {
    margin-left: 1rem;
  }
}
```

`& + &`가 의미하는 것은 `.Button + .Button`으로,     
두 버튼이 함께 있는 경우 두 번쨰 버튼에 여백이 설정된다.  




## 4. ...rest props 전달하기

컴포넌트에 필요한 이벤트가 있을 때마다 매번 `props`에 인자를 전달하는 것은 번거롭다. 

이러한 문제는 `spread` 와 `rest` 문법을 사용하여 해결할 수 있다.    
이 문법은 주로 배열, 객체, 함수의 파라미터, 인자를 다룰 때 사용하지만 컴포넌트에서도 사용할 수 있다. 

```js
//components/Button.js

function Button({ children, size, color, outline, fullWidth, ...rest }) { // 1
  return (
    <button
      className={className('Buton', size, color, outline, fullWidth)}
      {...rest} // 2
    >
      {children}
    </button>2️
  );
}
```


1️⃣처럼 함수의 파라미터에 `...rest`를 사용하면 직접 지정한 `children, size, ..., fullWidth`를 제외한 값들을 `rest` 객체에 모아주고,    
2️⃣처럼 button 태그에 `{...rest}` 를 해주면 `rest` 객체 안에 있는 값들을 모두 button 태그에 설정해준다. 


```js
// App.js

function App() {
  return (
    <div>
      <Button 
        size="large" 
        onClick={() => conosle.log('Click!')}
      >
        BUTTON
      </Button>
      <Button>BUTTON</Button>
      <Button size="small">BUTTON</Button>
    </div>
  );
}
```

위의 경우 Button 컴포넌트에서 `onClick` 이라는 `props`를 직접 전달받고 설정하지 않았지만,  
첫 번째 버튼의 클릭 이벤트는 정상적으로 작동함을 확인할 수 있다. 

이렇게 컴포넌트가 어떤 `props`를 받을 지 확실하지 않지만,   
그대로 컴포넌트 또는 HTML 태그에 전달해줘야하는 상황에
`...rest` 문법을 사용하면 된다. 