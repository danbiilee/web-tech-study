# redux-thunk 설치 및 적용하기

## 1. redux-thunk란

`redux-thunk`는 리덕스에서 비동기 작업을 처리할 때 가장 많이 사용하는 미들웨어이다. 이 미들웨어를 사용하면 액션 객체가 아닌 **함수를 디스패치** 할 수 있다.

`redux-thunk`의 코드는 아래와 같이 겨우 14줄밖에 되지 않는다. 그 중에서도 `action`의 타입이 함수이면 함수를 디스패치 하는 게 핵심이다.

```js
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => (next) => (action) => {
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }

    return next(action);
  };
}

const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;

export default thunk;
```

함수를 디스패치 할 때에는 해당 함수에서 `dispatch`와 `getState`를 파라미터로 전달받아야 한다. 이러한 함수를 **만들어주는 함수**를 `thunk`라고 부른다.

`thunk`의 사용 예시를 확인해보자.

```js
const getComments = () => (dispatch, getState) => {
  // 이 안에서 액션을 디스패치 할 수도 있고,
  // getState를 사용해 현재 상태를 조회할 수도 있다.
  const id = getState().post.activeId;

  dispatch({ type: 'GET_COMMENTS' }); // 요청 시작

  // getComments(): 댓글을 조회하는 프로미스를 반환
  api
    .getComments(id)
    .then((comments) =>
      dispatch({ type: 'GET_COMMENTS_SUCCESS', id, comments })
    ) // 성공
    .catch((e) => dispatch({ type: 'GET_COMMENTS_ERROR', error: e })); // 실패
};
```

혹은 `thunk` 함수에서 async/await를 사용해도 된다.

```js
const getComments = () => async (dispatch, getState) => {
  const id = getState().post.activeId;
  dispatch({ type: 'GET_COMMENTS' }); // 요청 시작

  try {
    const comments = await api.getComments(id);
    dispatch({ type: 'GET_COMMENTS_SUCCESS', id, comments });
  } catch (e) {
    dispatch({ type: 'GET_COMMENTS_ERROR', error: e });
  }
};
```

## 2. redux-thunk 설치 및 적용하기

그럼 이제 프로젝트에 `redux-thunk`를 설치해보자.

```js
$ yarn add redux-thunk
```

그 다음엔 index.js에서 `applyMiddlewares`를 통해 미들웨어를 적용해보자.

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

const store = createStore(
  rootReducer,
  // logger를 사용하는 경우, logger가 가장 마지막에 와야 한다.
  composeWithDevTools(applyMiddleware(ReduxThunk, logger))
);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);

serviceWorker.unregister();
```

## 3. 카운터 딜레이하기

이번엔 thunk 함수를 만들고, `setTimeout`을 사용해 액션이 디스패치 되는 것을 1초씩 딜레이시켜보자. 먼저 counter 모듈에서 `increaseAsync`와 `decreaseAsync` 함수를 추가해준다.

### modules/counter.js

```js
// 액션 타입
const INCREASE = 'counter/INCREASE';
const DECREASE = 'counter/DECREASE';

// 액션 생성함수
export const increase = () => ({ type: INCREASE });
export const decrease = () => ({ type: DECREASE });

// 디스패치 1초 딜레이 thunk 함수
// getState를 쓰지 않는다면 굳이 파라미터로 받아오지 않아도 된다.
export const increaseAsync = () => (dispatch) => {
  setTimeout(() => dispatch(increase()), 1000);
};
export const decreaseAsync = () => (dispatch) => {
  setTimeout(() => dispatch(decrease()), 1000);
};

// 초기값
const initialState = 0;

// 리듀서
export default function counter(state = initialState, action) {
  switch (action.type) {
    case INCREASE:
      return state + 1;
    case DECREASE:
      return state - 1;
    default:
      return state;
  }
}
```

이제 컨테이너 컴포넌트를 다음과 같이 수정한다.

### containers/CounterContainer.js

```js
import React from 'react';
import Counter from '../components/Counter';
import { useSelector, useDispatch } from 'react-redux';
import { increaseAsync, decreaseAsync } from '../modules/counter';

const CounterContainer = () => {
  const number = useSelector((state) => state.counter);
  const dispatch = useDispatch();
  const onIncrease = () => dispatch(increaseAsync());
  const onDecrease = () => dispatch(decreaseAsync());

  return (
    <Counter number={number} onIncrease={onIncrease} onDecrease={onDecrease} />
  );
};

export default CounterContainer;
```

컨테이너 컴포넌트에서 **thunk 함수를 디스패치** 함으로써, 실제로 액션 객체(`increase()` 혹은 `decrease()`)가 디스패치 되는 게 1초씩 딜레이될 것이다.
