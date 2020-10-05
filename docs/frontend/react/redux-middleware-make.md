# 미들웨어 만들어보기

## 1. 템플릿

리덕스 미들웨어를 만들 땐 다음 템플릿을 사용한다.

```js
const middleware = (store) => (next) => (action) => {
  // ...
};
```

미들웨어는 함수를 연달아서 두 번 리턴하는 함수이다. 이 함수를 `function` 키워드를 사용해 작성하면 다음과 같다.

```js
function middleware(store) {
  return function (next) {
    return function (action) {
      // ...
    };
  };
}
```

**✨ 각 함수에서 받아오는 파라미터 정보**

- `store`
  - 리덕스 스토어 인스턴스이다.
  - 이 안에 `dispatch`, `getState`, `subscribe` 내장함수들이 들어있다.
- `next`
  - 액션을 다음 미들웨어에게 전달하는 함수이다.
  - `next(action)` 형태로 사용한다.
  - 다음 미들웨어가 없다면 리듀서에게 액션을 전달한다.
  - `next`를 호출하지 않는다면 액션이 무시 처리되어 리듀서에게로 전달되지 않는다.
- `action`
  - 현재 처리하고 있는 액션 객체이다.

**✨ 미들웨어 구조**

![redux-middleware](../../../imgs/redux-middleware2.png)

미들웨어는 위와 같은 구조로 작동한다. 리덕스 스토어에는 여러 개의 미들웨어를 등록할 수 있고, 새로운 액션이 디스패치되면 첫 번째로 등록한 미들웨어가 호출된다.

만약 미들웨어에서 `next(action)`을 호출하면 다음 미들웨어로 액션이 넘어가게 되고, 만약 미들웨어에서 `store.dispatch`를 사용하면 다른 액션을 추가적으로 발생시킬 수도 있다.

## 2. 미들웨어 직접 만들어보기

단순히 전달받은 액션을 출력하고 다음으로 넘기는 미들웨어를 구현해보겠다.

src 디렉터리에 middlewares 라는 디렉터리를 생성 후, myLogger.js 파일을 다음과 같이 작성한다.

### middlewares/myLogger.js

```js
const myLogger = (store) => (next) => (action) => {
  console.log(action); // 액션 출력
  const result = next(action); // 다음 미들웨어(또는 리듀서)에게 액션 전달
  return result; // result = dispatch(action)의 결과물(기본: undefined)
};

export default myLogger;
```

## 3. 미들웨어 적용하기

이제 `applyMiddleware` 라는 함수를 사용해 미들웨어를 스토어에 적용해보자.

### index.js

```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { createStore, applyMiddleware } from 'redux';
import { Provider } from 'react-redux';
import rootReducer from './modules';
import myLogger from './middlewares/myLogger';

const store = createStore(rootReducer, applyMiddleware(myLogger));

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);

serviceWorker.unregister();
```

## 4. 미들웨어 수정하기

만약 액션이 리듀서까지 전달되고 난 후의 새로운 상태를 확인하고 싶다면 다음과 같이 수정할 수 있다.

### middlewares/myLogger.js

```js
const myLogger = (store) => (next) => (action) => {
  console.log(action);
  const result = next(action); // 다음 미들웨어(또는 리듀서)에게 액션 전달

  console.log('\t', store.getState()); // 업데이트 이후의 상태를 조회
  return result;
};

export default myLogger;
```

이렇게 미들웨어 안에서는 무엇이든 할 수 있다. 예를 들어 액션 값을 객체가 아닌 함수로 전달받아, 액션이 함수타입이면 이를 실행시키게 할 수도 있다. (이게 추후에 배울 `redux-thunk`이다.)

---

이번엔 액션 객체와 상태를 로깅하는 미들웨어를 직접 만들어보았지만, 리덕스 관련 값들을 콘솔에 로깅하는 건 [redux-logger](https://github.com/LogRocket/redux-logger) 라는 미들웨어를 사용하는 게 더욱 좋다.

다음 섹션에서는 `redux-logger` 라는 라이브러리를 설치 및 적용하고, 미들웨어를 사용하는 경우 리덕스 개발자도구를 어떻게 사용하는지 알아보도록 하겠다.
