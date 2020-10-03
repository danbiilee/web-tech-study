# 중복되는 코드 리팩토링하기

이번엔 이전까지 작성한 Context + 비동기 API 연동 패턴에서 반복되는 로직들을 함수화하여 재활용하는 방법을 알아보겠다. 

## 1. API 요청 함수
먼저 `getUsers`, `getUser` 함수를 리팩토링할 수 있다. 

api.js 파일을 생성 후, 다음과 같이 API 요청 코드만 분리한다. 

```js
// src/api.js

import axios from 'axios';

export async function getUsers() {
  const response = await axios.get(
    'https://jsonplaceholder.typicode.com/users',
  );
  return response.data;
}

export async function getUser(id) {
  const response = await axios.get(
    `https://jsonplaceholder.typicode.com/users/${id}`,
  );
  return response.data;
}
```

그 다음엔 asyncActionUtils.js 파일을 생성 후, 반복되는 액션 디스패치 관련 코드들을 함수화하도록 한다. 

```js
// src/asyncActionUtils.js

// 첫 번째 파라미터: 액션 타입(GET_USERS 등)
// 두 번째 파라미터: Promise를 반환하는 함수(API 요청 함수)
export function createAsyncDispatcher(type, promiseFn) {
  // 성공, 실패에 대한 액션 타입 문자열
  const SUCCESS = `${type}_SUCCESS`;
  const ERROR = `${type}_ERROR`;

  // 액션을 처리하는 새로운 함수 생성
  // ...rest 를 사용해 나머지 파라미터를 rest 배열에 담는다. 
  async function actionHandler(dispatch, ...rest) {
    try {
      dispatch({ type }); // 요청 시작
      const data = await promiseFn(...rest); // rest 배열을 spread로 전달  
      dispatch({ type: SUCCESS, data }); // 요청 성공
    } catch (e) {
      dispatch({ type: ERROR, error: e }); // 요청 실패
    }
  }

  return actionHandler;
}
```

이렇게 `createAsyncDispatcher` 함수를 만들면, UsersContext의 코드를 다음과 같이 리팩토링 할 수 있게 된다. 

```js
// src/UsersContext.js
import React, { createContext, useReducer, useContext } from 'react';
import * as api from './api';
import { createAsyncDispatcher } from './asyncActionUtils';

// ...
export const getUsers = createAsyncDispatcher('GET_USERS', api.getUsers);
export const getUser = createAsyncDispatcher('GET_USER', api.getUser);
```


## 2. 리듀서
리듀서도 리팩토링이 가능하다. 

UsersContext의 `loadingState`, `success`, `error`를 asyncActionUtils.js 로 이동시킨다. 

그리고 다음과 같이 `initialAsyncState` 객체와 `createAsyncHandler` 함수를 만들어 내보낸다. 

```js
// src/asyncActionUtils.js

// ... 

// 기본 상태
export const initialAsyncState = {
  loading: false,
  data: null,
  error: null,
};

const loadingState = {
  loading: true,
  data: null,
  error: null,
};

const success = data => ({
  loading: false,
  data,
  error: null,
});

const error = error => ({
  loading: false,
  data: null,
  error,
});

// 로딩, 성공, 실패 액션을 처리하는 리듀서
// type: action.type
// key: 리듀서에서 사용할 state의 key 값(state.users 혹은 state.user)
export function createAsyncHandler(type, key) {
  const SUCCESS = `${type}_SUCCESS`;
  const ERROR = `${type}_ERROR`;

  function handler(state, action) {
    switch (action.type) {
      case type:
        return {
          ...state,
          [key]: loadingState,
        };
      case SUCCESS:
        return {
          ...state,
          [key]: success(action.data),
        };
      case ERROR:
        return {
          ...state,
          [key]: error(action.error),
        };
      default:
        throw new Error(`Unhandled action type: ${action.type}`);
    }
  }

  return handler;
}
```

이제 방금 만든 `initialAsyncState` 와 `createAsyncHandler`를 사용해 UsersContext 코드를 수정해보자.

```js
// src/UsersContext.js

import React, { createContext, useReducer, useContext } from 'react';
import * as api from './api';
import {
  createAsyncDispatcher,
  initialAsyncState,
  createAsyncHandler,
} from './asyncActionUtils';

// 기본 상태 리팩토링
const initialState = {
  users: initialAsyncState,
  user: initialAsyncState,
};

// 리듀서 리팩토링 
const usersHandler = createAsyncHandler('GET_USERS', 'users');
const userHandler = createAsyncHandler('GET_USER', 'user');

function reducer(state, action) {
  switch (action.type) {
    // return 또는 break를 하지 않으면, 여러 개의 case에 대해 동일한 코드를 실행한다.
    case 'GET_USERS':
    case 'GET_USERS_SUCCESS':
    case 'GET_USERS_ERROR':
      return usersHandler(state, action);
    case 'GET_USER':
    case 'GET_USER_SUCCESS':
    case 'GET_USER_ERROR':
      return userHandler(state, action);
    default:
      throw new Error(`Unhandled action type: ${action.type}`);
  }
}

// ...
```