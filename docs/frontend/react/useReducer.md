# useReducer

`state`를 관리할 때 `useState`를 사용하지 않고, `useReducer`를 사용해도 된다.       

이 `Hook`함수를 사용하면 컴포넌트의 `state` 업데이트 로직을 컴포넌트에서 분리시킬 수 있다. 또 로직을 컴포넌트 바깥에 작성할 수도 있고, 다른 파일에 작성 후 불러와 사용할 수도 있다.    




## 1. `reducer` 함수

```js
function reducer(state, action) {
  // 새로운 상태를 만드는 로직
  return nextState;
}
```

**🔸 파라미터**   

- `state`: 현재 `state` 값
- `action`
  - 업데이트를 위한 정보를 가지고 있다.
  - 주로 `type`값을 지닌 **객체 형태**로 사용한다. 
  - `type` 값은 주로 `대문자`와 `_`로 작성한다. 
  
  ```js
  // 카운터에 1을 더하는 액션
  {
    type: 'INCREMENT'
  }

  // 새 할 일을 등록하는 액션
  {
    type: 'ADD_TODO',
    todo: {
      id: 1,
      text: 'useReducer 배우기',
      done: false,
    }
  }
  ```

- `nextState`
  - `reducer`함수가 반환하는 새로운 `state`로 곧, 컴포넌트가 지닐 새로운 `state`이다. 




## 2. `useReducer` 사용법

```js
const [ state, dispatch ] = useReducer(reducer, initialState);
```

**🔸 파라미터**   
- `reducer`: `reducer` 함수
- `initialState`: 초기 `state` 값

**🔸 반환하는 배열 값**
- `state`: 컴포넌트에서 사용할 `state`
- `dispatch`
  - 액션을 발생시키는 **함수** 
  - 사용법: `dispatch({ type: 'INCREMENT' })`


---

Counter 컴포넌트를 `useReducer`로 구현하면 어떻게 바뀔까?    
아래는 기존의 Counter 컴포넌트 코드이다. 

```js
function Counter() {
  const [number, setNumber] = useState(0);

  const onIncrease = () => {
    setNumber(prevNumber => prevNumber + 1);
  };

  const onDecrease = () => {
    setNumber(prevNumber => prevNumber - 1);
  };

  return (
    // ... 
  );
}
```

`useReducer`를 적용시키면 아래와 같이 바뀐다. 

```js
function reducer(state, action) {
  switch(action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;  
    default:
      return state;
  }
}

function Counter() {
  const [ number, dispatch ] = useReducer(reducer, 0);

  const onIncrease = () => {
    dispatch({ type: 'INCREMENT' });
  }  
  const onDecrease = () => {
    dispatch({ type: 'DECREMENT' });
  } 
}
```

App 컴포넌트도 `useReducer`를 사용해 변경하면 아래와 같다. 

```js
const initialState = { // 1
  inputs: {
    username: '',
    email: '',
  },
  users: [
    {
      id: 1,
      username: 'danbi',
      email: 'danbi@gmail.com',
      active: true
    },
    {
      id: 2,
      username: 'ash-island',
      email: 'ash@example.com',
      active: false
    },
    {
      id: 3,
      username: 'sik-k',
      email: 'sikk@example.com',
      active: false
    }
  ]
};

function reducer(state, action) { // 2
  switch(action.type) {
    case 'CHANGE_INPUT': 
      return { 
        ...state,
        inputs: {
          ...state.inputs,
          [action.name]: action.value,
        }
      };
    case 'CREATE_USER':
      return {
        inputs: initialState.inputs,
        users: state.users.concat(action.user),
      };
    default:
      return state;
  }
}

function App() {
  const [ state, dispatch ] = useReducer(reducer, initialState); // 3
  const nextId = useRef(4);

  const { users } = state; // 3
  const { username, email } = state.inputs;

  const onChange = useCallback(e => {
    const { name, value } = e.target;
    dispatch({ // 4 
      type: 'CHANGE_INPUT',
      name,
      value,
    });
  }, []);

  const onCreate = useCallback(() => {
    dispatch({ // 4
      type: 'CREATE_USER',
      user: {
        id: nextId.current++, 
        username,
        email
      }
    });
  }, [ username, email ]);
  
  return (
    // ...
  );
}
```

**1️⃣ App 컴포넌트 바깥으로 `state` 분리하기**
- 기존의 `useState`로 관리되던 `inputs`와 `users` 배열을 `initialState`라는 하나의 객체로 묶어준다. 
- 이후 `useReducer` 함수의 두 번째 파라미터로 전달한다. 


**2️⃣ App 컴포넌트 바깥에 `reducer` 함수 작성하기**
- `action` 객체의 `type`값에 따라 분기한다.
- `reducer`에서 새로운 `state` 를 만들 때 역시 불변성을 유지해야 하므로 `spread 연산자(...)`를 사용한다. 
- 이후 `useReducer` 함수의 첫 번째 파라미터로 전달한다. 


**3️⃣ 구조 분해 할당으로 필요한 값들 추출하기**
- 배열을 반환하는 `useReducer` 함수에서 최신 `state`값과 `dispatch` 함수를 추출한다.
- 위에서 추출한 `state` 객체에서 다시 `inputs`와 `users`를 추출한다. 


**4️⃣ `dispatch` 함수 작성하기**
- 파라미터로 `type`값이 담긴 객체를 전달한다. 
- 여기서 작성한 `type`값과 `reducer`함수 안에서 switch문으로 분기한 `action.type`값은 일치해야 한다.  
- `reducer`함수 안에서 `state`값을 업데이트하기 위해 필요한 값들을 추가적으로 객체에 담아준다.



## 3. useReducer 🆚 useState

어떨 때 `useState`를 쓰고, 어떨 때 `useReducer`를 써야한다는 규칙은 없다.     

만약 컴포넌트에서 관리하는 `state`값이 하나이고,     
그 값이 단순한 문자열과 같은 값이라면 `useState`로 관리하는 게 편할 것이다.    

하지만 컴포넌트에서 관리하는 값이 여러 개가 되어서 `state`의 구조가 복잡해진다면 `useReducer`로 관리하는 게 더 편할 수도 있다.     

또 아래처럼 `setter`를 한 함수에서 여러 번 사용해야 하는 일이 발생한다면 `useReducer`를 쓸 지 고민해보자. 

```js
const onCreate = () => {
  setUsers(users => users.concat(user));
  setInputs({
    username: '',
    email: ''
  });
}
```

`useReducer`를 써서 편해질 거 같으면 `useReducer`를 쓰고, 그럴 것 같지 않으면 `useState`를 유지하면 된다. 


