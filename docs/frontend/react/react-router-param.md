# 파라미터와 쿼리

페이지 주소를 정의할 때 유동적인 값을 전달해야할 때도 있다.  
이는 파라미터와 쿼리로 나누어진다.

일반적으로 파라미터는 특정 id나 이름으로 조회할 때 사용하고  
쿼리는 어떤 키워드를 검색하거나, 요청할 때 필요한 옵션을 전달할 때 사용한다.

```js
// 파라미터
/profiles/name
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
