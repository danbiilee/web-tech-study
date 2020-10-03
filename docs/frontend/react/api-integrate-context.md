# Context와 함께 사용하기
이번에는 API 연동과 리액트의 `Context`를 함께 사용하는 방법을 알아보겠다. 

컴포넌트에서 필요한 외부 데이터들은 컴포넌트 내부에서 `useAsync` 같은 Hook을 사용해 작업하면 충분하지만, 다양한 컴포넌트에서 필요한 특정 데이터들(로그인한 사용자의 정보, 설정 등)은 `Context`를 사용하면 개발이 편해진다. 


## 1. Context 준비하기
UsersContext.js 파일을 생성 후 다음과 같이 코드를 작성한다. 

```js
// src/UsersContext.js

import React, { createContext, useReducer, useContext } from 'react';

// 1. UsersStateContext 에서 사용할 기본 상태
const initialState = {
  users: {
    loading: false,
    data: null,
    error: null,
  },
  user: {
    loading: false,
    data: null,
    error: null,
  },
};

// 로딩중일 때의 상태 객체 
const loadingState = {
  loading: true,
  data: null,
  error: null,
};

// 성공했을 때의 상태를 만들어주는 함수 
const success = data => ({
  loading: false,
  data,
  error: null,
});

// 실패했을 때의 상태를 만들어주는 함수 
const error = error => ({
  loading: false,
  data: null,
  error,
});

// 2. 위에서 만든 객체/함수 들을 사용해 리듀서 작성
function usersReducer(state, action) {
  switch (action.type) {
    // users를 요청하는 액션일 땐 state.users의 상태를 변경
    case 'GET_USERS':
      return {
        ...state,
        users: loadingState,
      };
    case 'GET_USERS_SUCCESS':
      return {
        ...state,
        users: success(action.data),
      };
    case 'GET_USERS_ERROR':
      return {
        ...state,
        users: error(action.error),
      };
    // user를 요청하는 액션일 땐 state.user의 상태를 변경
    case 'GET_USER':
      return {
        ...state,
        user: loadingState,
      };
    case 'GET_USER_SUCCESS':
      return {
        ...state,
        user: success(action.data),
      };
    case 'GET_USER_ERROR':
      return {
        ...state,
        user: error(action.error),
      };
    default:
      throw new Error(`Unhandled action type: ${action.type}`);
  }
}

// 3. State용/Dispatch용 Context 생성 
const UsersStateContext = createContext(null);
const UsersDispatchContext = createContext(null);

// 4. 위에서 선언한 Context들의 Provider로 감싸주는 컴포넌트 생성
export function UsersProvider({ children }) {
  const [state, dispatch] = useReducer(usersReducer, initialState);
  return (
    <UsersStateContext.Provider value={state}>
      <UsersDispatchContext.Provider value={dispatch}>
        {children}
      </UsersDispatchContext.Provider>
    </UsersStateContext.Provider>
  );
}

// 5. State를 쉽게 조회할 수 있게 해주는 커스텀 훅
export function useUsersState() {
  const state = useContext(UsersStateContext);
  if (!state) {
    throw new Error('Users Provider can not find!');
  }
  return state;
}

// Dispatch를 쉽게 사용할 수 있게 해주는 커스텀 훅
export function useUsersDispatch() {
  const dispatch = useContext(UsersDispatchContext);
  if (!dispatch) {
    throw new Error('Users Provider can not find!');
  }
  return dispatch;
}
```


## 2. API 처리 함수 만들기
이제 기존과 같이 API를 호출하면, 요청이 시작했을 때 액션을 디스패치하고, 요청이 성공하거나 실패했을 때 또 액션을 디스패치하는 작업을 처리하는 함수를 만들어보겠다.

UsersContext.js의 하단에 `getUsers` 와 `getUser` 함수를 작성한다. 

이 함수들은 파라미터로 `dispatch`와 API 요청에 필요한 파라미터(`id`)를 받아오게 된다. 

```js
// src/UsersContext.js

import React, { createContext, useReducer, useContext } from 'react';
import axios from 'axios';

// ...
export async function getUsers(dispatch) {
  try {
    dispatch({ type: 'GET_USERS' });
    const response = await axios.get(
      'https://jsonplaceholder.typicode.com/users',
    );
    dispatch({ type: 'GET_USERS_SUCCESS', data: response.data });
  } catch (e) {
    dispatch({ type: 'GET_USERS_ERROR', error: e });
  }
}

export async function getUser(dispatch, id) {
  try {
    dispatch({ type: 'GET_USER' });
    const response = await axios.get(
      `https://jsonplaceholder.typicode.com/users/${id}`,
    );
    dispatch({ type: 'GET_USER_SUCCESS', data: response.data });
  } catch (e) {
    dispatch({ type: 'GET_USER_ERROR', error: e });
  }
}
```


## 3. Context 사용하기
우리가 만든 `Context`를 사용하기 위해선 먼저 App 컴포넌트에서 Users 컴포넌트를 `UsersProvider`로 감싸준다. 

```js
// src/App.js
import React from 'react';
import Users from './Users';
import { UsersProvider } from './UsersContext';

function App() {
  return (
    <UsersProvider>
      <Users />
    </UsersProvider>
  );
}

export default App;
```

그리고 Users 컴포넌트의 코드를 `Context`를 사용하는 형태로 수정한다. 

```js
// src/Users.js
import React, { useState } from 'react';
import User from './User';
import { useUsersState, useUsersDispatch, getUsers } from './UsersContext';

const Users = () => {
  // UsersContext에서 만든 커스텀 훅으로 각각 state와 dispatch를 불러온다. 
  const state = useUsersState();
  const dispatch = useUsersDispatch();
  const [userId, setUserId] = useState(null);

  // 조회하려는 state의 key를 명시해야 한다. (state.users 혹은 state.user)
  const { loading, data: users, error } = state.users; 

  const fetchData = () => {
    getUsers(dispatch); // API 요청 함수의 파라미터로 dispatch를 전달
  };

  if (loading) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!users)
    return (
      <div>
        <button onClick={fetchData}>데이터 불러오기</button>
      </div>
    );
  return (
    <div>
      <ul>
        {users.map(user => (
          <li
            key={user.id}
            onClick={() => setUserId(user.id)}
            style={{ cursor: 'pointer' }}
          >
            {user.username} ({user.name})
          </li>
        ))}
      </ul>
      <button onClick={fetchData}>다시 불러오기</button>
      {userId && <User id={userId} />}
    </div>
  );
};

export default Users;
```

그 다음엔 User 컴포넌트도 수정한다. 

```js
// src/User.js

import React, { useEffect } from 'react';
import { useUsersState, useUsersDispatch, getUser } from './UsersContext';

const User = ({ id }) => {
  const state = useUsersState();
  const dispatch = useUsersDispatch();
  const { loading, data: user, error } = state.user;

  // useEffect의 deps로 전달한 id값이 바뀔 때마다 getUser함수가 호출된다. 
  useEffect(() => {
    // API 요청 함수의 두 번째 파라미터로 props로 받아온 id값을 전달한다. 
    getUser(dispatch, id); 
  }, [dispatch, id]);

  if (loading) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!user) return null;
  return (
    <div>
      <h2>{user.username}</h2>
      <p>
        <b>Email: </b>
        {user.email}
      </p>
    </div>
  );
};

export default User;
```

