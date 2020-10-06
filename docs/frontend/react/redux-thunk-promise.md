# redux-thunk로 프로미스 다루기

> ✨ 프로미스 공부하기
>
> - https://learnjs.vlpt.us/async/
> - https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise

## 1. 가짜 API 함수 만들기

먼저 Promise를 사용해 데이터를 반환하는 가짜 API 함수를 만들어보자.

src 디렉터리에 api 디렉터리를 만들고, posts.js 파일을 생성 후 다음과 같이 코드를 작성한다.

### api/posts.js

```js
// n 밀리세컨드 동안 기다리는 프로미스를 만들어주는 함수
const sleep = (n) => new Promise((resolve) => setTimeout(resolve, n));

// 가짜 포스트 목록 데이터
const posts = [
  {
    id: 1,
    title: '리덕스 미들웨어를 배워봅시다',
    body: '리덕스 미들웨어를 직접 만들어보면 이해하기 쉽죠.',
  },
  {
    id: 2,
    title: 'redux-thunk를 사용해봅시다',
    body: 'redux-thunk를 사용해서 비동기 작업을 처리해봅시다!',
  },
  {
    id: 3,
    title: 'redux-saga도 사용해봅시다',
    body:
      '나중엔 redux-saga를 사용해서 비동기 작업을 처리하는 방법도 배워볼 거예요.',
  },
];

// 포스트 목록을 가져오는 비동기 함수
export const getPosts = async () => {
  await sleep(500); // 0.5초 기다리기
  return posts;
};

// id로 포스트를 조회하는 비동기 함수
export const getPostById = async (id) => {
  await sleep(500); // 0.5초 기다리기
  return posts.find((post) => post.id === id);
};
```

## 2. 리덕스 모듈 만들기

이제 posts 라는 리덕스 모듈을 만들어보겠다.

프로미스를 다루는 리덕스 모듈을 만들 땐 다음과 같은 사항들을 고려해야 한다.

1. 프로미스가 시작, 성공, 실패했을 때 다른 액션을 디스패치 해야 한다.
2. 각 프로미스마다 thunk 함수를 만들어야 한다.
3. 리듀서에서 액션에 따라 로딩중, 결과, 에러 상태를 변경해야 한다.

### modules/posts.js

```js
import * as postApi from '../api/posts'; // api/posts 안의 함수 모두 불러오기

// 액션 타입
// 포스트 목록 조회
const GET_POSTS = 'posts/GET_POSTS'; // 요청 시작
const GET_POSTS_SUCCESS = 'posts/GET_POSTS_SUCCESS'; // 요청 성공
const GET_POSTS_ERROR = 'posts/GET_POSTS_ERROR'; // 요청 실패
// 포스트 하나 조회
const GET_POST = 'posts/GET_POST';
const GET_POST_SUCCESS = 'posts/GET_POST_SUCCESS';
const GET_POST_ERROR = 'posts/GET_POST_ERROR';

// thunk 함수
// 모든 액션들에 대해 액션 생성함수를 만들 필요 없이,
// thunk 함수 안에서 바로 액션 객체를 만들어도 된다.
export const getPosts = () => async (dispatch) => {
  try {
    dispatch({ type: GET_POSTS });
    const posts = postApi.getPosts();
    dispatch({ type: GET_POSTS_SUCCESS, posts });
  } catch (e) {
    dispatch({ type: GET_POSTS_ERROR, error: e });
  }
};
export const getPost = (id) => async (dispatch) => {
  try {
    dispatch({ type: GET_POST });
    const post = postApi.getPostById(id);
    dispatch({ type: GET_POSTS_SUCCESS, id, post });
  } catch (e) {
    dispatch({ type: GET_POSTS_ERROR, error: e });
  }
};

// 초깃값
const initialState = {
  posts: {
    loading: false,
    data: null,
    error: null,
  },
  post: {
    loading: false,
    data: null,
    error: null,
  },
};

// 리듀서
export default function posts(state = initialState, action) {
  switch (action.type) {
    case GET_POSTS:
      return {
        ...state,
        posts: {
          loading: true,
          data: null,
          error: null,
        },
      };
    case GET_POSTS_SUCCESS:
      return {
        ...state,
        posts: {
          loading: false,
          data: action.posts,
          error: null,
        },
      };
    case GET_POSTS_ERROR:
      return {
        ...state,
        posts: {
          loading: false,
          data: null,
          error,
        },
      };
    case GET_POST:
      return {
        ...state,
        post: {
          loading: true,
          data: null,
          error: null,
        },
      };
    case GET_POST_SUCCESS:
      return {
        ...state,
        post: {
          loading: false,
          data: action.post,
          error: null,
        },
      };
    case GET_POST_ERROR:
      return {
        ...state,
        post: {
          loading: false,
          data: null,
          error,
        },
      };
    default:
      return state;
  }
}
```

