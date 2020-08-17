# CSS Module

리액트 프로젝트에서 컴포넌트를 스타일링할 때 `CSS Module`이라는 기술을 사용하면,    
CSS 클래스가 중첩되는 것을 완벽히 방지할 수 있다. 



## 1. CSS Module 생성하기

CRA 로 만든 프로젝트에서는 확장자를 `.module.css`로 CSS파일을 생성하면 된다.   

> **(❗️❗️❗️) `css-loader`**   
> `CSS Module` 기능은 webpack에서 사용하는 [css-loader](https://github.com/webpack-contrib/css-loader)에서 지원하는데,   
> CRA로 만든 프로젝트에는 이미 적용되어 있으므로 별도의 라이브러리 설치 없이 사용하면 된다. 

이러면 리액트 컴포넌트 파일에서 해당 CSS 파일을 불러올 떄 CSS 파일에 선언한 클래스명이 모두 고유해진다. 

```scss
// Box.module.css
.Box {
  padding: 2rem;
  background: black;
  color: white;
}
```



## 2. CSS Module 사용하기
 

```js
// Box.js
import React from 'react';
import styles from './Box.module.css'; // 1

function Box() {
  return <div className={styles.Box}></div> // 2
}
```

**1️⃣ `.module.css` 파일 import 하기**

**2️⃣ className 설정하기**
- `className` 을 설정할 땐 `.module.css` 파일을 `import`할 때 불러온 `styles` 객체 안에 있는 값을 참조해야 한다.

