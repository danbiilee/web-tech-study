# 커스텀 Hooks 만들기

컴포넌트를 만들 때 반복되는 로직을 `커스텀 Hooks`로 만들어 재사용할 수 있다.

예를 들어 `input` 을 관리하는 코드를 `커스텀 Hooks`로 만든다면,   
`src` 디렉터리에 `hooks` 디렉터리를 추가하고, 그 안에 `useInputs.js` 파일을 생성한다.   
*(보통 `커스텀 Hooks`를 만들 때엔 `use`라는 키워드로 시작하는 파일을 만든다)*    

그리고 그 안에서 `useState`, `useEffect`, `useReducer` 등 다른 `Hooks`를 사용해 원하는 기능을 구현하고,    
컴포넌트에서 사용하고 싶은 값들을 **반환**해주면 된다. 


아래는 `input`을 관리하는 `커스텀 Hooks`이다.

```js
// useInputs.js
function useInputs(initialForm) { // 1
	const [ form, setForm ] = useState(initialForm); 

	const onChange = useCallback(e => {
		const { name, value } = e.target;
		setForm(form => ({ ...form, [name]: value }));
	}, []);

	const reset = useCallback(() => setForm(initialForm), [initialForm]); // 2

	return [ form, onChange, reset ]; // 3
}

export default useInputs;
```

**1️⃣ 파라미터로 초기화 값 전달받기**    
- `useState` 함수의 파라미터로 전달할 초기값을 `useInputs` 함수의 매개변수로 전달받는다. 

**2️⃣ `useCallback`을 사용해 기능 구현하기**    
- `onChange`의 경우 setter에서 함수형 업데이트를 사용해서 참조하는 값이 없으므로, `useCallback`의 두 번째 파라미터로 빈 `deps`를 전달한다. 
- `reset`의 경우 setter에서 `initialForm`을 참조했으므로, `useCallback`의 `deps` 파라미터에 해당 값을 전달한다. 

**3️⃣ 필요한 값 반환하기**    
- 다른 컴포넌트에서 사용해야 할 값들을 함수의 반환할 수 있게 한다.  


이렇게 만든 `useInputs 커스텀 Hook`을 `App.js`에서 사용해보자.    
기존에 `useReducer`에서 사용하던 `inputs` 값을 없애고, 이와 관련된 값을 `useInputs`로 대체하면 된다. 

```js
// App.js
import useInputs from './hooks/useInputs';

const initialState = { // 1
	users: [
    {
      id: 1,
      username: 'danbi',
      email: 'danbi@gmail.com',
      active: true
    },
    {
      id: 2,
      username: 'ash-island',
      email: 'ash@example.com',
      active: false
    },
    {
      id: 3,
      username: 'sik-k',
      email: 'sikk@example.com',
      active: false
    }
  ]
};

function reducer(state, action) { // 2
	switch(action.type) {
		case 'CREATE_USER':
			return {
				users: state.users.concat(action.user),
			};
		case 'TOGGLE_USER':
			return {
				...state,
				users: state.users.map(user => (
					action.id === user.id ? { ...user, active: !user.active } : user
				))
			};
		case 'REMOVE_USER':
			return {
				...state,
				users: state.users.filter(user => action.id !== user.id)
			};
		default:
			return state;
	}
}

function App() {
	const [{ username, email }, onChange, reset] = useInputs({ // 3
    username: '',
    email: ''
	});
	// ... 
	
	return (
		// ... 
	);
}
```

**1️⃣ initialState에서 관리하던 inputs 값 삭제하기**

**2️⃣ `reducer` 함수에서 관리하던 `type: CHANGE_INPUT` 삭제하기**

**3️⃣ `커스텀 Hook useInput` 사용하기**
- `useInputs` 함수의 파라미터로 초기값을 전달한다.
- `useInputs`가 반환하는 배열의 항목들을 구조 분해 할당을 통해 추출한다.
	```js
	// useInputs.js
	function useInputs(initialForm) {
		// ...
		return [form, onChange, reset];
	}
	```
  - 반환하는 배열의 첫 번째 항목인 `form`은 username과 email을 key로 갖고 있는 객체 형태이므로, 다시 구조 분해 할당을 통해 값을 추출할 수 있다. 