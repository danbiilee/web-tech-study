# 리액트 라우터 부가기능

## 1. history 객체
`history` 객체는 Route 컴포넌트에게 `match`, `location` 과 함께 전달되는 props 중 하나이다. 이 객체를 통해, 컴포넌트 내에 구현하는 메소드에서 라우터에 직접 접근을 할 수 있다. 

➡️ 뒤로가기, 특정 경로로 이동, 이탈 방지 등... 


아래는 `history` 객체를 사용하는 예제 페이지이다. 

```js
// HistorySample.js
import React, { useEffect } from 'react';

function HistorySample({ history }) {
	const goBack = () => {
		history.goBack();
	};

	const goHome = () => {
		history.push('/');
	};
	
	useEffect(() => { // 1
		const unblock = history.block('Will you really leave?');
		return () => {
			unblock();
		};
	}, [history]);

	return (
		<div>
			<button onClick={goBack}>Go Back</button>
			<button onClick={goHome}>Go Home</button>
		</div>
  );
}

export default HistorySample;
```

**1️⃣ useEffect를 사용한 이탈 방지**
- `history` 객체의 값이 바뀌기 전, 즉 사용자가 다른 경로로 이동하려고할 때 unblock 함수가 실행되며 이탈을 방지하게 된다. 
> 참고 [리액트의 useEffect](./useEffect.md)

이제 App 컴포넌트에서 렌더링 후 버튼들을 눌러보자. 

```js
// App.js 

import React from 'react';
import { Route, Link } from 'react-router-dom';
import Home from './Home';
import About from './About';
import Profiles from './Profiles';
import HistorySample from './HistorySample';

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
				<li>
					<Link to="/profiles">User List</Link>
				</li>
				<li>
					<Link to="/history">History Example</Link>
				</li>
			</ul>
			<hr />  
			<Route path="/" exact component={Home} />
			<Route path="/about" component={About} />
			<Route path="/profiles" component={Profiles} />
			<Route path="/history" component={HistorySample} />
		</div>
  );
};

export default App;
```



## 2. withRouter HoC
## 3. Switch
## 4. NavLink
## 5. 그 외

