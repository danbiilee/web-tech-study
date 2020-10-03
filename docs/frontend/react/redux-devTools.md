# 리덕스 개발자도구 적용하기

리덕스 개발자도구를 사용하면 현재 스토어의 상태를 조회할 수 있고, 지금까지 어떤 액션들이 디스패치 됐는지, 액션에 따라 상태가 어떻게 변화했는지 확인할 수 있다. 또한 액션을 직접 디스패치 할 수도 있다. 

먼저 [크롬 웹 스토어에서 확장 프로그램](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd)을 설치한다. 

그 다음엔 프로젝트에 `redux-devtools-extension`을 설치한다. 

```js
$ yarn add redux-devtools-extension
```

그리고 index.js를 다음과 같이 수정하면 적용이 완료된다.

### index.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import rootReducer from './modules';
import { composeWithDevTools } from 'redux-devtools-extension'; // 리덕스 개발자도구

const store = createStore(rootReducer, composeWithDevTools()); // 개발자도구 활성화

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root'),
);

serviceWorker.unregister();
```

이제 크롬 개발자도구의 Redux 탭을 열어 리덕스 개발자도구를 사용할 수 있게 됐다! 