## 3. 리덕스 모듈 리팩토링하기

현재 posts 모듈은 반복되는 코드들이 상당히 많다. 이런 반복되는 코드들은 함수화하여 리팩토링하는 것이 바람직하다.

먼저 src 디렉터리에 lib 디렉터리를 만들고, 그 안에 asyncUtils.js 라는 파일을 생성 후 함수들을 다음과 같이 작성한다.

### lib/asyncUtils.js

```js
// Promise에 기반한 thunk를 만들어주는 함수
export const createPromiseThunk = (type, promiseCreator) => {
  const [SUCCESS, ERROR] = [`${type}_SUCCESS`, `${type}_ERROR`];

  // 이 thunk 함수는 promiseCreator가 하나의 파라미터만 받는다는 전제하에 작성했다.
  // 만약 여러 종류의 파라미터를 전달해야 한다면, 객체 타입의 파라미터를 받아오도록 하자.
  // 예: writeComment({ postId: 1, text: '댓글' });
  return (param) => async (dispatch) => {
    try {
      dispatch({ type, param }); // 요청 시작
      const payload = await promiseCreator(param); // 결과의 이름을 payload로 통일
      dispatch({ type: SUCCESS, payload, param }); // 성공
    } catch (e) {
      dispatch({ type: ERROR, error: e }); // 실패
    }
  };
};

// 리듀서에서 사용할 수 있는 여러 유틸 함수들
export const reducerUtils = {
  // 초기상태: 초기 data의 기본값을 null로 설정했지만 변경 가능하다.
  initial: (initialData = null) => ({
    loading: false,
    data: initialData,
    error: null,
  }),
  // 로딩중 상태: prevData의 기본값을 null로 설정했지만,
  // 다른 값을 지정하면 null로 초기화하지 않고 다른 값을 유지할 수 있다.
  loading: (prevData = null) => ({
    loading: true,
    data: prevData,
    error: null,
  }),
  // 성공 상태
  success: (data) => ({
    loading: false,
    data,
    error: null,
  }),
  // 실패 상태
  error: (error) => ({
    loading: false,
    data: null,
    error,
  }),
};
```

이제 이 함수들을 사용해 기존 posts 모듈을 리팩토링 해보자.

### modules/posts.js

```js
import * as postApi from '../api/posts'; // api/posts 안의 함수 모두 불러오기
import { createPromiseThunk, reducerUtils } from '../lib/asyncUtils';

// 액션 타입
// 포스트 목록 조회
const GET_POSTS = 'posts/GET_POSTS';
const GET_POSTS_SUCCESS = 'posts/GET_POSTS_SUCCESS';
const GET_POSTS_ERROR = 'posts/GET_POSTS_ERROR';
// 포스트 하나 조회
const GET_POST = 'posts/GET_POST';
const GET_POST_SUCCESS = 'posts/GET_POST_SUCCESS';
const GET_POST_ERROR = 'posts/GET_POST_ERROR';

// thunk 함수를 만드는 게 이렇게 간단해졌다!
export const getPosts = createPromiseThunk(GET_POSTS, postApi.getPosts);
export const getPost = createPromiseThunk(GET_POST, postApi.getPostById);

// 초깃값도 reducerUtils의 함수를 사용해 리팩토링했다.
const initialState = {
  posts: reducerUtils.initial(),
  post: reducerUtils.initial(),
};

// 리듀서 안의 상태값도 reducerUtils의 함수를 사용해 리팩토링했다.
export default function posts(state = initialState, action) {
  switch (action.type) {
    case GET_POSTS:
      return {
        ...state,
        posts: reducerUtils.loading(),
      };
    case GET_POSTS_SUCCESS:
      return {
        ...state,
        // action.posts -> action.payload
        posts: reducerUtils.success(action.paylod),
      };
    case GET_POSTS_ERROR:
      return {
        ...state,
        posts: reducerUtils.error(action.error),
      };
    case GET_POST:
      return {
        ...state,
        post: reducerUtils.loading(),
      };
    case GET_POST_SUCCESS:
      return {
        ...state,
        // action.post -> action.payload
        post: reducerUtils.success(action.paylod),
      };
    case GET_POST_ERROR:
      return {
        ...state,
        post: reducerUtils.error(action.error),
      };
    default:
      return state;
  }
}
```

