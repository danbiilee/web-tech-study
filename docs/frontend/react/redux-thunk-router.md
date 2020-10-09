# thunk 함수에서 라우터 연동하기

로그인 요청을 할 때 로그인 성공 시 `/` 경로로 이동시키고, 실패 시 경로를 유지하는 등 thunk 함수 내에서 라우터를 사용해야 할 때도 있다.

그냥 컨테이너 컴포넌트 내에서 `withRouter`를 사용해서 `props`로 `history`를 받아와 사용해도 상관은 없지만, thunk에서 처리하면 코드가 훨씬 깔끔해질 수 있다.

## 1. customHistory 만들어 적용하기

thunk 내에서 라우터의 `history` 객체를 사용하려면, `BrowserHistory` 인스턴스를 직접 만들어서 사용해야 한다. 따라서 index.js에서 `createBrowserHistory` 함수로 직접 인스턴스를 생성 후 `BrowserRouter` 컴포넌트 대신 `Router` 컴포넌트를 렌더링 한다. 

그리고 스토어를 만들 때, `redux-thunk`의 `withExtraArgument`를 사용하면 posts 모듈의 thunk 함수에서 사전에 정해준 값들을 참조할 수 있게 된다. 

### index.js

```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { createStore, applyMiddleware } from 'redux';
import { Provider } from 'react-redux';
import rootReducer from './modules';
import logger from 'redux-logger';
import { composeWithDevTools } from 'redux-devtools-extension';
import ReduxThunk from 'redux-thunk';
import { Router } from 'react-router-dom';
import { createBrowserHistory } from 'history';

const customHistory = createBrowserHistory();

const store = createStore(
  rootReducer,
  // logger를 사용하는 경우, logger가 가장 마지막에 와야 한다.
  composeWithDevTools(
    applyMiddleware(
      ReduxThunk.withExtraArgument({ history: customHistory }),
      logger,
    ),
  ),
);

ReactDOM.render(
  <Router history={customHistory}>
    <Provider store={store}>
      <App />
    </Provider>
  </Router>,
  document.getElementById('root'),
);

serviceWorker.unregister();
```

> ❓❓❓ `history` 라이브러리는 `react-router-dom`을 설치하면 자동으로 같이 설치된다. (고 하는데 나는 왜인지 `history` 가 없어서 추가적으로 설치했다.)



## 2. 홈 화면으로 가는 thunk 만들기

그리고 홈 화면으로 가는 thunk 함수를 posts 모듈에 추가한다.

### modules/posts.js
```js
// 세 번째 인자로 ReduxThunk.withExtraArgument로 넣어준 값들을 사용할 수 있다.
// extra.history 와 같은 형식이므로 구조 분해 할당을 해준다.
export const goToHome = () => (dispatch, getState, { history }) => {
  history.push('/');
};
```

이제 PostContainer 컴포넌트에서 이 thunk 함수를 디스패치하면 된다.

### containers/PostContainer.js

```js
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import Post from '../components/Post';
import { getPost, goToHome } from '../modules/posts';

const PostContainer = ({ postId }) => {
  const { loading, data, error } = useSelector(
    state =>
      state.posts.post[postId] || {
        loading: false,
        data: null,
        error: null,
      },
  );
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(getPost(postId));
  }, [dispatch, postId]);

  if (loading && !data) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!data) return null;
  return (
    <>
      <button onClick={() => dispatch(goToHome())}>Go to Home</button>
      <Post post={data} />
    </>
  );
};

export default PostContainer;
```

--- 
지금은 다른 작업을 하지 않고 바로 홈으로 이동하게 했지만,   
`getState()`를 사용해 리덕스 스토어의 상태를 확인 후 조건부로 이동하거나, 특정 API를 호출 후 성공했을 때만 이동하는 형식으로도 구현할 수 있다.