# useRef로 컴포넌트 안의 변수 만들기


`useRef`는 컴포넌트에서 특정 DOM을 선택하는 용도 외에도,     
컴포넌트 안에서 조회 및 수정이 가능한 변수를 관리하는 용도로 사용할 수 있다.   

컴포넌트 내부에서 `let` 키워드로 선언한 변수는 컴포넌트가 리렌더링될 때마다 값이 초기화된다.     
따라서 계속 유지하고 싶은 값을 관리하기 위해선 useState를 사용해야하는데,     
useState는 state가 변경될 때마다 컴포넌트가 리렌더링된다는 특징이 있다. 

그러므로 리렌더링 할 필요 없이 값을 관리해야할 때 `useRef`를 사용해 변수를 만드는 것이다. 


**useRef로 관리하는 변수 🆚 state**
|| useRef 변수 | state |
|---|---|---|
| 컴포넌트 리렌더링 | 값이 바뀌어도 리렌더링 되지 않음 | 상태가 변할 때마다 리렌더링 됨 |
| 조회 시점 | 값을 변경 후 바로 조회 가능 | 리렌더링된 이후 업데이트된 상태 조회 가능 |


`useRef` 로 관리하는 변수를 사용해 다음과 같은 값을 관리할 수 있다.
- `setTimeout`, `setInterval`을 통해 만들어진 `id`
- 외부 라이브러리를 사용해 생성된 인스턴스
- scroll 위치



아래는 배열에 새 항목을 추가할 때,    
`useRef`를 이용해 새 항목에서 사용할 고유 id를 관리하는 예제이다.    

> 이후 배열에 새 항목을 추가하기 위해,    
> 배열을 App 컴포넌트에서 선언 후 UserList에게 `props`로 내려주는 방식으로 변경했다.


```js
// App.js
function App() {
  const users = [
    {
      id: 1,
      name: 'danbi',
      email: 'danbi@gmail.com'
    },
    {
      id: 2,
      name: 'ash',
      email: 'ash@example.com'
    },
    {
      id: 3,
      name: 'sik',
      email: 'sik@example.com'
    }
  ];

  const nextId = useRef(4); // 1 
  const onCreate = () => {
    // 배열에 새 항목을 추가하는 함수
    // ...
    nextId.current += 1; // 2
  };

  return <UserList users={users} />;
}
```

**1️⃣ useRef()를 사용해 변수 만들기**
- `useRef()` 함수의 파라미터에 현재 users 배열의 마지막 항목이 가진 id값에 1을 더한 값을 전달하여 기본값을 설정한다. 
- 4라는 기본값이 nextId의 `.current`에 담기게 된다.
  
**2️⃣ 변수 관리하기**
- `nextId.current`로 값을 조회, 수정한다. 
