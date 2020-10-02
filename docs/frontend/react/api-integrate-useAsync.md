# 커스텀 Hook으로 요청 상태 관리 로직 재사용하기

## 1. 커스텀 Hook 만들기
데이터를 요청해야 할 때마다 리듀서를 작성하는 것은 번거로운 일이다. 대신에, 커스텀 Hook을 만들어 요청 상태 관리 로직을 재사용하는 방법을 알아보자.

먼저 useAsync.js 파일을 생성 후 다음 코드를 작성한다. 

```js
// src/useAsync.js

import { useReducer, useEffect } from 'react';

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

function useAsync(callback, deps = []) { // 1
  const [state, dispatch] = useReducer(reducer, {
    loading: false,
    data: null,
    error: null,
  });

  const fetchData = async () => {
    dispatch({ type: 'LOADING' });
    try {
      const data = await callback(); // 2
      dispatch({ type: 'SUCCESS', data });
    } catch (e) {
      dispatch({ type: 'ERROR', error: e });
    }
  };

  useEffect(() => {
    fetchData();
    // eslint-disable-next-line
  }, deps); // 3

  return [state, fetchData]; // 4
}

export default useAsync;
```

**1️⃣ useAsync 함수**
- 첫 번째 파라미터: API 요청을 시작하는 함수
- 두 번째 파라미터: 해당 함수 안에서 사용하는 `useEffect`의 `deps`
  - 현재 Users 컴포넌트에서는 불필요한 부분이다.
  - API요청 시 파라미터가 필요하고, 그 파라미터가 바뀔 때마다 새로운 데이터를 불러오고 싶은 경우에 활용할 수 있다. 

**2️⃣ API 요청**
- `useAsync` 함수의 첫 번째 인자인 콜백함수로써, API를 요청한다. 
- 해당 콜백함수는 `async` 함수이며, Users 컴포넌트에서 작성한다. 

**3️⃣ deps 전달**
- `useAsync` 함수의 두 번째 인자로 전달받은 `deps`를 `useEffect`에서 사용하려고 하면 eslint 경고가 발생한다. 
- `// eslint-disable-next-line` 구문을 위에 추가하면 그 다음 줄에서만 eslint 설정이 비활성화 된다. 

**4️⃣ Hook 반환 값**
- `useAsync` Hook은 요청 관련 상태인 `state`와 `fetchData` 함수를 반환한다. 이렇게 반환되는 `fetchData`를 통해 데이터 리로딩을 쉽게 할 수 있게 된다. 


이제 Users 컴포넌트에서 이 Hook을 사용해보자. 

```js
import React from 'react';
import axios from 'axios';
import useAsync from './useAsync';

async function getUsers() { // 1 
  const response = await axios.get(
    'https://jsonplaceholder.typicode.com/users',
  );
  return response.data;
}

const Users = () => {
  const [state, fetchData] = useAsync(getUsers, []); // 2 
  const { loading, data: users, error } = state;

  if (loading) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!users) return null;
  return (
    <div>
      <ul>
        {users.map(user => (
          <li key={user.id}>
            {user.username} ({user.name})
          </li>
        ))}
      </ul>
      <button onClick={fetchData}>다시 불러오기</button> 
    </div>
  );
};

export default Users;
```

**1️⃣ API 요청 함수**
- `useAsync` 에서 콜백함수의 결과 프로미스를 바로 data에 담았기 때문에 요청의 결과값 `response`에서 `data`를 추출하여 반환하도록 작성한다. 

**2️⃣ 구조 분해 할당**
- `useAsync` 는 `state` 와 `fetchData`함수를 배열로 반환한다. 



## 2. 데이터 나중에 불러오기 
현재 Users 컴포넌트는 처음 렌더링되는 시점부터 API를 요청하고 있다. 

만약 필요할 때만 특정 버튼을 눌러 API를 요청하고 싶다면, 다음과 같이 `useAsync` 에 세 번째 파라미터 `skip`을 추가한다. 
그리고 `skip` 파라미터의 기본값을 `false`로 지정 후, 그 값이 `true`라면 `useEffect`에서 API 요청을 하지 않도록 설정한다. 

```js
// src/useAsync.js
function useAsync(callback, deps = [], skip = false) {
  const [state, dispatch] = useReducer(reducer, {
    loading: false,
    data: null,
    error: null,
  });

  const fetchData = async () => {
    dispatch({ type: 'LOADING' });
    try {
      const data = await callback();
      dispatch({ type: 'SUCCESS', data });
    } catch (e) {
      dispatch({ type: 'ERROR', error: e });
    }
  };

  useEffect(() => {
    // skip 이 true일 때는 API 요청을 하지 않는다.
    if (skip) return; 
    fetchData();
    // eslint-disable-next-line
  }, deps);

  return [state, fetchData];
}
```

이에 따라 Users 컴포넌트도 수정한다. 

```js
// src/Users.js
const Users = () => {
  // 처음 로딩땐 skip 파라미터 값을 true로 전달한다. 
  const [state, fetchData] = useAsync(getUsers, [], true); 
  const { loading, data: users, error } = state;

  if (loading) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  // data가 없다면 불러오기 버튼을 렌더링한다. 
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
          <li key={user.id}>
            {user.username} ({user.name})
          </li>
        ))}
      </ul>
      <button onClick={fetchData}>다시 불러오기</button>
    </div>
  );
};
```


## 3. 파라미터를 통한 API 요청 
API를 요청할 때 파라미터가 필요한 경우엔 어떻게 해야할까?

우리는 User 컴포넌트를 생성 후, `props`로 `id` 값을 전달받아 아래와 같이 주소 맨 뒤에 `id` 값을 더해 API를 요청할 것이다. 

https://jsonplaceholder.typicode.com/users/1


먼저 아래와 같이 User 컴포넌트를 작성한다. 

```js
// src/User.js
import React from 'react';
import axios from 'axios';
import useAsync from './useAsync';

async function getUser(id) { // 1
  const response = await axios.get(
    `https://jsonplaceholder.typicode.com/users/${id}`,
  );
  return response.data;
}

const User = ({ id }) => {
  const [state] = useAsync(() => getUser(id), [id]); // 1 
  const { loading, data: user, error } = state;

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

**1️⃣ API 요청 함수 생성**
- 인자로 `id` 값을 전달받고, 리터럴 템플릿으로 요청 URI 뒤에 해당 `id` 값을 더해주는 함수를 새롭게 생성한다. 

**2️⃣  useAsync 사용**
- 첫 번째 파라미터: API 요청 함수를 **호출**하여 `id` 값을 전달한다. 
- `id` 가 바뀔 때마다 API를 재요청할 수 있도록 `deps`에 `id` 값을 전달한다. 



그 다음엔 Users 컴포넌트 코드를 수정한다.

`useState`를 사용해 `useId` 값을 관리할 수 있도록 하고,     
리스트의 항목을 클릭하면 클릭한 사용자의 `id` 를 `userId` 값으로 설정하여 User 컴포넌트를 조건부 렌더링 한다. 

```js
const Users = () => {
  const [state, fetchData] = useAsync(getUsers, [], true);
  const { loading, data: users, error } = state;
  const [userId, setUserId] = useState(null);

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
```

