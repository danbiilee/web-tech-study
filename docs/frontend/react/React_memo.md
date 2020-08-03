# React.memo를 통한 성능 최적화 하기 

`React.memo`를 사용하면 컴포넌트의 `props`가 바뀌지 않았을 때,     
리렌더링을 방지하여 컴포넌트의 리렌더링 성능 최적화를 할 수 있다. 


아래처럼 컴포넌트를 `React.memo`로 감싸주면 된다. 

```js
// CreateUser.js
const CreateUser = ({ username, email, onChange, onCreate }) => {
  return (
    // ...
  );
};

export default React.memo(CreateUser);
```

```js
// UserList.js

// User Component
const User = React.memo(function User({ user, onRemove, onToggle }) {
  return (

  );
}

// UserList Component
function UserList({ users, onRemove, onToggle }) {
  return ( 
    //... 
  );
}

export default React.memo(UserList);
```

수정 후, `input` 태그의 값을 수정할 때 아래의 `UserList` 컴포넌트가 리렌더링되지 않는 걸 확인할 수 있다. 

그런데 `User` 중 하나라도 수정되면,    
모든 `User`들이 리렌더링되고 `CreateUser` 컴포넌트도 리렌더링 된다.   
`useCallback`함수의 두 번째 `deps` 파라미터로 `users`를 참조하고 있기 때문에 `users` 배열이 바뀔 때마다 `onCreate`, `onToggle`, `onRemove`가 새로 선언되기 때문이다.    

이를 최적화하기 위해선 **함수형 업데이트**를 통해 `deps`에서 `users`를 지우고, `useState`로 관리하는 `users`를 참조하지 않게 해야 한다.

함수형 업데이트를 하면 `setUsers`에 등록한 콜백함수의 파라미터로 최신 `users`를 전달할 수 있기 때문에 `deps` 에 `users`를 넣지 않아도 된다. 


아래와 같이 수정하면, 특정 `User`를 수정할 때 해당 `User`만 리렌더링 될 것이다. 

```js
// App.js
function App() {
  // ...
  const onChange = useCallback(e => {
    const { name, value } = e.target;
    setInputs(inputs => ({
      ...inputs,
      [name]: value
    }));
  }, []);

  const onCreate = useCallback(() => {
    const user = {
      id: nextId.current,
      username,
      email
    };
    setUsers(users => users.concat(user));
    // ...
  }, [username, email]);

  const onRemove = useCallback(id => {
    setUsers(users => users.filter(user => user.id !== id));
  }, []);

  const onToggle = useCallback(id => {
    setUsers(users =>
      users.map(user =>
        user.id === id ? { ...user, active: !user.active } : user
      )
    );
  }, []);
}
```


> [❗❗❗]    
> `useMemo`, `useCallback`, `React.memo`는 실제로 컴포넌트의 성능을 개선할 수 있는 상황에서만 사용하자.    
> - User 컴포넌트에서 `b`와 `button`에 `onClick`으로 설정한 `onToggle`과 `onRemove`함수들은 `useCallback`으로 재사용해도 리렌더링을 막을 수 없으므로, 굳이 재사용할 필요 없다. 
> - 렌더링 최적화를 하지 않을 컴포넌트에 `React.memo`를 사용하는 건 불필요한 `props` 비교만 하는 것이므로 할 필요 없다. 


`React.memo`의 두 번째 파라미터로 `propsAreEqual`이라는 함수를 전달해 특정 값들만 비교를 하는 것도 가능하다.    

```js
export default React.memo(
  UserList,
  (prevProps, nextProps) => prevProps.users === nextProps.users
);
```

하지만 이걸 잘못 사용할 경우 버그가 발생할 수 있다.      
만약 `onToggle`과 `onRemove`에서 함수형 업데이트를 하지 않은 경우, 최신 users 배열을 참조하지 않으므로 users만 비교하는 것은 심각한 오류를 발생시킬 수 있다. 


