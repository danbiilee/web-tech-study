# 서브 라우트

서브 라우트는 라우트 내부에 라우트를 만드는 걸 의미한다.  
간단히 Route 컴포넌트 안에 또 Route 컴포넌트를 렌더링하면 된다. 

그럼 Profiles 컴포넌트를 만들고, 그 안에 각 유저들의 프로필 링크들과 프로필 라우트를 함께 렌더링해보자. 

```js
// Profiles.js

import React from 'react';
import { Link, Route } from 'react-router-dom';
import Profile from './Profile';

const Profiles = () => {
  return (
    <div>
      <h3>User List: </h3>
			<ul>
				<li>
					<Link to="/profiles/danbi">danbi</Link>
				</li>
				<li>
					<Link to="/profiles/ash">ash</Link>
				</li>
			</ul>
			
			<Route 
				path="/profiles" 
				exact 
				render={() => <div>유저를 선택해 주세요.</div>} // 1
			/>
			<Route path="/profiles/:username" component={Profile} />
    </div>
  );
};

export default Profiles;
```

**1️⃣ render**
- Route 컴포넌트에서는 `component` 대신 `render`를 사용해 JSX 자체를 렌더링할 수도 있다. 
- JSX 자체를 렌더링하는 것이므로, 필요하다면 props나 기타 값들을 전달해 줄 수도 있다. 


그 다음, App 컴포넌트에서 Profiles 컴포넌트를 위한 링크와 라우트를 생성해준다.   
(기존 Profiles 라우트는 제거한다.)

```js
import React from 'react';
import { Route, Link } from 'react-router-dom';
import Home from './Home';
import About from './About';
import Profiles from './Profiles';

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
			</ul>
			<hr />  
			<Route path="/" exact component={Home} />
			<Route path="/about" component={About} />
			<Route path="/profiles" component={Profiles} />
		</div>
  );
};

export default App;
```

---
## 서브 라우트 🆚 state
만약 특정 라우트 내에 탭을 만들게 된다면, `state`로 관리하는 것보다 `서브 라우트`로 관리하는 것을 추천한다. 

`setState` 를 통해 값을 관리해줄 필요도 없고,   
링크를 통해 다른 곳에서 쉽게 진입시킬 수도 있고,    
검색엔진 크롤링까지 고려한다면 검색엔진 봇이 더욱 다양한 데이터를 수집해갈 수 있기 때문이다. 
