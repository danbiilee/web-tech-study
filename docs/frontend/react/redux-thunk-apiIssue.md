# API 재로딩 이슈 해결하기

이번엔 사용자에게 나쁜 경험을 제공할 수 있는 API 재로딩 문제를 해결해보도록 하겠다.

## 1. 포스트 목록 재로딩 문제 해결하기

포스트 목록이 재로딩되는 문제를 해결하는 방법엔 두 가지가 있다.

첫 번째는 데이터가 이미 존재한다면 요청을 하지 않는 방법이고,  
두 번째는 로딩을 새로하되, `로딩중...`을 띄우지 않는 방법이다. 두 번째 방법은 뒤로가기를 통해 포스트 목록을 다시 조회할 때 최신 데이터를 보여줄 수 있다는 장점이 있다.

**🔍 1️⃣ 요청하지 않기**

먼저 첫 번째 방법을 적용해보기 위해 PostListContainer를 다음과 같이 수정한다.

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
    if (data) return; // 데이터가 있다면 요청하지 않는다.
    dispatch(getPosts());
  }, [dispatch]);

  if (loading) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!data) return null;
  return <PostList posts={data} />;
};

export default PostListContainer;
```

**🔍 2️⃣ 로딩중...을 띄우지 않기**

이번엔 두 번째 방법을 적용해보겠다. 우선 asyncUtils.js의 `handleAsyncActions` 함수를 다음과 같이 수정한다.

### lib/asyncUtils.js

```js
export const handleAsyncActions = (type, key, keepData = false) => {
  const [SUCCESS, ERROR] = [`${type}_SUCCESS`, `${type}_ERROR`];
  return (state, action) => {
    switch (action.type) {
      case type:
        return {
          ...state,
          [key]: reducerUtils.loading(keepData ? state[key].data : null),
        };
      case SUCCESS:
        return {
          ...state,
          [key]: reducerUtils.success(action.payload),
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

`keepData` 라는 파라미터를 추가해 이 값이 `true` 라면 로딩을 할 때에도 데이터를 유지할 수 있도록 한다.

`reducerUtils` 객체의 `loading` 메서드를 다시 한 번 확인하자.

```js
// 로딩중 상태: prevData의 기본값을 null로 설정했지만,
// 다른 값을 지정하면 null로 초기화하지 않고 다른 값을 유지할 수 있다.
loading: (prevData = null) => ({
	loading: true,
	data: prevData,
	error: null,
}),
```

이제 posts 모듈의 리듀서도 다음과 같이 수정한다.

### modules/posts.js

```js
export default function posts(state = initialState, action) {
  switch (action.type) {
    case GET_POSTS:
    case GET_POSTS_SUCCESS:
    case GET_POSTS_ERROR:
      return handleAsyncActions(GET_POSTS, 'posts', true)(state, action);
    case GET_POST:
    case GET_POST_SUCCESS:
    case GET_POST_ERROR:
      return handleAsyncActions(GET_POST, 'post')(state, action);
    default:
      return state;
  }
}
```

포스트 목록에 관한 요청에만 `keepData` 파라미터에 `true`를 전달했다.

그리고 PostListContainer를 다음과 같이 수정한다.

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

  // 로딩중이면서 데이터가 없을 때만 로딩중...을 표시한다.
  if (loading && !data) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!data) return null;
  return <PostList posts={data} />;
};

export default PostListContainer;
```

## 2. 포스트 조회시 재로딩 문제 해결하기

특정 포스트를 조회할 땐 파라미터 값에 따라 다른 결과가 있기 때문에, 재로딩 문제를 해결하려면 방금 했던 방식으로는 처리할 수 없다.

이 문제를 해결하는 방식 또한 두 가지가 있다.

첫 번째는 컴포넌트가 언마운트 될 때 포스트 내용을 비우도록 하는 방법이고,  
두 번째는 포스트 내용을 비우지 않으면서 데이터가 있다면 요청을 하지 않거나, 요청은 하되 `로딩중...`을 표시하지 않는 방법이다.

**🔍 1️⃣ 포스트 내용 비우기**
첫 번째 방법을 적용하려면 posts 모듈에 `CLEAR_POST` 라는 액션을 만들어야 한다.

### modules/posts.js

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
// 포스트 비우기
const CLEAR_POST = 'posts/CLEAR_POST';

// thunk 함수
export const getPosts = createPromiseThunk(GET_POSTS, postApi.getPosts);
export const getPost = createPromiseThunk(GET_POST, postApi.getPostById);

export const getClear = () => ({ type: CLEAR_POST }); // 포스트 비우기 액션 생성함수

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
      return handleAsyncActions(GET_POSTS, 'posts', true)(state, action);
    case GET_POST:
    case GET_POST_SUCCESS:
    case GET_POST_ERROR:
      return handleAsyncActions(GET_POST, 'post')(state, action);
    case CLEAR_POST:
      return {
        ...state,
        post: reducerUtils.initial(), // 초기 상태로 되돌린다.
      };
    default:
      return state;
  }
}
```

그리고 PostContainer 컴포넌트에서 `useEffect` 의 `cleanup` 함수를 사용해 포스트 비우기 액션을 디스패치 해주면 된다.

```js
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import Post from '../components/Post';
import { getClear, getPost } from '../modules/posts';

const PostContainer = ({ postId }) => {
  const { loading, data, error } = useSelector((state) => state.posts.post);
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(getPost(postId));
    return () => {
      // cleanup 함수: 컴포넌트가 언마운트 될 때 실행된다.
      // deps에 특정 값이 있다면 deps의 값이 바뀌기 직전에도 실행된다.
      dispatch(getClear());
    };
  }, [dispatch, postId]);

  if (loading) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!data) return null;
  return <Post post={data} />;
};

export default PostContainer;
```

이렇게 해주면 포스트 페이지에서 떠날 때마다 포스트를 비우게 되므로, 이전 포스트가 보여지는 문제가 해결된다.

하지만 한 가지 단점은 이미 읽었던 포스트를 다시 조회할 때도 새로 요청을 한다는 점이다. 이 문제를 개선하려면, posts 모듈에서 관리하는 상태의 구조를 바꿔야 한다.

현재 이 구조를

```js
{
	posts: {
		data,
		loading,
		error
	},
	post: {
		data,
		loading,
		error
	}
}
```

다음과 같이 변경해야 한다.

```js
{
	posts: {
		data,
		loading,
		error
	},
	post: {
		'1': {
			data,
			loading,
			error
		},
		'2': {
			data,
			loading,
			error
		},
		[id]: {
			data,
			loading,
			error
		},
	}
}
```

이렇게 변경하기 위해 asyncUtils.js 에 만든 기존 함수들을 수정하는 대신, 다음 함수들을 새롭게 작성한다.

1. createPromiseThunkById
2. handleAsyncActionsById

그리고 이제부터 액션이 어떤 post의 `id`를 가르키는지 알아야하므로, `action.meta` 값에 `id`를 넣어주도록 한다.

### lib/asyncUtils.js

```js
//...

// 파라미터에서 id를 어떻게 선택할 지 정의하는 함수이다.
// 만약 파라미터가 객체 형태라면 param => param.id 로 설정하면 된다.
const defaultIdSelector = (param) => param;
// 특정 id를 처리하는 thunk 생성함수
export const createPromiseThunkById = (
  type,
  promiseCreator,
  idSelector = defaultIdSelector
) => {
  const [SUCCESS, ERROR] = [`${type}_SUCCESS`, `${type}_ERROR`];
  return (param) => async (dispatch) => {
    const id = idSelector(param); // 파라미터에서 id 값을 가져온다.
    try {
      dispatch({ type, meta: id }); // action.meta 에 id 값을 담는다.
      const payload = await promiseCreator(param);
      dispatch({ type: SUCCESS, payload, meta: id });
    } catch (e) {
      dispatch({ type: ERROR, error: e, meta: id });
    }
  };
};

// 액션을 id별로 처리하는 유틸함수
export const handleAsyncActionsById = (type, key, keepData = false) => {
  const [SUCCESS, ERROR] = [`${type}_SUCCESS`, `${type}_ERROR`];

  return (state, action) => {
    const id = action.meta; // action.meta에 담았던 id 값을 가져온다.
    switch (action.type) {
      case type:
        return {
          ...state,
          [key]: {
            ...state[key],
            [id]: reducerUtils.loading(
              // 아직 포스트 조회 전이라 state[key][id]가 null일 수도 있으므로 유효성 체크 후 data 가져온다.
              keepData ? state[key][id] && state[key][id].data : null
            ),
          },
        };
      case SUCCESS:
        return {
          ...state,
          [key]: {
            ...state[key],
            [id]: reducerUtils.success(action.payload),
          },
        };
      case ERROR:
        return {
          ...state,
          [key]: {
            ...state[key],
            [id]: reducerUtils.error(action.error),
          },
        };
    }
  };
};
```

**🔍 2️⃣ 요청하지 않기**

**🔍 3️⃣ 로딩중...을 띄우지 않기**
