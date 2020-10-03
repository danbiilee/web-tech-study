# 리덕스 모듈 만들기

## 1. Ducks 패턴
리덕스 모듈이란 다음 항목들이 모두 들어있는 자바스크립트 파일을 의미한다. 
- 액션 타입
- 액션 생성함수
- 리듀서

위 항목들은 각각 다른 파일에 저장할 수도 있지만 서로 다른 디렉터리에, 다른 파일에 분리되어 있으면 개발할 때 불편함이 생길 수 있다. 

따라서 우리는 [Ducks 패턴](https://github.com/erikras/ducks-modular-redux)에 따라 위 항목들을 하나의 파일에 몰아서 작성할 것이다. 

**✨ Ducks 패턴 규칙**
- 반드시 리듀서 함수는 `export default`로 내보낸다. 
- 반드시 액션 생성함수는 `export`로 내보낸다.
- 반드시 액션 타입의 형식은 `파일 디렉터리/ACTION_TYPE`를 따른다. 
- 보통 액션 타입명은 `UPPER_SNAKE_CASE` 형식을 따른다.


## 2. counter 모듈 만들기
첫 번째로 만들 모듈은 counter 모듈이다.

src 디렉터리에 modules 디렉터리를 만들고, 그 안에 counter.js 파일을 생성 후 다음 코드를 작성한다. 

### modules/counter.js
```js
// 액션 타입: Ducks 패턴에 따라 액션 타입명 앞에 접두사를 붙인다.
const SET_DIFF = 'counter/SET_DIFF';
const INCREASE = 'counter/INCREASE';
const DECREASE = 'counter/DECREASE';

// 액션 생성함수: export 키워드로 내보낸다.
export const setDiff = diff => ({ type: SET_DIFF, diff });
export const increase = () => ({ type: INCREASE });
export const decrease = () => ({ type: DECREASE });

// 초기 상태
const initialState = {
  number: 0,
  diff: 1,
};

// 리듀서: export default로 내보낸다.
export default function counter(state = initialState, action) {
  switch (action.type) {
    case SET_DIFF:
      return {
        ...state,
        diff: action.diff,
      };
    case INCREASE:
      return {
        ...state,
        number: state.number + state.diff,
      };
    case DECREASE:
      return {
        ...state,
        number: state.number - state.diff,
      };
    default:
      return state;
  }
}
```

## 3. todos 모듈 만들기
두 번째로는 mouldes 디렉터리에 todos.js 파일을 다음과 같이 작성한다. 

### modules/todos.js
```js
// 액션 타입
const ADD_TODO = 'todos/ADD_TODO';
const TOGGLE_TODO = 'todos/TOGGLE_TODO';

// 액션 생성함수
let nextId = 1; // todo에서 사용할 고유 id 
export const addTodo = text => ({
  type: ADD_TODO,
  todo: {
    id: nextId++, // 새 항목 추가후 nextId 값에 1을 더해준다.
    text,
  },
});
export const toggleTodo = id => ({
  type: TOGGLE_TODO,
  id,
});

// 초기 상태
// 리듀서의 초기 상태는 꼭 객체 타입이어야 하는 것은 아니다. 
/*
우리는 다음과 같은 todo 객체를 아래의 배열 안에 넣을 것이다.
{
  id: 1,
  text: 'test',
  done: false,
}
*/
const initialState = [];

// 리듀서
export default function todos(state = initialState, action) {
  switch (action.type) {
    case ADD_TODO:
      return state.concat(action.todo);
    case TOGGLE_TODO:
      return state.map(
        todo =>
          todo.id === action.id 
            ? { ...todo, done: !todo.done } 
            : todo
      );
    default:
      return state;
  }
}
```


## 4. 루트 리듀서 만들기
위에서 우리는 두 개의 리덕스 모듈을 만들었다. 그래서 현재 리듀서도 두 개이다. 한 프로젝트에 여러 개의 리듀서가 있을 때는 한 리듀서로 합쳐 사용하는데, 합쳐진 리듀서를 `루트 리듀서`라고 한다. 

리듀서를 합치는 작업은 리덕스에 내장되어 있는 `combineReducers` 라는 함수를 사용한다. 

modules 디렉터리에 index.js 파일을 생성 후 다음과 같이 코드를 작성한다. 

### modules/index.js
```js
import { combineReducers } from 'redux';
import counter from './counter';
import todos from './todos';

const rootReducer = combineReducers({
  counter,
  todos,
});

export default rootReducer;
```

루트 리듀서가 만들어졌으면, 이제 스토어를 생성한다. 

### src/index.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { createStore } from 'redux';
import rootReducer from './modules';

const store = createStore(rootReducer); // 스토어 생성
console.log(store.getState()); // 스토어의 상태 조회

ReactDOM.render(<App />, document.getElementById('root'));

serviceWorker.unregister();
```

## 5. 리액트 프로젝트에 리덕스 적용하기 

먼저 프로젝트에 `react-redux` 라는 라이브러리를 설치한다. 

```js
$ yarn add react-redux
```

그 다음엔 index.js에서 `Provider` 라는 컴포넌트를 불러와 App 컴포넌트를 감싸준다. 그리고 `Provider`의 `props`에 위에서 생성한 `store`를 넣어준다. 

### src/index.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import rootReducer from './modules';

const store = createStore(rootReducer);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root'),
);

serviceWorker.unregister();
```

이렇게 `store`를 `props`로 전달한 `Provider`로 App 컴포넌트를 감싸게 되면, 렌더링하는 모든 컴포넌트에서 리덕스 스토어에 접근할 수 있게 된다. 
