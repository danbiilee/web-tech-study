# react-router

## 1. 설치하기

프로젝트 디렉토리에 `react-router` 라이브러리를 설치한다.

```js
$ yarn add react-router-dom
```

## 2. 적용하기

라우터는 index.js 에서 `BrowserRouter` 라는 컴포넌트를 사용해 구현한다.

```js
// index.js

import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom'; // 1. BrowserRouter를 import한다
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

// 2. App 컴포넌트를 BrowserRouter 컴포넌트로 감싼다
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById('root')
);
```

## 3. Route

`Route` 라는 컴포넌트를 사용해 사용자가 요청하는 주소에 따라 다른 컴포넌트를 보여줄 수 있다.  
사용 방식은 다음과 같다.

```js
<Route path="주소" component={보여주고 싶은 컴포넌트} />
```

App.js 에서 사이트에 가장 처음 들어왔을 때 보여줄 `Home` 컴포넌트와  
사이트의 소개를 보여주는 `About` 컴포넌트가 연결된 `Route`들을 렌더링해보자.

아래대로 코드를 작성후 서버를 시작해 `/`와 `/about` 경로로 들어가보자!

```js
// App.js

import React from 'react';
import { Route } from 'react-router-dom'; // 1
import Home from './Home';
import About from './About';

const App = () => {
  return (
    <div>
      <Route path="/" exact component={Home} /> // 2
      <Route path="/about" component={About} />
    </div>
  );
};

export default App;
```

**1️⃣ `Route` import 하기**

**2️⃣ `Route` 렌더링 하기**

- Home 을 위한 라우트에 `exact`라는 props를 설정해주지 않으면 `/about` 경로와 `/` 경로의 `/`가 일치하여 두 컴포넌트가 전부 보이게 된다!
- `exact` 는 `exact={true}` 를 축약한 것이다.

## 4. Link

리액트 라우터를 사용할 땐 일반 `<a>` 태그를 사용하면 안된다.  
만약 사용한다면, `onClick` 이벤트 핸들러에서 `e.preventDefault()` 를 호출하고 따로 주소를 변환시켜줘야 한다.

대신, 클릭했을 때 다른 주소로 이동시키는 `Link` 라는 컴포넌트를 사용해야 한다.  

그 이유는 `a` 태그의 기본적인 속성이 페이지를 이동시키면서 페이지를 아예 새로 불러오는데,  
그렇게 되면 리액트 앱이 지니고 있던 상태들이 초기화 되면서 렌러딩된 컴포넌트들이 모두 사라지고 새로 렌더링을 하게 되기 때문이다.   
`Link` 컴포넌트는 `HTML5 History API`를 사용해 브라우저의 주소만 바꿀 뿐, 페이지를 새로 불러오지는 않는다. 

```js
// App.js

import React from 'react';
import { Route, Link } from 'react-router-dom'; 
import Home from './Home';
import About from './About';

const App = () => {
  return (
		<div>
			<ul>
				<li>
					<Link to="/">Home</Link>
				</li>
				<li>
					<Link to="/about">About</Link>
				</li>
			</ul>
			<hr />
			<Route path="/" exact component={Home} /> 
			<Route path="/about" component={About} />
    </div>
  );
};

export default App;
```
