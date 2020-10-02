# useReducer로 요청 상태 관리하기

이번엔 User 컴포넌트에서 `useState`로 관리했던 로딩, 데이터, 에러에 관한 상태를 
`useReducer`를 사용해 `LOADING`, `SUCCESS`, `ERROR` 액션에 따라 처리할 수 있도록 구현해보자. 

```js
import React, { useReducer, useEffect } from 'react';
import axios from 'axios';

function reducer(state, action) {
  switch (action.type) {
    case 'LOADING':
      return {
        loading: true,
        data: null,
        error: null,
      };
    case 'SUCCESS':
      return {
        loading: false,
        data: action.data,
        error: null,
      };
    case 'ERROR':
      return {
        loading: false,
        data: null,
        error: action.error,
      };
    default:
      throw new Error(`Unhandled action type: ${action.type}`);
  }
}

const Users = () => {
  const [state, dispatch] = useReducer(reducer, {
    loading: false,
    data: null,
    error: null,
  }); // 1
  const { loading, data: users, error } = state; // 2

  const fetchUsers = async () => {
    try {
      dispatch({ type: 'LOADING' }); // 3
      const response = await axios.get(
        'https://jsonplaceholder.typicode.com/users',
      );
      dispatch({ type: 'SUCCESS', data: response.data });
    } catch (e) {
      dispatch({ type: 'ERROR', error: e });
    }
  };

  useEffect(() => {
    fetchUsers();
  }, []);

  if (loading) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!users) return <div>데이터가 없습니다.</div>;
  return (
    <div>
      <ul>
        {users.map(user => (
          <li key={user.id}>
            {user.username} ({user.name})
          </li>
        ))}
      </ul>
      <button onClick={fetchUsers}>다시 불러오기</button>
    </div>
  );
};

export default Users;
```

**1️⃣ useReducer**
- 먼저 Users 컴포넌트 바깥에 `reducer` 함수를 작성한다.
- 그리고 `useReducer` 함수의 첫 번째 파라미터로 `reducer` 함수를, 두 번째 파라미터로 초기값을 전달한 후 `state`와 `dispatch`를 반환받는다 
  
**2️⃣ state 구조 분해 할당**
- `reducer` 함수에서 `action.type`에 따라 리턴된 **객체**가 `state`에 담기게 될 것이므로 구조 분해 할당을 통해 값을 추출한다. 
- 기존 코드 `users` 를 그대로 사용하기 위해 `data: users` 를 통해 `state.data`를 `users`로 받아준다. 

**3️⃣ 액션 발생 시키기**
- `dispatch` 함수를 통해 액션을 발생시킨다. 
- 파라미터로 `action` 객체를 전달한다. 
  - `action` 객체에는 type, data, error 가 담길 수 있다. 



## useReducer 로 구현했을 때의 장점

- `reducer` 로 로직을 분리했으므로 재사용이 가능해진다.  
- `useState`의 `setState` 함수를 여러 번 사용하지 않아도 된다. 
  
```js
// useState를 사용했을 때의 fetchUsers 함수이다. 
const fetchUsers = async () => { 
  try {
    // 아래 세 줄의 코드가 useReducer를 사용하면  
    // dispatch({ type: 'LOADING' }); 한 줄로 단축이 가능해진다. 
    setUsers(null);
    setError(null);
    setLoading(true);
    const response = await axios.get(
      'https://jsonplaceholder.typicode.com/users',
    );
    setUsers(response.data);
  } catch (e) {
    setError(e);
  }
  setLoading(false);  
};
```