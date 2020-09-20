# 파라미터와 쿼리

페이지 주소를 정의할 때 유동적인 값을 전달해야할 때도 있다.  
이는 파라미터와 쿼리로 나누어진다.

일반적으로 **파라미터**는 특정 id나 이름으로 조회할 때 사용하고  
**쿼리**는 어떤 키워드를 검색하거나, 요청할 때 필요한 옵션을 전달할 때 사용한다.

```js
// 파라미터
/profiles/name
// 쿼리
/about?details=true
```

## 1. URL Params

Profile 컴포넌트에서 파라미터를 사용해보자.  
`/profile/danbi` 이런 식으로 뒤에 username을 전달하고, 해당 값을 파라미터로 받아온다.

```js
// Profile.js
import React from 'react';

const profileData = {
  danbi: {
    name: '이단비',
    description: '개발자',
  },
  ash: {
    name: 'ASH ISLAND',
    description: '래퍼',
  },
};

const Profile = ({ match }) => {
  const { username } = match.params; // 1
  const profile = profileData[username];
  if (!profile) {
    return <div>User is not existed!</div>;
  }
  return (
    <div>
      <h3>
        {username}({profile.name})
      </h3>
      <p>{profile.description}</p>
    </div>
  );
};

export default Profile;
```

**1️⃣ 파라미터 받아오기**

- 파라미터는 `match` 객체의 `params` 값을 참조하여 받아온다.

**✨`match` 객체**

- [match](https://reactrouter.com/web/api/match) 객체 안에는 현재 주소가 `Route` 컴포넌트에서 정한 규칙과 어떻게 일치하는 지에 대한 정보가 들어있다.

이제 App 컴포넌트에서 `/profiles/:username` 이라고 path 규칙을 정해주면,  
username에 해당하는 값이 `match.params`에 담겨 Profile 컴포넌트에서 match props를 통해 전달받을 수 있게 된다.

```js
// App.js
import React from 'react';
import { Route } from 'react-router-dom';
import Profile from './Profile';

const App = () => {
  return (
    <div>
      <Route path="/profiles/:username" component={Profile} />
    </div>
  );
};

export default App;
```


## 2. Query

이번엔 About 컴포넌트에서 쿼리를 사용해보자.  
쿼리는 props로 전달되는 `location` 객체에 있는 `search` 값에서 읽어올 수 있다. 
`location` 객체는 아래와 같이 현재 앱이 갖고 있는 주소에 대한 정보를 지니고 있다. 

```js
{
  key: 'ac3df4', // not with HashHistory!
  pathname: '/somewhere'
  search: '?some=search-string',
  hash: '#howdy',
  state: {
    [userDefined]: true
  }
}
```

이 때 `search` 값은 문자열 형태로 되어있으므로, `qs` 라이브러리를 사용해 객체 형태로 변환해준다. 

먼저 라이브러리를 설치한다. 

```js
$ yarn add qs
```

`/about/detail=true` 경로에 들어갔을 때, About 컴포넌트의 추가 정보가 보일 수 있도록 구현해보자.    
About 컴포넌트에서 전달받은 `location.search` 의 detail 값을 받아와서, 해당 값이 true일 때 추가 정보를 보여주도록 하자. 


```js
// About.js

import React from 'react';
import qs from 'qs';

const About = ({ location }) => {
	const query = qs.parse(location.search, {
		ignoreQueryPrefix: true,
	});
	const detail = query.detail === 'true'; // 쿼리의 파싱 결과값은 문자열이다.

	return (
		<div>
			<h1>소개</h1>
			<p>This is exercise project that practices react-router.</p>
			{detail && <p>Additional infromation is blah blah...</p>}
		</div>
	);
};

export default About;
```