# 할 일 목록 구현하기

## 1. 프리젠테이셔널 컴포넌트 구현하기
먼저 components 디렉터리에 Todos.js 파일을 생성한다. 

이 파일에는 TodoItem, TodoList, Todos 이렇게 3개의 컴포넌트를 작성할 것이다. 이렇게 여러 개의 컴포넌트를 만들면 컴포넌트 리렌더링 성능의 최적화가 가능해진다.    
(취향에 따라 각각 다른 파일에 분리해 작성해도 괜찮다.) 

### components/Todos.js
```js
import React, { useState } from 'react';

// 최적화를 위해 React.memo 사용
const TodoItem = React.memo(({ todo, onToggle }) => {
  return (
    <li
      onClick={() => onToggle(todo.id)}
      style={{
        cursor: 'pointer',
        textDecoration: todo.done ? 'line-through' : 'none',
      }}
    >
      {todo.text}
    </li>
  );
});

// 최적화를 위해 React.memo 사용
const TodoList = React.memo(({ todos, onToggle }) => {
  return (
    <ul>
      {todos.map(todo => (
        <TodoItem key={todo.id} todo={todo} onToggle={onToggle} />
      ))}
    </ul>
  );
});

const Todos = ({ todos, onCreate, onToggle }) => {
  // 모든 상태를 리덕스에서 관리해야하는 건 아니다. 
  const [text, setText] = useState('');
  const onChange = e => {
    setText(e.target.value);
  };
  const onSubmit = e => {
    e.preventDefault(); // submit 이벤트 발생 시 새로고침 방지 
    onCreate(text);
    setText(''); // input 초기화 
  };

  return (
    <div>
      <form onSubmit={onSubmit}>
        <input
          type="text"
          value={text}
          onChange={onChange}
          placeholder="할 일을 입력하세요"
        />
        <button type="submit">등록</button>
      </form>
      <TodoList todos={todos} onToggle={onToggle} />
    </div>
  );
};

export default Todos;
```


## 2. 컨테이너 컴포넌트 구현하기 
containers 디렉터리에 TodosContainer.js 파일을 생성 후 다음과 같이 작성한다.

### containers/TodosContainer.js
```js
import React, { useCallback } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import Todos from '../components/Todos';
import { toggleTodo, addTodo } from '../modules/todos';

const TodosContainer = () => {
  // useSelector에서 원하는 값만 바로 반환해 조회할 수도 있다.
  // 꼭 객체를 반환해야할 필요는 없다. 
  const todos = useSelector(state => state.todos);
  const dispatch = useDispatch();

  const onCreate = text => dispatch(addTodo(text));
  const onToggle = useCallback(id => dispatch(toggleTodo(id)), [dispatch]); // 최적화를 위해 useCallback 사용 

  return <Todos todos={todos} onToggle={onToggle} onCreate={onCreate} />;
};

export default TodosContainer;
```

이제 이 컨테이너 컴포넌트를 App 컴포넌트에서 렌더링하면 된다. 

### App.js
```js
import React from 'react';
import CounterContainer from './containers/CounterContainer';
import TodosContainer from './containers/TodosContainer';

function App() {
  return (
    <div>
      <CounterContainer />
      <hr />
      <TodosContainer />
    </div>
  );
}

export default App;
```