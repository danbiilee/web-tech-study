# useCallback을 사용해 성능 최적화하기 

`useCallback`은 `useMemo`와 비슷한 `Hook`이다.

`useMemo`는 연산된 특정 결과값을 재사용할 때 사용하는 반면,    
`useCallback`은 특정 함수를 재사용할 때 사용한다. 



`useCallback`을 사용하지 않으면 함수들은 컴포넌트가 리렌더링될 때마다 새로 만들어진다.    
당장은 함수가 새로 선언되는 것만으로는 큰 부하가 생기지 않지만,   
나중에 컴포넌트에서 `props`가 바뀌지 않으면 Virtual DOM에 새로 렌더링조차 하지 않고 컴포넌트의 결과물을 재사용하는 최적화 작업을 할 때 `useCallback`을 사용해 함수를 재사용하는 것이 필수이다.    
컴포넌트 렌더링 최적화 작업을 하기 전에는 `useCallback`을 사용함으로써, 바로 눈에 띄는 성능 최적화를 이뤄낼 수 없음을 참고하자.  



이전에 App.js에서 구현했던 `onCreate`, `onRemove`, `onToggle`함수를 `useCallback`을 사용해 최적화할 수 있다. 

```js
// App.js
import React, { useRef, useState, useMemo, useCallback } from 'react';

function App() {
  // ...
  const onChange = useCallback(
    e => {
      const { name, value } = e.target;
      setInputs({
        ...inputs,
        [name]: value,
      });
    },
    [inputs]
  );

  const onCreate = useCallback(
    () => {
      const user = {
        id: nextId.current,
        name,
        email,
      };
      //setUsers([ ...users, user ]);
      setUsers(users.concat(user));
      setInputs({
        name: '',
        email: '',
      });
      nextId.current += 1;
    },
    [name, email, users]
  );

  const onRemove = useCallback(
    id => {
      setUsers(users.filter(user => user.id !== id));
    }, 
    [users]
  );

  const onToggle = useCallback(
    id => {
      setUsers(users.map(user => user.id === id ? {...user, active: !user.active} : user ));
    }, 
    [users]
  );
}
```

**[❗❗❗ 주의할 점]**    
함수 안에서 사용하는 `state`나 `props`가 있다면 꼭 `deps` 배열에 포함시켜야 한다.    
그렇지 않으면, 함수 내에서 해당 값들을 참조할 때 가장 최신 값을 참조할 거란 보장을 할 수 없게 된다.   
그리고 `props`로 전달받은 함수가 있다면, 이 또한 `deps` 배열에 넣어주어야 한다. 


`useCallback`은 `useMemo`를 기반으로 만들어졌기 때문에,    
아래와 같이 표현할 수도 있다. 

```js
const onToggle = useMemo(
  () => () => {
    // ...
  },
  [users]
);
```
