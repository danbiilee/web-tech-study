# Styled Components

## 설치

```js
$ yarn add styled-components
```

## 글로벌 스타일 추가

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
