# useMemo를 사용해 성능 최적화하기 

`useMemo`라는 `Hook`을 이용해 연산된 값을 재사용하여 컴포넌트의 불필요한 리렌더링을 막아 성능을 최적화할 수 있다. 

아래는 이전의 `UserList` 컴포넌트 예제에 이어,    
`active`값이 `true`인 사용자의 수를 세어 화면에 렌더링하는 예제이다.


```js
// App.js
function countActiveUsers(users) {
  return users.filter(user => user.active).length;
}

function App() {
  // ...
  const [users, setUsers] = useState([
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com',
      active: true
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com',
      active: false
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com',
      active: false
    }
  ]);
  const count = countActiveUsers(users);

  return (
    // ...
    <div>활성 사용자 수 : {count}</div>
  );
}
```

다른 `username`을 클릭해 초록색으로 만들면 활성 사용자 수 또한 업데이트 된다.   

그런데 이 때 발생하는 성능적 이슈가 있다.     
`users`에 변화가 있을 때 뿐만 아니라 `input` 태그의 값이 바뀔 때에도 `countAtcvieUsers` 함수가 호출된다는 것이다.

이럴 때 `useMemo`라는 `Hook` 함수를 사용해 성능을 최적화할 수 있다. 

```js
import React, { useRef, useState, useMemo } from 'react';

function App () {
  // ...
  const count = useMemo(() => countActiveUsers(users), [users]);
}
```


`useMemo()` 함수의 

- 첫 번째 파라미터에는 연산을 담당하는 함수,
- 두 번째 파라미터에는 `deps` 배열    

을 전달한다. 

그럼 `deps` 배열이 변경됐을 때만 등록한 연산 함수가 호출되어 값을 연산하고,   
변경되지 않았다면 이전에 연산한 값을 재사용하여 리렌더링이 발생하지 않게 된다. 






