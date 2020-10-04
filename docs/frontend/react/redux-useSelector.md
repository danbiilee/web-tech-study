# useSelector 최적화

리액트 컴포넌트에서 리덕스 상태를 조회해 사용할 때 최적화를 하기 위해, 지난 번 할 일 목록 구현하기에서는 프리젠테이셔널 컴포넌트에서 `React.memo`를 사용했었다.   

이번에는 **컨테이너 컴포넌트**에서 어떤 것들을 검토해야 하는지 알아보겠다. 

---
먼저 리액트 개발자 도구의 톱니바퀴를 눌러 Highlight Updates를 체크 후, 카운터의 +, - 를 눌러보면 하단의 할 일 목록은 리렌더링되지 않지만, 할 일 목록을 등록하거나 토글하면 카운터가 리렌더링됨을 확인할 수 있다. 

그 이유는 기본적으로 `useSelector`를 사용해 리덕스 스토어의 상태를 조회할 땐, 상태가 바뀌지 않았으면 리렌더링하지 않음에 있다. 

TodosContainer의 경우 카운터 값이 바뀔 때 `todos` 값엔 변화가 없으므로 리렌더링 되지 않는 것이다.

### containers/TodosContainer.js
```js
const todos = useSelector(state => state.todos);
```

하지만 CounterContainer의 경우 `useSelector`를 통해, 매번 렌더링될 때마다 새로운 객체 `{ number, diff }`를 만드는 것이기 때문에 상태가 바뀌었는지를 확인할 수 없어서 리렌더링이 이루어지고 있는 것이다. 

### containers/CounterContainer.js
```js
const { number, diff } = useSelector(
  state => ({
    number: state.counter.number,
    diff: state.counter.diff,
  }));
```

이를 최적하기 위한 두 가지 방법이 있다. 

## 1. useSelector 여러 번 사용하기
이렇게 하면 해당 값들이 하나라도 바뀌었을 때만 컴포넌트가 리렌더링 될 것이다.

```js
const number = useSelector(state => state.counter.number);
const diff = useSelector(state => state.counter.diff);
```

## 2. shallowEqual 함수 사용하기 
두 번째 방법으로는 `useSelector`의 두 번째 파라미터를 사용하는 것이다. 

`useSelector`의 두 번째 파라미터는 `eqaulityFn` 으로 이전 값과 다음 값을 비교해 `true`가 나오면 리렌더링을 하지 않고, `false`가 나오면 리렌더링을 하는 함수인데,
   
이 자리에 객체 안의 가장 겉에 있는 값들을 모두 비교해주는 `react-redux`의 내장함수인 `shallowEqual` 함수를 전달해 주는 것이다. 

### containers/CounterContainer.js
```js
import React from 'react';
import { useSelector, useDispatch, shallowEqual } from 'react-redux';
import { setDiff, increase, decrease } from '../modules/counter';
import Counter from '../components/Counter';

const CounterContainer = () => {
  const { number, diff } = useSelector(
    state => ({
      number: state.counter.number,
      diff: state.counter.diff,
    }),
    shallowEqual,
  );

  // ... 
};
```

