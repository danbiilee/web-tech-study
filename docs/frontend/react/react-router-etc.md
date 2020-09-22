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

  useEffect(() => {
    // 1
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

`withRouter HoC` 은 라우트 컴포넌트가 아닌 곳에서 `match`, `location`, `history`를 사용해야할 때 쓴다.

```js
// WithRouterSample.js
import React from 'react';
import { withRouter } from 'react-router-dom';

const WithRouterSample = ({ location, match, history }) => {
  return (
    <div>
      <h4>location</h4>
      <textarea value={JSON.stringify(location, null, 2)} readOnly />
      <h4>match</h4>
      <textarea value={JSON.stringify(match, null, 2)} readOnly />
      <button onClick={() => history.push('/')}>Go Home</button>
    </div>
  );
};

export default withRouter(WithRouterSample);
```

위처럼 `match`, `location`, `history`를 사용해 코드를 작성 후, export 할 때 컴포넌트를 `withRouter`로 감싸주면 된다.

이제 WithRouterSample 컴포넌트를 Profiles.js 에서 렌더링해보자.

```js
// Profiles.js
import React from 'react';
import { NavLink, Route } from 'react-router-dom';
import Profile from './Profile';
import WithRouterSample from './WithRouterSample';

const Profiles = () => {
  return (
    <div>
      <h3>User List: </h3>
      <ul>{/* ... */}</ul>

      <Route path="/profiles" exact render={() => <div>Select a User!</div>} />
      <Route path="/profiles/:username" component={Profile} />
      <WithRouterSample />
    </div>
  );
};

export default Profiles;
```

`withRouter`를 사용하면, 자신의 **부모 컴포넌트 기준의 `match`** 값이 전달된다.

여기선 WithRouterSample 컴포넌트의 부모 컴포넌트가 Profiles 컴포넌트이고, Profiles 컴포넌트는 라우트 `/profiles` 와 규칙이 일치한다.
그러므로 특정 유저를 선택하여 `/profiles/name` 이라고 URL Params가 있는 상황이 되어도, `match.params` 값이 비어있게 된다.

## 3. Switch

`Switch`는 여러 라우트들을 감싸서 그 중 규칙이 일치하는 라우트 단 하나만을 렌더링 해준다.  
`Switch`를 사용하면, 아래처럼 어떤 라우트와도 일치하지 않을 때 보여줄 Not Found 페이지를 구현할 수도 있다.

```js
// App.js

import React from 'react';
import { Route, Link, Switch } from 'react-router-dom';
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
      <Switch>
        <Route path="/" exact component={Home} />
        <Route path="/about" component={About} />
        <Route path="/profiles" component={Profiles} />
        <Route path="/history" component={HistorySample} />
        <Route
          render={({ location }) => (
            <div>
              <h2>This page in not found!</h2>
              <p>{location.pathname}</p>
            </div>
          )}
        />{' '}
        // 1
      </Switch>
    </div>
  );
};

export default App;
```

**1️⃣ Not Found 컴포넌트 구현**

- 특정 path를 지정하지 않으면, 위 4개의 path들을 제외한 나머지 path들에 대해 Not Found를 렌더링하게 된다.

## 4. NavLink

`NavLink` 는 `Link` 컴포넌트와 비슷한데,  
현재 경로와 `Link` 에서 사용하는 경로가 일치하는 경우 특정 스타일 혹은 클래스를 적용할 수 있는 컴포넌트이다.

## 5. 그 외
