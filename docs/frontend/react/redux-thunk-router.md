# thunk 함수에서 라우터 연동하기

로그인 요청을 할 때 로그인 성공 시 `/` 경로로 이동시키고, 실패 시 경로를 유지하는 등 thunk 함수 내에서 라우터를 사용해야 할 때도 있다.

그냥 컨테이너 컴포넌트 내에서 `withRouter`를 사용해서 `props`로 `history`를 받아와 사용해도 상관은 없지만, thunk에서 처리하면 코드가 훨씬 깔끔해질 수 있다.

## 1. customHistory 만들어 적용하기

thunk 내에서 라우터의 `history` 객체를 사용하려면, `BrowserHistory` 인스턴스를 직접 만들어서 사용해야 한다.

먼저 프로젝트에 `history` 라이브러리를 설치한다.

```js
$ yarn add history
```

그리고 index.js 를 다음과 같이 수정한다.

### index.js

```js

```

## 2. 홈 화면으로 가는 thunk 만들기
