# Module

## 1. Node.js의 모듈

웹 페이지에서 자바스크립트는 script 태그로 로드하며, 여러 자바스크립트 파일을 로드할 경우 하나의 파일로 합쳐져 동일한 유효범위를 갖게 된다. 자바스크립트를 기반으로 한 Server-side 언어인 `Node.js`는 이러한 자바스크립트의 문제점을 해결하고자 모듈 시스템의 사실상 표준인 `CommonJS`를 채택해 `module` 단위로 각 기능을 분리할 수 있도록 했다.   

모듈은 파일과 1대1 대응 관계를 가지며, 하나의 모듈은 **독립적인 실행 영역(Scope)** 를 가지게 된다. 따라서 Client-side 언어인 자바스크립트와는 달리 전역변수의 중복 문제가 발생하지 않는다.   

모듈은 `module.exports` 혹은 `exports` 객체를 통해 정의하고 외부로 내보낸다. 그렇게 내보내진 모듈은 `require` 함수를 사용해 임포트 한다. 



## 2. exports
모듈 안에서 선언한 것들은 해당 모듈 내부에서만 참조 가능하다.   
모듈 안에 선언한 것을 외부로 보내 사용하게 하려면 `exports` 객체를 사용해야 한다.   

모듈을 **파일로 작성**하고, 외부로 보낼 대상을 `exports` 객체의 **프로퍼티 또는 메소드**로 정의한다. 

```javascript
// circle.js
const { PI } = Math; // PI 변수에 Math.PI값을 비구조할당으로 대입

// exports 객체의 메소드로 정의
exports.area = (r) => PI * r * r;
exports.circumference = (r) => 2 * PI * r;
```

circle.js는 독립적인 파일 스코프를 갖는 모듈이다.    
따라서 변수 PI는 circle 모듈에서만 유효한 private 변수이고, `exports` 객체에 메소드로 정의된 area와 circumference는 외부로 보내진다.

```javascript
// app.js
const circle = require('./circle');

console.log(`지름이 4인 원의 면적: ${circle.area(4)}`);
console.log(`지름이 4인 원의 둘레: ${circle.circumference(4)}`);
```
`require` 함수를 사용해 circle 모듈을 임포트한다. 모듈은 항상 js파일로 작성되므로 확장자는 생략할 수 있다.    
이 때 circle 모듈은 **객체로 반환**되므로 `circle.area`와 같은 형식으로 참조한다. 



## 3. module.exports
`exports` 객체는 프로퍼티 또는 메소드를 여러 개 정의할 수 있다.     
하지만 `module.exports`에는 하나의 값(원시타입, 함수, 객체)만을 할당할 수 있다. 

```javascript
// circle.js
const { PI } = Math;

// 하나의 함수를 할당 
module.exports = function (r) {
  return {
    area() { return PI * r * r; },
    circumference() { return 2 * PI * r}
  };
```

```javascript
// app.js
const circle = require('./circle');
const myCircle = circle(4);

console.log(`지름이 4인 원의 면적: ${myCircle.area()}`);
console.log(`지름이 4인 원의 둘레: ${myCircle.circumference()}`);
```

app.js에서 `require` 함수를 통해 circle모듈을 할당받은 circle변수는   
circle모듈(circle.js)에서 `module.exports`에 할당한 값 자체 즉, 객체를 리턴하는 함수가 된다.    


### 3.1 exports 와 module.exports의 차이
`exports`는 `module.exports`의 참조이며, `module.exports`의 alias이다. 

| |모듈 정의 방식| require 함수의 호출 결과 
|---|---|---|
|**exports**|exports 객체에는 값을 할당할 수 없고, 외부로 보낼 대상을 프로퍼티 또는 메소드로 추가한다.| exports 객체에 추가한 프로퍼티와 메소드가 담긴 객체가 전달된다.|
|**module.exports**|module.exports 객체에 하나의 값만을 할당한다.|module.exports 객체에 할당한 값이 전달된다. |


### 3.2 module.exports에 함수 할당하기 
`module.exports`에는 하나의 값 즉, 하나의 함수만 할당할 수 있다. 

```javascript
// foo.js
module.exports = function(a, b) {
  return a + b;
};
```

```javascript
// app.js
const add = require('./foo');

const result = add(1, 2);
console.log(result); // 3
```


### 3.3 module.exports에 객체 할당하기 
모듈에 여러 개의 값을 할당하고 싶다면, 다음과 같이 객체를 사용해 복수의 기능을 하나로 묶어 할당하는 방법을 사용하면 된다. 

```javascript
// foo.js
module.exports = {
  add (v1, v2) { return v1 + v2 },
  minus (v1, v2) { return v1 - v2 }
};
```

```javascript
// app.js
const calc = require('./foo');

const result1 = calc.add(1, 2);
console.log(result1); // 3

const result2 = calc.minus(1, 2);
console.log(result2); // -1
```



## 4. require
`require` 함수에는 파일뿐만 아니라 디렉터리를 지정할 수도 있다. 

```code
project/
├── app.js
└── module/
    ├── index.js
    ├── calc.js
    └── print.js
```

위와 같은 디렉터리 구조가 있을 때, app.js 에서 module 폴더의 index 모듈을 임포트하고 싶다면?!   
`require(./module/index)`처럼 직접 모듈을 명시하지 않고 아래와 같이 호출해도 해당 디렉터리의 index.js를 로드한다. 

```javascript
// app.js
const myModule = require('./module');
```

index 모듈을 임포트함과 동시에 calc 모듈과 print 모듈까지 임포트하고 싶다면?!!    
index.js 내에서 calc.js 와 print.js를 `require` 함수를 이용해 임포트하고, 그걸 `module.exports`를 이용해 외부로 내보내고 있어야 한다.     
그러면 app.js에서 index 모듈을 임포트하는 한 번의 `require` 함수 호출로 calc.js 와 print.js의 모든 기능을 사용할 수 있다. 

```javascript
// module/index.js
module.exports = {
  calc: require('./calc'),
  print: require('./print')
};
```

```javascript
// module/calc.js
module.exports = {
  add (v1, v2) { return v1 + v2 },
  minus (v1, v2) { return v1 - v2 }
};
```

```javascript
// module/print.js
module.exports = {
  sayHello() { console.log('Hi!') }
};
```

```javascript
// app.js
const myModule = require('./module');

const result = myModule.calc.add(1, 2); // module/calc.js의 기능
myModule.print.sayHello(); // module/print.js의 기능
```



## 5. 코어 모듈과 파일 모듈
`Node.js`가 기본적으로 포함하고 있는 모듈을 코어 모듈이라 한다. 코어 모듈을 로딩할 때엔 경로를 명시하지 않아도 된다. 

```javascript
const http = require('http');
```

npm을 통해 설치한 패키지도 경로를 명시하지 않아도 된다. 

```javascript
const mongoose = require('mongoose');
```

그 외에는 전부 파일 모듈이다. 파일 모듈을 로딩할 땐 경로를 명시해야 한다. 

```javascript
const foo = require('./module/calc');
```


> ℹ️ 출처  
> [https://poiemaweb.com](https://poiemaweb.com/nodejs-module)