만약 리듀서 쪽의 반복되는 코드들을 더 줄이고 싶다면, asyncUtils.js 에서 아래와 같이 `handleAsyncActions` 함수를 추가하도록 한다.

### lib/asyncUtils.js

```js
// ...

// 비동기 관련 액션들을 처리하는 리듀서를 만들어주는 함수
// type: 액션 타입, key: 상태의 key(posts, post)
export const handleAsyncActions = (type, key) => {
  const [SUCCESS, ERROR] = [`${type}_SUCCESS`, `${type}_ERROR`];
  return (state, action) => {
    switch (action.type) {
      case type:
        return {
          ...state,
          [key]: reducerUtils.loading(),
        };
      case SUCCESS:
        return {
          ...state,
          [key]: reducerUtils.success(action.paylod),
        };
      case ERROR:
        return {
          ...state,
          [key]: reducerUtils.error(action.error),
        };
      default:
        return state;
    }
  };
};
```

`handleAsyncActions` 함수를 만들었다면, posts 리듀서를 다음과 같이 최종적으로 리팩토링 할 수 있다.

### moudles/posts.js

```js
import * as postApi from '../api/posts'; // api/posts 안의 함수 모두 불러오기
import {
  createPromiseThunk,
  handleAsyncActions,
  reducerUtils,
} from '../lib/asyncUtils';

// 액션 타입
// 포스트 목록 조회
const GET_POSTS = 'posts/GET_POSTS';
const GET_POSTS_SUCCESS = 'posts/GET_POSTS_SUCCESS';
const GET_POSTS_ERROR = 'posts/GET_POSTS_ERROR';
// 포스트 하나 조회
const GET_POST = 'posts/GET_POST';
const GET_POST_SUCCESS = 'posts/GET_POST_SUCCESS';
const GET_POST_ERROR = 'posts/GET_POST_ERROR';

// thunk 함수
export const getPosts = createPromiseThunk(GET_POSTS, postApi.getPosts);
export const getPost = createPromiseThunk(GET_POST, postApi.getPostById);

// 초깃값
const initialState = {
  posts: reducerUtils.initial(),
  post: reducerUtils.initial(),
};

// 리듀서
export default function posts(state = initialState, action) {
  switch (action.type) {
    case GET_POSTS:
    case GET_POSTS_SUCCESS:
    case GET_POSTS_ERROR:
      return handleAsyncActions(GET_POSTS, 'posts')(state, action);
    case GET_POST:
    case GET_POST_SUCCESS:
    case GET_POST_ERROR:
      return handleAsyncActions(GET_POST, 'post')(state, action);
    default:
      return state;
  }
}
```

참고로 다음 코드는

```js
case GET_POSTS:
case GET_POSTS_SUCCESS:
case GET_POSTS_ERROR:
	return handleAsyncActions(GET_POSTS, 'posts')(state, action);
```

아래처럼 표현할 수도 있다.

```js
case GET_POSTS:
case GET_POSTS_SUCCESS:
case GET_POSTS_ERROR:
	const postsReducer = handleAsyncActions(GET_POSTS, 'posts');
	return postsReducer(state, action);
```

리팩토링이 끝났으면 이제 이 모듈을 루트 리듀서에 등록해준다.

### modules/index.js

```js
import { combineReducers } from 'redux';
import counter from './counter';
import posts from './posts';

const rootReducer = combineReducers({
  counter,
  posts,
});

export default rootReducer;
```

## 4. 포스트 목록 구현하기

포스트 목록을 보여줄 프리젠테이셔널 컴포넌트는 components 디렉터리 안에 PostList.js 파일을 생성 후, 아래와 같이 작성한다.

### components/PostList.js

```js
import React from 'react';

const PostList = ({ posts }) => {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
};

export default PostList;
```

컨테이너 컴포넌트 PostListContainer는 containers 디렉터리 안에 생성한다.

### containers/PostListContainer.js

```js
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import PostList from '../components/PostList';
import { getPosts } from '../modules/posts';

const PostListContainer = () => {
  const { loading, data, error } = useSelector((state) => state.posts.posts);
  const dispatch = useDispatch();

  // 컴포넌트 마운트 후 포스트 목록 요청
  useEffect(() => {
    dispatch(getPosts());
  }, [dispatch]);

  if (loading) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!data) return <div>데이터가 없습니다.</div>;
  return <PostList posts={data} />;
};

export default PostListContainer;
```

