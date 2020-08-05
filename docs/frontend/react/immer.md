# Immer를 사용한 state의 불변성 지키기


리액트에서 배열이나 객체를 업데이트 할 때는 직접 수정하지 않고,    
`spread 연산자(...)`를 사용해 새로운 객체를 만듦으로써 불변성을 지켜주면서 업데이트 해야 한다. 

```js
const object = {
  a: 1,
  b: 2
};

object.b = 3; // X

const nextObject = { // O
  ...object,
  b: 3
};
```

배열도 마찬가지로 `push`, `splice` 등의 원본 배열을 변경하는 함수를 사용하거나 n번째 항목을 직접 수정하면 안되고,    
`concat`, `filter`, `map` 등의 함수를 사용해 원본 배열 변경없이 새 배열을 만듦으로써 불변성을 지켜줘야 한다. 

```js
const todos = [
  {
    id: 1,
    text: '할 일 #1',
    done: true
  },
  {
    id: 2
    text: '할 일 #2',
    done: false
  }
];

const inserted = todos.concat({
  id: 3,
  text: '할 일 #3',
  done: false
});

const filtered = todos.filter(todo => todo.id !== 2);

const toggled = todos.map(
  todo => todo.id === 2
    ? {
      ...todo,
      done: !todo.done,
    }
    : todo
);
```

하지만 `state`의 구조가 복잡해지면 불변성을 지켜가며 업데이트 하는게 어려워진다. 

이럴 때 `Immer`라는 라이브러리를 사용한다.     
`Immer`를 사용하면 `state`를 업데이트할 때 불변성을 신경쓰지 않아도 `Immer`가 불변성 관리를 알아서 대신 해준다. 




## 1. 사용법

먼저 프로젝트에서 아래의 명령어를 실행해 `Immer`를 설치한다.

```js
$ yarn add immer
```

그리고 `Immer`를 import 한다. 보통 `produce`라는 이름으로 불러온다. 

```js
import produce from 'immer';
```

**✨ `produce` 함수**
- 첫 번째 파라미터: 수정하고 싶은 상태
- 두 번째 파라미터: 어떻게 업데이트 할 지를 정의하는 함수

```js
const state = {
  number: 1,
  dontChangeMe: 2
};

const nextState = produce(state, draft => {
  draft.number += 1;
});
```



## 2. Reducer에서 Immer 사용하기 

`produce`에 두 번째 파라미터로 전달하는 함수에서는 불변성을 신경쓰지 않고 직접 `state`값을 변경해도 된다.   

하지만 우리가 만든 App 컴포넌트의 `state`는 구조가 복잡하지 않기 때문에   
새 항목을 추가하거나, 삭제하는 경우에는 오히려 `Immer`를 쓰는 것보단 기존의 `concat`, `filter` 함수를 사용하는 것이 코드가 더 간결하고 편하다. 

이처럼 `Immer`를 사용한다고 해서 무조건 코드가 간결해지는 것은 아니다. 

```js
// App.js
import produce from 'immer';

function reducer(state, action) {
	switch(action.type) {
		case 'CREATE_USER':
			return produce(state, draft => {
        draft.users.push(action.user);
      });
		case 'TOGGLE_USER':
      return produce(state, draft => {
        const user = draft.users.find(user => action.id === user.id);
        user.active = !user.active;
      });
		case 'REMOVE_USER':
      return produce(state, draft => {
        const index = draft.users.findIndex(user => action.id === user.id);
        draft.users.splice(index, 1);
      });
		default:
			return state;
	}
}
```



## 3. Immer와 함수형 업데이트 

아래처럼 `useState`를 사용할 때 setter에 함수형 업데이트를 전달하면 `useCallback` 함수의 두 번째 파라미터로 `deps` 배열을 전달해도 된다. 

```js
const [todo, setTodo] = useState({
  text: 'Hello',
  done: false
});

const onClick = useCallback(() => {
  setTodo(todo => ({
    ...todo,
    done: !todo.done
  }));
}, []);
```

이렇게 함수형 업데이트를 하는 경우 `Immer`를 사용하면, 상황에 따라 더 편하게 코드를 작성할 수 있다. 


**✨ `produce` 함수**
- 두 개의 파라미터를 전달하는 경우
  - 첫 번째 파라미터에 넣은 `state`의 불변성을 유지하면서 업데이트된 새로운 `state`를 반환한다. 
- 첫 번째 파라미터를 생략한 경우
  - `state`를 업데이트하는 함수를 반환한다.

```js
const todo = {
  text: 'Hello',
  done: false
};

const updater = produce(draft => {
  draft.done = !draft.done;
});

const nextTodo = updater(todo); // { text: 'Hello', done: true }
```

즉, `useState`의 setter에 업데이트 함수를 반환하는 `produce`를 전달하면,    
함수형 업데이트를 아래처럼 구현할 수 있게 된다. 

```js
const [todo, setTodo] = useState({
  text: 'Hello',
  done: false
});

const onClick = useCallback(() => {
  setTodo(
    produce(draft => {
      draft.done = !draft.done;
    })
  );
}, []);
```

---
**[❗❗❗]**  
성능적으로는 `Immer`를 사용하지 않은 코드가 조금 더 빠르다.   

그리고 `Immer`는 Javascript 엔진의 `Proxy` 기능을 사용하는데, 이는 구형 브라우저 및 `React-Native` 같은 환경에서는 지원되지 않으므로 `ES5 fallback`을 사용하게 된다.   
`ES5 fallback`을 사용하게 되면 `Immer`를 사용한 것보다도 더 느려지게 된다. 

그러므로 가능하면 `state`의 구조가 복잡해지는 것을 방지하고,   
`state`의 구조가 복잡해져서 불변성을 유지하기가 어려운 상황이 온다면 `Immer`를 사용하되, 필요한 곳에만 쓰고 간단히 처리될 수 있는 곳에는 사용하지 않는 것을 권장한다. 

