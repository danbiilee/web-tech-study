# 리덕스 기능 연습하기

리액트에서 리덕스를 사용해보기 전에 리액트 컴포넌트 없이, 리덕스에서 제공되는 기능들을 연습해보도록 하겠다. 

## 1. redux 설치

새 프로젝트를 생성 후 해당 디렉터리에서 `redux` 라이브러리를 설치한다. 

```js
$ yarn add redux
```

## 2. 연습 코드 작성

그 다음엔 index.js에서 exercise.js 파일을 불러온 후 다음과 같이 작성한다.

```js
// src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import './exercise';

ReactDOM.render(<App />, document.getElementById('root'));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister();
```

```js
// src/exercise.js

import { createStore } from 'redux';

// 리덕스에서 관리할 상태
const initialState = {
  counter: 0,
  text: '',
  list: [],
};

// 액션 타입: 주로 대문자로 작성한다. 
const INCREASE = 'INCREASE';
const DECREASE = 'DECREASE';
const CHANGE_TEXT = 'CHANGE_TEXT';
const ADD_TO_LIST = 'ADD_TO_LIST';

// 액션 생성함수: 주로 카멜케이스로 작성한다. 
// 액션은 객체 형태로 type 외에 필드를 마음대로 넣을 수 있다. 
const increase = () => ({
  type: INCREASE,
});
const decrease = () => ({
  type: DECREASE,
});
const changeText = text => ({
  type: CHANGE_TEXT,
  text, 
});
const addToList = item => ({
  type: ADD_TO_LIST,
  item,
});

// 리듀서:
// 위 액션 생성함수들을 통해 만들어진 액션 객체를 참조하여 새로운 상태를 만드는 함수 
// 리듀서에선 불변성을 반드시 지켜줘야 한다! 
function reducer(state = initialState, action) {
  switch (action.type) {
    case INCREASE:
      return {
        ...state,
        counter: state.counter + 1,
      };
    case DECREASE:
      return {
        ...state,
        counter: state.counter - 1,
      };
    case CHANGE_TEXT:
      return {
        ...state,
        text: action.text,
      };
    case ADD_TO_LIST:
      return {
        ...state,
        list: state.list.concat(action.item),
      };
    default:
      return state;
  }
}

// 스토어 생성
const store = createStore(reducer);

// 리스너 함수: 스토어 안의 상태가 바뀔때마다 호출되는 함수
const listener = () => {
  console.log(store.getState());
};

// 구독: subscribe 함수의 파라미터로 전달한 함수가 액션이 디스패치될 때마다 호출된다.
const unsubscribe = store.subscribe(listener);
// 구독 해제: unsibscribe() 호출. 

// 액션 디스패치: dispatch 함수의 파라미터로 액션 객체(액션 생성함수)를 전달한다. 
store.dispatch(increase());
store.dispatch(decrease());
store.dispatch(changeText('hiiii'));
store.dispatch(addToList({ id: 1, text: 'hiiiii1' }));
```

리덕스 스토어 안의 상태는 액션이 디스패치될 때마다 업데이트 된다. 

여기서 우리는 `listener`라는 함수를 만들어서 리덕스 상태에 변화가 생길 때마다 콘솔에 상태를 출력하도록 처리했다. 