그리고 이 컨테이너 컴포넌트를 App 컴포넌트에서 렌더링하면 된다.

### App.js

```js
import React from 'react';
import PostListContainer from './containers/PostListContainer';

function App() {
  return <PostListContainer />;
}

export default App;
```

## 5. 리액트 라우터 적용하기

이번엔 프로젝트에 리액트 라우터를 적용해서 특정 포스트를 읽는 기능도 구현해보겠다.

먼저 리액트 라우터를 설치한다.

```js
$ yarn add react-router-dom
```

그리고 index.js 를 다음과 같이 작성한다.

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
import { BrowserRouter } from 'react-router-dom';

const store = createStore(
  rootReducer,
  // logger를 사용하는 경우, logger가 가장 마지막에 와야 한다.
  composeWithDevTools(applyMiddleware(ReduxThunk, logger))
);

ReactDOM.render(
  <BrowserRouter>
    <Provider store={store}>
      <App />
    </Provider>
  </BrowserRouter>,
  document.getElementById('root')
);

serviceWorker.unregister();
```

## 6. 포스트 조회하기

그럼 이제 포스트 하나를 조회하기 위해, 프리젠테이셔널 컴포넌트 Post를 생성한다.

### components/Post.js

```js
import React from 'react';

const Post = ({ post }) => {
  const { title, body } = post;
  return (
    <div>
      <h1>{title}</h1>
      <p>{body}</p>
    </div>
  );
};

export default Post;
```

컨테이너 컴포넌트도 생성한다.  
PostContainer 컴포넌트는 `postId` 값을 `props`로 받아오는데, 이 값은 라우트의 URL 파라미터에서 읽어올 것이다.

### containers/PostContainer.js

```js
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import Post from '../components/Post';
import { getPost } from '../modules/posts';

const PostContainer = ({ postId }) => {
  const { loading, data, error } = useSelector((state) => state.posts.post);
  const dispatch = useDispatch();

  useEffect(() => {
    useDispatch(getPost(postId)); // thunk 함수의 파라미터로 postId값을 전달한다.
  }, [dispatch, postId]);

  if (loading) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!data) return null;
  return <Post post={data} />;
};

export default PostContainer;
```

## 7. 라우트 설정하기

이제 페이지 관련 컴포넌트를 생성 후, 라우트를 설정해보자.

먼저 pages 디렉터리를 만들고, PostListPage.js 와 PostPage.js 를 다음과 같이 작성한다.

### pages/PostListPage.js

```js
import React from 'react';
import PostListContainer from '../containers/PostListContainer';

const PostListPage = () => {
  return <PostListContainer />;
};

export default PostListPage;
```

### pages/PostPage.js

```js
import React from 'react';
import PostContainer from '../containers/PostContainer';

const PostPage = ({ match }) => {
  const { id } = match.param; // URL 파라미터 조회

  // URL 파라미터 값은 문자열이므로 숫자로 변환해줘야 한다.
  return <PostContainer postId={parseInt(id, 10)} />;
};

export default PostPage;
```

그리고 App 컴포넌트에서 라우트 설정을 한다.

### App.js

```js
import React from 'react';
import { Route } from 'react-router-dom';
import PostListPage from './pages/PostListPage';
import PostPage from './pages/PostPage';

function App() {
  return (
    <>
      <Route path="/" component={PostListPage} exact={true} />
      <Route path="/:id" component={PostPage} />
    </>
  );
}

export default App;
```

그 다음엔 포스트 목록에서 특정 포스트를 클릭했을 때 조회 페이지로 이동할 수 있도록, 다음과 같이 PostList 컴포넌트에서 `Link` 컴포넌트를 사용한다.

### components/PostList.js

```js
import React from 'react';
import { Link } from 'react-router-dom';

const PostList = ({ posts }) => {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <Link to={`/${post.id}`}>{post.title}</Link>
        </li>
      ))}
    </ul>
  );
};

export default PostList;
```

---

### 🐛 문제점 발견

잘 작동하긴 하나 두 가지 문제점이 있다.

1. 특정 포스트를 조회한 후, 뒤로 돌아갔을 때 포스트 목록을 다시 불러오게 되면서 `로딩중...`이 나타나게 된다.
2. 특정 포스트를 조회한 후, 다른 포스트를 조회하면 이전에 열었던 포스트가 잠깐 보여졌다가 `로딩중...`이 나타나게 된다.

이 문제를 해결하는 방법은 다음 섹션에서 다뤄보도록 하겠다!
