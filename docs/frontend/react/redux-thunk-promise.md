# redux-thunk로 프로미스 다루기

> ✨ 프로미스 공부하기
>
> - https://learnjs.vlpt.us/async/
> - https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise

## 1. 가짜 API 함수 만들기

먼저 Promise를 사용해 데이터를 반환하는 가짜 API 함수를 만들어보자.

src 디렉터리에 api 디렉터리를 만들고, posts.js 파일을 생성 후 다음과 같이 코드를 작성한다.

### api/posts.js

```js
// n 밀리세컨드 동안 기다리는 프로미스를 만들어주는 함수
const sleep = (n) => new Promise((resolve) => setTimeout(resolve, n));

// 가짜 포스트 목록 데이터
const posts = [
  {
    id: 1,
    title: '리덕스 미들웨어를 배워봅시다',
    body: '리덕스 미들웨어를 직접 만들어보면 이해하기 쉽죠.',
  },
  {
    id: 2,
    title: 'redux-thunk를 사용해봅시다',
    body: 'redux-thunk를 사용해서 비동기 작업을 처리해봅시다!',
  },
  {
    id: 3,
    title: 'redux-saga도 사용해봅시다',
    body:
      '나중엔 redux-saga를 사용해서 비동기 작업을 처리하는 방법도 배워볼 거예요.',
  },
];

// 포스트 목록을 가져오는 비동기 함수
export const getPosts = async () => {
  await sleep(500); // 0.5초 기다리기
  return posts;
};

// id로 포스트를 조회하는 비동기 함수
export const getPostById = async (id) => {
  await sleep(500); // 0.5초 기다리기
  return posts.find((post) => post.id === id);
};
```

## 2. 리덕스 모듈 만들기

이제 posts 라는 리덕스 모듈을 만들어보겠다.

프로미스를 다루는 리덕스 모듈을 만들 땐 다음과 같은 사항들을 고려해야 한다.

1. 프로미스가 시작, 성공, 실패했을 때 다른 액션을 디스패치 해야 한다.
2. 각 프로미스마다 thunk 함수를 만들어야 한다.
3. 리듀서에서 액션에 따라 로딩중, 결과, 에러 상태를 변경해야 한다.

## 3. 리덕스 모듈 리팩토링하기

## 4. 포스트 목록 구현하기

## 5. 리액트 라우터 적용하기

## 6. 포스트 조회하기

## 7. 라우터 설정하기
