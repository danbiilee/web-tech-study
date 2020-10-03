# 카운터 구현하기

## 1. 프리젠테이셔널 컴포넌트와 컨테이너 컴포넌트

우리는 이번에 `프리젠테이셔널 컴포넌트(Presentational Component)`와 `컨테이너 컴포넌트(Container Component)`를 분리해서 작업할 것이다. 

리덕스의 창시자 Dan Abramov가 [이러한 패턴](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)을 소개하면서부터 이 방식이 정석이 되었지만, 2019년에 본인이 썼던 포스트를 수정하면서 꼭 이런 패턴으로 작성할 필요는 없다고 명시했다. 

**✨ Presentational Component**
- 어떻게 보이는 지를 고려한다. 
- 주로 DOM 마크업과 스타일에 관한 코드를 포함한다. 
- Flux actions나 stores 같은 다른 앱에 의존하지 않는다. 
- 데이터를 로드하거나 변경하지 않는다. 
- 오로지 props를 통해 데이터와 콜백함수를 전달받아 사용한다.
- UI에 관련된 걸 제외하고, 컴포넌트 내부에 state를 정의하지 않는다. 

**✨ Container Component**
- 어떻게 작동하는 지를 고려한다. 
- div를 감싸는 것을 제외하고는 DOM 마크업을 포함하지 않으며, 어떠한 스타일 코드도 포함하지 않는다. 
- 다른 컴포넌트들에게 데이터나 함수들을 제공한다.
- Flux actions을 호출하고, 이들을 프리젠테이셔널 컴포넌트에게 콜백함수로서 제공한다.
- 보통 `HOC`로 사용된다. 


## 2. 프리젠테이셔널 컴포넌트 만들기
프리젠테이셔널 컴포넌트란, 리덕스 스토어에 직접적으로 접근하지 않고 필요한 값이나 함수를 `props`로만 받아와서 사용하는 컴포넌트이다. 

src 디렉터리에 components 디렉터리를 만들고, 그 안에 Counter.js를 다음과 같이 작성한다. 

### components/Counter.js
```js
import React from 'react';

const Counter = ({ number, diff, onSetDiff, onIncrease, onDecrease }) => {
  const onChange = e => {
    // e.target.value는 문자열이므로 숫자로 변환해야 한다. 
    onSetDiff(parseInt(e.target.value, 10));
  };

  return (
    <div>
      <h1>{number}</h1>
      <div>
        <input type="number" value={diff} min="1" onChange={onChange} />
        <button onClick={onIncrease}>+</button>
        <button onClick={onDecrease}>-</button>
      </div>
    </div>
  );
};

export default Counter;
```


## 3. 컨테이너 컴포넌트 만들기
컨테이너 컴포넌트란, 리덕스 스토어의 상태를 조회하거나 액션을 디스패치할 수 있는 컴포넌트를 말한다. 그리고 HTML 태그들을 사용하는 대신, 다른 프리젠테이셔널 컴포넌트들을 불러와서 사용한다. 

src 디렉터리에 containers 디렉터리를 만들고 CounterContainer.js를 다음과 같이 작성한다. 

### containers/CounterContainer.js
```js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { setDiff, increase, decrease } from '../modules/counter';
import Counter from '../components/Counter';

const CounterContainer = () => {
  // useSelector: 리덕스 스토어의 상태를 조회하는 Hook
  // state = store.getState()
  const { number, diff } = useSelector(state => ({
    number: state.counter.number,
    diff: state.counter.diff,
  }));

  // useDispatch: 리덕스 스토어의 dispatch를 사용할 수 있게 해주는 Hook
  const dispatch = useDispatch();
  // 액션을 디스패치하는 함수들: 
  // dispatch에 액션 생성함수(액션 객체)를 전달하면, 스토어가 리듀서에서 해당 액션 타입과 일치하는 로직을 찾아 상태를 변경한다. 
  const onSetDiff = diff => dispatch(setDiff(diff));
  const onIncrease = () => dispatch(increase());
  const onDecrease = () => dispatch(decrease());

  return (
    <div>
      <Counter
        number={number}
        diff={diff}
        onSetDiff={onSetDiff}
        onIncrease={onIncrease}
        onDecrease={onDecrease}
      />
    </div>
  );
};

export default CounterContainer;
```

이제 App 컴포넌트에서 CounterContainer 컴포넌트를 불러와 렌더링한다. 

### App.js
```js
import React from 'react';
import CounterContainer from './containers/CounterContainer';

function App() {
  return (
    <div>
      <CounterContainer />
    </div>
  );
}

export default App;
```