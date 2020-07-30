# useState를 통해 상태 관리하기

사용자 인터랙션에 따라 컴포넌트에서 보여줘야 하는 내용이 바뀌어야 할 때가 있다.   
컴포넌트에선 그러한 동적인 값을 `상태(state)`라고 한다.


리액트 16.8에서 `Hooks` 라는 기능이 도입되면서 `useState` 함수를 사용해 함수형 컴포넌트에서도 `상태`를 관리할 수 있게 되었다.   
(`useState` 함수가 리액트의 `Hooks` 중 하나!)



## 1. 상태 업데이트 

아래는 버튼을 누르면 숫자가 바뀌는 `Counter` 컴포넌트 예제이다. 

```js
// Counter.js
function Counter() {
	const [ number, setNumber ] = useState(0); // 1

	const onIncrease = () => {
		setNumber(number + 1); // 2
	};
	const onDecrease = () => {
		setNumber(number - 1);
	};

	return (
		<div>
			<h1>{number}</h1>
			<button onClick={onIncrease}>+1</button> // 3
			<button onClick={onDecrease}>-1</button>
		</div>
	);
}
```

**1️⃣ `useState(defaultValue)`**
- 상태의 기본값을 파라미터로 넣어 호출한다. 여기선 상태의 기본값으로 0을 설정했다. 
- `useState` 함수는 배열을 반환한다.
  - 첫 번째 요소: 현재 상태
  - 두 번째 요소: Setter 함수
- `구조 분해 할당` 을 통해 반환된 배열의 각 요소를 추출한다.

**2️⃣ 상태값 관리**  
`Setter` 함수에 업데이트하고 싶은 새로운 값을 파라미터로 전달해 `number` 값을 변경한다.


**3️⃣ 요소에 이벤트 설정하기**
- `on이벤트이름={함수명}`   
- (❗) 함수명을 넣어야지, `onClick={onIncrease()}` 이렇게 함수를 호출하면 안된다. 렌더링될 때 함수가 실행되기 때문! 


```js
// 구조 분해 할당 하지 않은 경우
const numberState = useState(0);
const number = numberState[0];
const setNumber = numberState[1];
```



## 2. 함수형 업데이트

상태 값을 변경할 때 위의 방법이 아닌,   
아래처럼 기존 값을 어떻게 업데이트 할 지에 대한 **함수를 등록**하는 방식으로도 가능하다. 

```js
const onIncrease = () => {
	setNumber(prevValue => prevValue + 1);
};
const onDecrease = () => {
	setNumber(prevValue => prevValue - 1);
};
```

`Setter` 함수에 변경될 그 다음 상태값을 파라미터로 전달하는 게 아니라,   
**이전 값을 업데이트하는 함수**를 전달했다. 

함수형 업데이트는 주로 컴포넌트를 최적화 할 때 사용한다. 
