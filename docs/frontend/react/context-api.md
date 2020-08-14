# Context API를 사용해 전역 값 관리하기

App 컴포넌트에서 구현한 `onToggle`, `onRemove` 함수는 아래와 같이 UserList 컴포넌트를 거쳐 각 User 컴포넌트들에게 전달된다.  

```js
// UserList.js
function UserList({ users, onRemove, onToggle }) {
  return (
    <div>
      {users.map(user => (
        <User
          user={user}
          key={user.id}
          onRemove={onRemove}
          onToggle={onToggle}
        />
      ))}
    </div>
  );
}
```

여기서 UserList 컴포넌트는 해당 함수들을 직접 사용하지 않고 중간 다리 역할만 하고 있다.        
만약 이보다 더 복잡한 구조를 가진 프로젝트에서 이와 같이 특정 함수를 컴포넌트를 거쳐 전달을 해야 한다면 매우 번거로울 것이다. 

이 때 `Context API`와 `dispatch` 함수를 함께 사용하면    
프로젝트 안에서 **전역적**으로 사용할 수 있는 **값**을 관리할 수 있다. 

> (❗) `Context API`가 관리하는 건 꼭 `state`가 아니어도 된다.    
> 그 값은 함수일 수도, 외부 라이브러리의 인스턴스일 수도, 심지어 DOM일 수도 있다. 



## 1. Context 만들기

`React.createContext()` 함수를 이용해 새로운 `Context`를 만든다.     
파라미터에는 Context를 쓸 때 값을 따로 지정하지 않을 경우 사용되는 기본값을 전달한다. 

```js
const UserDispatch = React.createContext(null);
```

`Context` 안에는 `Provider`라는 컴포넌트가 들어있다. 

먼저 `Provider` 컴포넌트로 다른 컴포넌트들을 감싸준다.  
그리고 `Provider` 컴포넌트의 `value`라는 값에 `dispatch`함수를 전달해 `Context`의 값으로 설정한다. 

그러면 `Provider`에 의해 감싸진 컴포넌트들은 어디서든 `Context`의 값인 `dispatch`함수를 조회해 사용할 수 있게 된다. 

```js
<UserDispatch.Provider value={dispatch}>
  // ...
</UserDispatch.Provider>
```


App 컴포넌트에 Context API를 적용해보자. 

```js
// App.js
function App() {
  // ...
  export const UserDispatch = React.createContext(null); // 1

  return (
    <UserDispatch.Provider value={dispatch}>
      // ... 
      <UserList users={users} /> // 2
    </UserDispatch.Provider>
  )
}
```

**1️⃣ Context 생성하기**
- UserDispatch라는 이름으로 `Context`를 만들고, 동시에 `export`하여 내보내준다.    
- 그러면 다른 파일에서 아래와 같이 불러와서 사용할 수 있다.

```js
import { UserDispatch } from './App';
```

**2️⃣ 관련된 코드 지우기**
- `onToggle`, `onRemove` 함수, UserList에게 `props`를 전달하는 코드를 지워준다. 
- UserList 컴포넌트에서 `onToggle`, `onRemove` 함수와 관련된 코드들을 지워준다. 

```js
// UserList.js
const User = React.memo(function User({ user }) {
  return (
    <div>
      <b
        style={{
          cursor: 'pointer',
          color: user.active ? 'green' : 'black'
        }}
        onClick={() => {}}
      >
        {user.username}
      </b>
      &nbsp;
      <span>({user.email})</span>
      <button onClick={() => {}}>삭제</button>
    </div>
  );
});

function UserList({ users }) {
  return (
    <div>
      {users.map(user => (
        <User user={user} key={user.id} />
      ))}
    </div>
  );
}
```



## 2. Context 조회하기

이제 User 컴포넌트에서 `dispatch`를 사용할 차례이다.    
그러기 위해서는 `Hook`의 `useContext` 함수를 사용해 위에서 만든 UserDispatch Context를 조회해야 한다. 


```js
// UserList.js
import { UserDispatch } from './App'; 

const User = React.memo(function User({ user }) {
  const dispatch = useContext(UserDispatch); // 1

  return (
    <div>
      <b
        style={{
          cursor: 'pointer',
          color: user.active ? 'green' : 'black'
        }}
        onClick={() => {
          dispatch({ type: 'TOGGLE_USER', id: user.id }); // 2
        }}
      >
        {user.username}
      </b>
      &nbsp;
      <span>({user.email})</span>
      <button
        onClick={() => {
          dispatch({ type: 'REMOVE_USER', id: user.id }); // 2 
        }}
      >
        삭제
      </button>
    </div>
  );
});
```

**1️⃣ `dispatch` 조회하기**
- `useContext` 함수의 파라미터로 `import`한 UserDispatch Context를 전달해 `dispatch`를 반환받는다.


**2️⃣ `dispatch` 사용하기**
- `dispatch` 함수에 `action` 객체를 전달함으로써 User 컴포넌트가 `onToggle`, `onRemove`함수를 전달받지 않았음에도 사용할 수 있도록 코드를 작성한다. 




## 3. useReducer 🆚 useState

[지난 번](./useReducer.md)과 다르게 `useState`를 사용하는 것과 `useReducer`를 사용하는 것의 큰 차이를 느낄 수 있었을 것이다. 

`useReducer`를 사용하면 이렇게 `dispatch`와 `Context API`를 통해 전역적으로 사용하는 값을 관리할 수 있고,    
그로 인해 코드의 구조가 훨씬 깔끔해질 수 있다. 

깊은 곳에 위치하는 컴포넌트에게 여러 컴포넌트를 거쳐 함수를 전달해야 한다면,     
이렇게 `useReducer`와 `Context API`를 사용하면 된다. 