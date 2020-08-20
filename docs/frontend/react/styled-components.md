# Styled Components

`styled-components`는 CSS in JS, 즉 JS 안에 CSS를 작성하는 기술을 사용하는 라이브러리이다.     
CSS in JS 관련 리액트 라이브러리는 이 외에도 `emotion`, `styled-jsx`가 있다. 




## 1. Tagged Template Literal 

`styled-components` 를 사용하기 전에, `Tagged Template Literal` 문법에 대해 짚고 넘어가자. 

이미 알고 있는 `템플릿 리터럴(Template Literal)` 문법은 문자열 조합을 쉽게 해주는 ES6 문법이다.    
그런데 만약, 템플릿 리터럴을 사용할 때 `${}` 안에 문자열이나 숫자가 아닌 객체나 함수를 넣으면 어떻게 될까?

```js
const object = { a: 1 };
console.log(`${object}`); // "[object Object]"

const fn = () => true;
console.log(`${fn}`); // "() => true"
```

템플릿 리터럴을 사용하면서도 그 안에 넣은 자바스크립트 값을 조회하고 싶을 때,     
`Tagged Template Literal` 문법을 사용하면 된다. 

```js
const red = '빨간색';
const blue = '파란색';
function favoriteColors(texts, ...values) {
	console.log(texts);
	console.log(values);
}
favoriteColors`내가 좋아하는 색은 ${red}와 ${blue}이다.`;
// texts ["내가 좋아하는 색은 ", "와 ", "이다."]
// values ["빨간색", 파란색"]
```

위의 예제처럼 우리가 입력한 문자열이 모두 분해되어, 문자열과 `${}` 를 통해 넣어준 자바스크립트 값을 따로 조회하는 것이 가능해진다. 

> (❗️) 여기서 함수 파라미터에는 `파라미터의 rest` 문법이 사용됐다.    

`styled-components` 에서는 이런 문법을 사용해 아래와 같이 컴포넌트의 `props`를 읽어오기도 한다. 

```js
const StyledDiv = styled`
	background: ${props => props.color};
`;
``` 




## 2. 설치하기

프로젝트 디렉터리에 `styled-components` 설치한다. 

```js
$ yarn add styled-components
```




## 3. 스타일링 컴포넌트 만들기

### 3.1 컴포넌트 만들기

`styled-components` 를 사용하면 `import`한 `styled` 객체를 이용해 특정 스타일을 가진 컴포넌트를 만들 수 있다.

`div` 태그를 스타일링 하고 싶으면 `styled.div`,    
`input` 태그를 스타일링 하고 싶으면 `styled.input` 와 같은 형식으로 사용하면 된다. 

```js
// App.js
import React from 'react';
import styled from 'styled-components';

const Circle = styled.div`
    width: 5rem;
    height: 5rem;
    background: black;
    border-radius: 50%;
`;

function App() {
    return <Circle />;
}

export default App;
```


### 3.2 props 전달하기 

이번엔 Circle 컴포넌트에 color 라는 `props` 를 전달해보자.

```js
// App.js
const Circle = styled.div`
    width: 5rem;
    height: 5rem;
    background: ${props => props.color || 'black'};
    border-radius: 50%;
`;

function App() {
    return <Circle color="blue" />;
}
```

color 라는 `props` 값을 설정했으면 해당 값을 배경색으로 설정하고,     
그렇지 않으면 검정색을 배경색으로 설정하도록 코드를 수정했다. 


### 3.3 css 기능 사용하기

이번엔 huge 라는 `props`를 전달해보자.

```js
// App.js
import React from 'react';
import styled, { css } from 'styled-components';

const Circle = styled.div`
    width: 5rem;
    height: 5rem;
    background: ${props => props.color || 'black'};
    border-radius: 50%;
    ${props =>
        props.huge && 
        css`
            width: 10rem;
            height: 10rem;
        `}
`;

function App() {
    return <Circle color="blue" huge />;
}
```

이렇게 여러 줄의 CSS 코드를 조건부로 설정하고 싶다면,     
`styled-components` 라이브러리에서 `css` 객체를 추가적으로 `import`하여 사용해야 한다. 


### 3.4 ThemeProvider 기능 사용하기

특정 변수를 특정 컴포넌트에서 선언해 해당 컴포넌트에서만 사용하는 대신,       
`ThemeProvider` 라는 기능을 사용하면 `styled-components`로 만드는 모든 컴포넌트에서 조회해 사용할 수 있는 **전역적인 값**을 선언할 수 있다. 

```js
// App.js
import React from 'react';
import styled, { ThemeProvider } from 'styled-components';
import Button from './components/Button';

const AppBlock = styled.div`
    width: 512px;
    margin: 4rem auto 0;
    padding: 1rem;
    border: 1px solid black;
`;

function App() {
    return (
        <ThemeProvider
            theme={{
                palette: {
                    blue: '#228be6',
                    gray: '#495057',
                    pink: '#f06595'
                }
            }}
        >
            <AppBlock>
                <Button>BUTTON</Button>
            </AppBlock>
        </ThemeProvider>
    );
}
``` 

이렇게 `ThemeProvider`에 `theme` 속성을 설정하면,    
`ThemeProvider` 내부에 렌더링된 `styled-components` 로 만든 모든 컴포넌트에서 `palette` 를 조회해 색상 값들을 사용할 수 있게 된다. 

Button 컴포넌트에서 위에 선언한 `palette.blue` 값을 조회해보자.

```js
// Button.js
import React from 'react';
import styled, { css } from 'styled-components';
import { darken, lighten } from 'polished';

const StyledButton = styled.button`
	/* ... */
	${props => {
		const selected = props.theme.palette.blue;
		return css`
			background: ${selected};
			&:hover {
				background: ${lighten(0.1, selected)};
			}
			&:active {
				background: ${darken(0.1, selected)};
			}
		`;
	}}
`;
```

`ThemeProvider` 로 설정한 값은 `props.theme` 으로 조회할 수 있다.    

위에서는 `selected` 값이 무조건 blue 를 가리키게 했지만,   
Button 컴포넌트가 color 라는 `props`를 통해 받아오게 될 색상으로 수정하면 아래와 같다. 

```js
const selected = props.theme.palette[props.color];
```




## 4. polished 라이브러리 

Sass에서 `lighten()`, `darken()` 과 같은 유틸 함수를 사용했던 것처럼,    
CSS in JS 에서도 `polished` 라이브러리를 설치하면 비슷한 유틸 함수를 사용할 수 있다. 


### 4.1 설치하기

```js
$ yarn add polished
```

### 4.2 사용하기

```js
import React from 'react';
import styled from 'styled-components';
import { darken, lighten } from 'poslished';

const StyledButton = styled.button`
    background: #228be6;
    &:hover {
        background: ${lighten(0.1, '#228be6')};
    }
    &:active {
        background: ${darken(0.1, '#228be6')};
    }
`;
```




## 5. 글로벌 스타일 

특정 컴포넌트를 만들어서 스타일링 하는 게 아니라 글로벌 스타일링을 추가할 땐,  
 `styled-components`의 `createGlobalStyle` 을 사용한다.

아래는 페이지에 배경색을 적용하는 예제이다.

```js
// App.js
import React from 'react';
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
  body {
      background: #e9ecef;
  }
`;

function App() {
    return (
        <>
           <GlobalStyle />
           <div>배경색 적용!<div>
        </>
    );
}
```
