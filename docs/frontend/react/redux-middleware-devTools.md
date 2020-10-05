# redux-logger 사용 및 리덕스 개발자도구 적용하기

## 1. redux-logger 사용하기

먼저 프로젝트에 `redux-logger`를 설치한다.

```js
$ yarn add redux-logger
```

그 다음엔 index.js에서 불러와 적용한다. 다음과 같이 리덕스에는 여러 개의 미들웨어를 적용할 수 있다.

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
import logger from 'redux-logger';

// 여러 개의 미들웨어 적용
const store = createStore(rootReducer, applyMiddleware(myLogger, logger));

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);

serviceWorker.unregister();
```

콘솔에서 `redux-logger`를 통해 리덕스 관련 정보들이 예쁘게 출력됨을 확인 후, 우리가 만든 myLogger는 지워준다.

## 2. 미들웨어와 함께 리덕스 개발자도구 사용하기

먼저 프로젝트에 `redux-devtools-extension`을 설치한다.

```js
$ yarn add redux-devtools-extension
```

Redux DevTool를 미들웨어와 함께 사용하는 방법은 [공식 문서](https://www.npmjs.com/package/redux-devtools-extension#usage)에서 아래와 같이 제안하고 있다.

```js
import { createStore, applyMiddleware } from 'redux';
import { composeWithDevTools } from 'redux-devtools-extension';

const store = createStore(
  reducer,
  composeWithDevTools(
    applyMiddleware(...middleware)
    // other store enhancers if any
  )
);
```

그럼 이에 따라 index.js를 다음과 같이 수정해보자.

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
import logger from 'redux-logger';
import { composeWithDevTools } from 'redux-devtools-extension';

const store = createStore(
  rootReducer,
  composeWithDevTools(applyMiddleware(logger))
);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);

serviceWorker.unregister();
```
