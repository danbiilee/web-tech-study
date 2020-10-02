# API 연동의 기본

우리가 웹 앱을 만들 때 데이터를 보존시키고, 다른 사람들도 조회할 수 있게 하려면 서버를 만들고, 서버의 API를 사용해서 데이터를 읽고 써야 한다. 

실제 프로젝트에서는 주로 `Redux` 라는 라이브러리와 `Redux 미들웨어`를 함께 사용하여 구현을 하지만, 이 도구들은 API 연동을 할 때 필수적인 요소는 아니다. 

따라서 이번엔 `Redux` 없이 컴포넌트에서 API연동을 하는 방법을 다뤄보도록 하겠다. 



## 1. axios

API 호출을 위해 프로젝트에 `axios` 라는 라이브러리를 설치한다. 

```js
$ yarn add axios
```

`axios`를 사용하면 하고 싶은 작업에 따라 다른 메서드로 API 요청을 할 수 있다. 

**✨ 메서드 종류**
- `GET`: 데이터 조회
- `POST`: 데이터 등록
- `PUT`: 데이터 수정
- `DELETE`: 데이터 제거

**✨ `axios` 사용법**
```js
import axios from 'axios';

axios.get('/users/1'); // 1

axios.post('/users', { 
  username: 'gildong', 
  name: 'hong-gildong'
}); // 2
```

**1️⃣ `get` 메서드**
- 메서드 이름은 소문자로 작성한다. 
- 파라미터에는 API의 URI를 전달한다.

**2️⃣ `post` 메서드**
- `axios.post()` 로 데이터를 등록할 땐 두 번째 파라미터에 등록하고자 하는 정보를 전달할 수 있다. 

---
실습할 땐 연습용 API인  [JSONPlaceholder](https://jsonplaceholder.typicode.com/)를 사용할 것이다.

그 중에서 사용할 API의 주소는 https://jsonplaceholder.typicode.com/users 이다. 

---

## 2. 데이터 로딩
API 요청에 대한 상태를 관리할 땐 다음과 같이 총 3가지 상태를 관리해줘야 한다. 

1. 요청의 결과
2. 로딩 상테
3. 에러

`useState` 를 사용해 위의 요청 상태를 관리하고, `useEffect`를 사용해 컴포넌트가 처음 렌더링되는 시점에 요청을 시작하여 데이터를 로딩하는 작업을 해보겠다. 

```js
// src/Users.js
import React, { useState, useEffect } from 'react';
import axios from 'axios';

const Users = () => {
  const [loading, setLoading] = useState(false);
  const [users, setUsers] = useState(null);
  const [error, setError] = useState(null);

  useEffect(() => { // 1 
    const fetchUsers = async () => { // 2 
      try {
        // 요청이 시작할 땐 error와 users를 초기화하고, loading을 true로 변경한다.
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
    </div>
  );
};

export default Users;
```

**1️⃣ useEffect**
- 컴포넌트가 처음 렌더링될 때 API를 요청하기 위해, `useEffect`의 두 번째 파라미터에 빈 `deps`를 전달한다. 

**2️⃣ async 함수 선언** 
- `useEffect`의 첫 번째 파라미터에는 `async` 함수를 전달할 수 없으므로, 함수 내부에서 `async`를 사용하는 새로운 함수를 선언한다. 



## 3. 에러 발생 확인하기
주소를 이상하게 바꿔 에러 발생도 확인해보자.

```js
const response = await axios.get(
  'https://jsonplaceholder.typicode.com/users/errorrrr',
);
```


## 4. API 재요청하기 
버튼을 눌러 API를 재요청하는 기능을 구현하려면, `fetchUsers` 함수를 바깥으로 꺼내고 버튼과 해당 함수를 연결해주면 된다. 

```js
import React, { useState, useEffect } from 'react';
import axios from 'axios';

const Users = () => {
  const [loading, setLoading] = useState(false);
  const [users, setUsers] = useState(null);
  const [error, setError] = useState(null);

  const fetchUsers = async () => {
    try {
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

  useEffect(() => {
    fetchUsers();
  }, []);

  if (loading) return <div>로딩중...</div>;
  if (error) return <div>에러 발생!</div>;
  if (!users) return <div>데이터가 없습니다.</div>;
  return (
    <div>
      // ... 
      <button onClick={fetchUsers}>다시 불러오기</button>
    </div>
  );
};

export default Users;
```
