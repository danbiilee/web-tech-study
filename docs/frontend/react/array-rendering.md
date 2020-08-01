# 배열 렌더링하기 


아래와 같은 배열을 컴포넌트에 렌더링 하려면 어떻게 해야 할까? 

```js
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
```

배열의 요소를 인덱스를 통해 일일이 선택해 렌더링하는 것보단,     
자바스크립트 내장함수인 `map()`을 이용해 배열을 렌더링하는 컴포넌트를 만들어 코드의 재사용성을 높이는 것이 좋다. 

> (❗) 한 파일에 여러 개의 컴포넌트를 선언해도 괜찮다! 



## 1. map() 을 이용한 렌더링 

리액트에서 동적인 배열을 렌더링해야 할 땐 `map()` 함수를 사용한다.     
`map()`함수로 일반 데이터 배열을 리액트 요소로 이루어진 배열로 변환해주면 된다.

> **📌 `map()` 함수**    
> - 배열을 순회하면서 각 요소를 특정 값으로 변환하여 새로운 배열로 반환한다. 
> - 반환된 배열의 `length`는 기존 배열의 `length`와 동일하다.
> - 기존 배열은 변경되지 않는다. 

```js
// UserList.js

// User Component
function User({ user }) { // 2
  return (
    <div>
      <b>{user.name}</b> <span>({user.email})</span>
    </div>
  )
} 

// UserList Component
function UserList() {
  const users = [ ... ]; // 위의 배열과 동일

  return (
    <div>
      {users.map(user => (
        <User user={user} /> // 1 
      ))}
    </div>
  );
}
```

**1️⃣ 배열의 각 요소를 props로 내려주기** 
- UserList 컴포넌트에서 `map()`함수를 이용해 배열을 순회하면서, 각 요소들을 자식 User컴포넌트의 `props`로 내려준다. 
  
**2️⃣ 재사용 가능한 컴포넌트**
- 부모 컴포넌트인 UserList가 `props`로 내려준 `user`를 파라미터로 받아 상세 내용을 렌더링한다.  
- UserList 컴포넌트 안에서 바로 코드를 작성하지 않고, 컴포넌트로 따로 분리하여 재사용성을 높였다.



## 2. 고유 key 

리액트에서 배열을 렌더링할 땐 `key`라는 `props`를 설정해야 한다.      
그래야 배열이 업데이트 될 때 수정되지 않는 기존 값은 그대로 둔 채, 원하는 곳에 내용을 삽입하거나 삭제할 수 있다.    

이 때 `key` 값은 배열의 각 요소들마다 갖고 있는 **고유값**이어야 한다.    

> ⚠ `key`를 설정하지 않을 경우       
> Warning: Each child in a list should have a unique "key" prop.     
> 이라는 콘솔 에러가 뜬다. 

```js
return (
  <div>
    {users.map(user => (
      <User user={user} key={user.id} />
    ))}
  </div>
);
```

만약 배열의 요소들이 고유한 값을 가지고 있지 않다면,       
`map()` 함수를 사용할 때 콜백 함수의 두 번째 파라미터 `index`를 `key`로 사용하면 된다. 

```js
return (
  <div>
    {users.map((user, index) => (
      <User user={user} key={user.index} />
    ))}
  </div>
);
```

