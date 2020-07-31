# input 상태 관리하기 


## 1. 하나의 input 상태 관리하기

아래는 `input` 태그에 입력하면 `text`라는 `state`에 값이 저장되고,    
초기화 버튼을 누르면 값이 지워지는 예제이다. 

```js
function InputSample() {
  const [text, setText] = useState(''); 

  const onChange = (e) => { // 1
    setText(e.target.value);
  };

  const onReset = () => {
    setText('');
  };

  return (
    <div>
      <input onChange={onChange} value={text}  /> {/* 2 */}
      <button onClick={onReset}>초기화</button>
    </div>
  );
}
```

**1️⃣ 이벤트 객체로 현재 입력값 조회하기**
- `input` 태그의 `value` 값이 바뀔 때마다 `onChange`함수가 호출된다.
- 이 때 이벤트 객체 `e`를 파라미터로 받아와 사용한다. 
- `e.target`은 이벤트가 발생한 DOM인 `input` 태그를 가리키게 되므로, `e.target.value`로 현재 `input` 태그에 입력한 값을 조회하고 업데이트할 수 있다.

**2️⃣ input의 value 값 업데이트**
- `input` 태그의 상태를 관리할 땐 `input` 태그의 `value`값도 설정해준다. 
- 그렇게 해야 상태가 바뀌었을 때 `input` 태그의 `value`값도 함께 업데이트된다. 




## 2. 여러 개의 input 상태 관리하기

상태를 관리해야 할 `input` 태그가 여러 개일 땐,    
하나의 `onChange` 이벤트 핸들러를 사용하되 `input` 태그에 `name` 속성을 설정하여 그 값을 참조한다.   

그리고 `useState` 함수에서는 **객체 형태의 상태**를 관리해준다. 

```js
function InputSample() {
  const [inputs, setInputs] = useState({ // 1
    name: '',
    id: ''
  });

  const { name, id } = inputs; // 2 

  const onChange = (e) => {
    const { value, name } = e.target; // 3 
    setInputs({ // 4
      ...inputs, 
      [name]: value 
    });
  };

  return (
    <div>
      <input name="name" onChange={onChange} value={name} />
      <input name="id" onChange={onChange} value={id}/>
    </div>
  );
}
```

**1️⃣ 객체를 이용해 기본값 설정**
- `input` 태그의 값을 여러 개 저장하기 위해 객체로 기본값을 설정한다. 

**2️⃣ state의 값 추출**
- `state`는 객체이므로 `{ key, key } = object` 형태의 구조 분해 할당을 이용해 값을 추출한다. 

**3️⃣ e.target의 값 추출**
- 여기서 `e.target`은 `value` 값의 변화가 발생한 `input`태그이므로, 해당 DOM에서 `name` 속성의 값과 `value`값을 추출한다.

**4️⃣ state 업데이트**
> ✨ 스프레드 문법 `...`   
> : 객체 혹은 배열의 내용을 모두 펼쳐서 복사해준다. 

- `스프레드 문법`을 이용해 기존의 `state` 객체를 복사한 뒤 객체의 `key`로 `name`을 가진 값을 그 `key`의 `value`로 설정한다.
  - `name=id`인 `input` 태그의 값이 `''`에서 `'id1234'`로 변경되는 경우
    ```js
    setInputs({
      // ...로 된 코드는 실제로 아래와 같이 컴파일된다
      name: '',
      id: '',
      // 최종적으로 id는 마지막에 선언한 값을 갖게 된다 
      id: 'id1234', 
    })
    ```
- 위와 같은 작업을 **불변성을 지킨다** 라고 한다.



## 3. 불변성 지키기 

리액트에서는 불변성을 지켜야만 컴포넌트에서 상태가 업데이트 됐음을 감지할 수 있고,  
그에 따라 필요한 렌더링이 진행된다.

따라서 기존 객체를 직접 수정하지 않고,  
1️⃣ 새로운 객체를 만들고 2️⃣ `...` 문법을 이용해 기존 객체를 복사하고  3️⃣ 변경된 값만 업데이트    
하는 방식으로 작업한다. 

먄약, `inputs[name] = value` 이런 식으로 기존 상태를 직접 수정하게 되면,   
값을 바꿔도 리렌더링되지 않는다. 

그리고 불변성을 지켜야만 컴포넌트 최적화를 제대로 할 수 있다.