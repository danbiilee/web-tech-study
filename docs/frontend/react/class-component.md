# 클래스형 컴포넌트

클래스형 컴포넌트는 이제 잘 사용하지 않지만,   
함수형 컴포넌트와 `Hooks`로 못하는 작업들, 옛날에 만들어진 리액트 관련 라이브러리와의 호환,    
`react-native-navigation`과 같은 React-Native 관련 라우터 라이브러리를 사용하기 위해서 클래스형 컴포넌트를 써야하는 일이 종종 있다. 


## 1. 클래스형 컴포넌트 만들기

아래는 기존에 함수형 컴포넌트로 작성했던 `Hello.js`이다.

```js
function Hello({ color, name, isSpecial }) {
  return (
    <div style={{ color }}>
      {isSpecial && <b>*</b>}
      안녕하세요 {name}
    </div>
  );
}

Hello.defaultProps = {
  name: '이름없음'
};
```

이 컴포넌트를 클래스형 컴포넌트로 작성하면 아래와 같다. 

```js
class Hello extends Component {
  render() { // 1
    const { color, name, isSpecial } = this.props; // 2
    return (
      <div style={{ color }}>
        {isSpecial && <b>*</b>}
        안녕하세요 {name}
      </div>
    );
  }
}

Hello.defaultProps = { // 3
  name: '이름없음'
};
```

**1️⃣ `render()` 메서드**
- 클래스형 컴포넌트에서 필수인 메서드이다. 
- 이 메서드 안에서 렌더링하려는 JSX를 반환하면 된다. 

**2️⃣ `props` 조회하기**
- `this.props`로 조회한다. 

**3️⃣ `defaultProps` 설정하기**
- 함수형 컴포넌트에서 설정하는 방법과 동일하다.
- 혹은 다음과 같이 클래스 내부에 `static` 키워드로 선언할 수도 있다.
  ```js
	class Hello extends Component {
		static defaultProps = {
			name: '이름없음'
		};
		render() {
			// ...
		}
	}
	```



## 2. 커스텀 메서드 만들기

함수형 컴포넌트에서 특정 기능을 수행하는 함수를 작성할 땐 컴포넌트 안에 다음과 같이 선언해주면 됐다. 

```js
function Counter () {
	const onIncrease = () => {
		dispatch({ type: 'INCREMENT' });
	};
	return {
		// ...
	}
}
```

클래스형 컴포넌트는 이와 조금 다르다.     
`render` 메서드 내부에서 선언할 수도 있지만, 일반적으로는 **클래스 내부에** 커스텀 메서드로 선언한다. 

아래처럼 클래스 내부에 선언한 함수를 `메서드`라고 한다. 
메서드명은 보통 앞에 `handle`을 붙여 짓는데, 정해진 규칙은 아니다. 

```js
class Counter extends Component {
  handleIncrease() {
    console.log('increase');
	}

	render () {
		<div>
			<h1>0</h1>
			<button onClick={this.handleIncrease}>+1</button>
		</div>
	}
}
```

`state`를 업데이트 할 때는 메서드 안에서 `this.setState()` 라는 함수를 사용한다.      

이 때 `this`는 컴포넌트 인스턴스를 가리켜야 하는데,    
위에서 구현한 `handleIncrease` 메서드에서 `this`를 조회하면 `undefined`가 출력된다.

그 이유는 우리가 만든 메서드들을 각 요소의 이벤트로 등록하는 과정에서 메서드와 컴포넌트 인스턴스의 관계가 끊겨버리기 때문이다.    
이를 해결하기 위한 방법은 총 3개가 있다. 



## 3. 메서드와 컴포넌트 인스턴스 바인딩하기

### 3.1 `constructor()` 사용

첫 번째는 클래스의 생성자 메서드인 `constructor`를 사용하는 것이다.   
`constructor` 안에서 각 메서드에 `bind` 작업을 해주면 된다.

```js
class Counter extends Component {
  constructor(props) {
		super(props);
		this.handleIncrease = this.handleIncrease.bind(this);
	}

	handleIncrease() {
    console.log('increase');
	}

	// ...
}
```

**✨ `contructor` 함수**    
- 파라미터로 `props`를 전달받고, `super(props)`를 호출해줘야 한다. 
  - 해당 클래스가 컴포넌트로서 작동할 수 있도록 `Component`에 구현되어있는 생성자 함수를 먼저 실행해준다. 


 

### 3.2 화살표 함수로 메서드 작성 

두 번째 방법은 커스텀 메서드를 선언할 때 화살표 함수 문법을 사용해 작성하는 것이다. 

```js
class Counter extends Component {
  handleIncrease = () => {
    console.log('increase');
    console.log(this);
	};

	// ...
}
```

이는 클래스에 특정 속성을 선언할 수 있게 해주는 `class-properties`라는 문법을 사용하는 것인데,   
이 문법은 아직 정식 자바스크립트 문법이 아니다.    
단, `CRA`로 만든 프로젝트에는 적용되어 있기 때문에 바로 사용할 수 있다. 

> **CRA(Create React App)**    
> CRA로 만든 프로젝트는 리액트에 필수적인 바벨(Bable)과 웹팩(Webpack)을 
> 복잡한 환경 설정 없이 사용할 수 있고, 바로 빌드하여 실행할 수 있다! 



 