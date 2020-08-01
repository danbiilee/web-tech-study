# useRef로 특정 DOM 선택하기

리액트를 사용하는 프로젝트에서도 아래와 같이 DOM을 직접 선택해야 하는 상황이 있다.

- 특정 요소의 크기를 알아야 할 때
- 스크롤바의 위치를 알아야 할 때
- 특정 요소에 포커스를 설정해야 할 때
- `Video.js`, `JWPlayer`와 같은 HTML5 Video 관련 라이브러리를 사용해야 할 때
- `D3`, `chart.js`와 같은 그래프 관련 라이브러리를 사용해야 할 때 
  
그럴 땐, `ref`를 사용한다.    
함수형 컴포넌트에서 `ref`를 사용할 땐 `useRef`라는 Hook 함수를 사용하고,   
클래스형 컴포넌트에서는 콜백 함수를 사용하거나 `React.createRef`라는 함수를 사용한다. 


아래는 초기화 버튼을 클릭했을 때, 이름 `input` 태그에 포커스가 잡히도록 하는 예제이다.   
(`onChnage` 함수의 코드는 생략했다.)

```js
function InputSample() {
  const [inputs, setInputs] = useState({ 
    name: '',
    id: ''
  });
  const { name, id } = inputs;

  const nameInput = useRef(); // 1

  const onReset = () => {
    setInputs({
      name: '',
      nickname: ''
    });
    nameInput.current.focus(); // 3
  };

  return (
    <div>
      <input
        name="name"
        placeholder="이름"
        onChange={onChange}
        value={name}
        ref={nameInput} {/* 2 */}
      />
      <button onClick={onReset}>초기화</button>
    </div>
  );
}
```

**1️⃣ Ref 객체 만들기**   
- `useRef()` 함수를 사용해 `nameInput`이라는 `Ref 객체`를 만든다. 

**2️⃣ Ref 객체를 DOM에 설정하기**    
- 선택하고 싶은 `input` 태그에 `ref` 라는 속성으로 `nameInput` 객체를 설정한다. 
- 그러면 `nameInput` 객체의 `current` 속성에 위의 `input` 태그가 담기게 된다. 

**3️⃣ Ref 객체에 담겨있는 DOM 선택하기**
- `nameInput.current`로 `name=name`인 `input` 태그를 선택할 수 있다. 