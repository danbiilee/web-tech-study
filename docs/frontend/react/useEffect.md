# useEffect를 사용해 특정 작업 설정하기 



## 1. useEffect

`useEffect`라는 `Hook`을 사용해 컴포넌트가 마운트 됐을 때(처음 나타났을 때), 언마운트 됐을 때(사라질 때), 업데이트 될 때(리렌더링될 때) 특정 작업을 처리할 수 있다. 

**마운트 시 하는 작업**
- `props`로 전달받은 값을 해당 컴포넌트의 로컬 `state`로 설정
- 외부 API 요청 (REST API 등)
- 라이브러리 사용 (D3, Video.js 등)
- `setInterval`을 통한 반복작업 혹은 `setTimeout`을 통한 작업 예약

**언마운트 시 하는 작업**
- `setInterval`, `setTimeout`을 사용해 등록한 작업 clear하기 (`clearInterval`, `clearTimeout`)
- 라이브러리 인스턴스 제거하기 


> **✨ 컴포넌트가 업데이트되는 시점**
> - `props`가 변할 때
> - `state`가 변할 때
> - 부모 컴포넌트가 렌더링될 때 



```js
// UserList.js
function User({ user, onRemove, onToggle }) {
  useEffect(() => {
    console.log('컴포넌트가 화면에 나타남');
    return () => {
      console.log('컴포넌트가 화면에서 사라짐');
    };
	}, []);
	return (
		// ...
	);
}
```

**파라미터**
- 첫 번째 파라미터: 함수
- 두 번째 파라미터: 의존값이 들어있는 배열(`deps`)
  - 빈 `deps`를 전달하는 경우 컴포넌트가 처음 나타날 때(마운트)만 `useEffect`의 첫 번째 파라미터에 전달한 함수가 실행된다.

**`cleanup` 함수**
  - `useEffect`가 반환하는 함수로 뒷정리를 담당한다.
  - 빈 `deps`를 전달하는 경우 컴포넌트가 사라질 때 `cleanup` 함수가 호출된다. 
  



## 2. `deps`에 특정 값 넣기

`useEffect` 안에서 사용하는 `state`나 `props`가 있다면 `deps`에 넣어주는 것이 규칙이다.     
그렇지 않다면, `useEffect`에 등록한(첫 번째 파라미터에 전달한) 함수가 실행될 때 최신 `state`나 `props`를 가리키지 않게 된다. 


`deps`에 특정 값이 있다면, 

- 컴포넌트가 처음 마운트 될 때
- `deps`에 지정한 값이 바뀌기 직전에     
- `deps`에 지정한 값이 바뀔 때
- 컴포넌트가 언마운트 될 때 
  
`useEffect`에 등록한 함수가 호출된다. 


```js
function User({ user, onRemove, onToggle }) {
  useEffect(() => {
    console.log('user 값이 설정됨');
    console.log(user);
    return () => {
      console.log('user 가 바뀌기 전..');
      console.log(user);
    };
	}, [user]);
	return (
		// ...
	);
}
```



## 3. `deps`파라미터를 생략하기
 
빈 `deps`를 전달하는 게 아니라 아예 `deps`파라미터를 생략한다면,     
컴포넌트가 업데이트되어 리렌더링 될 때마다 등록한 함수가 호출된다.

```js
function User({ user, onRemove, onToggle }) {
  useEffect(() => {
    console.log(user);
	});
	return (
		// ...
	);
}
```