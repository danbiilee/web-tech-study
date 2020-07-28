# 조건


## 1. if() 함수

`if()` 함수는 자바스크립트의 `조건부 삼항 연산자`와 비슷하다.

조건의 값이 `true`이면 `표현식1`을,    
조건의 값이 `false`이면 `표현식2`를 반환한다.

```css
if(조건, 표현식1, 표현식2)
```

```css
$px: 555px;
div {
  width: if($px > 300px, $px, null);
  height: if($px > 800px, $px, null);
}
```

컴파일되면 아래와 같다. 

(❗) SCSS의 데이터 타입 중 `null`은 값이 없다는 뜻이 아니라   
그 속성을 사용하지 않겠다 즉, 해당 속성이 컴파일되지 않음을 뜻한다. 

```css
div {
  width: 555px;
}
```



## 2. @if 지시어

`@if` 지시어는 자바스크립트의 `if문` 과 유사하여 조건에 따른 분기 처리가 가능하다.   
`@else`, `@else if`와 함께 사용해 좀 더 복잡한 조건문을 작성할 수 있다. 

```css
/* @if */
@if (조건) {
  /* 조건이 참일 때 구문 */
}

/* @if @else */
@if (조건) {
  /* 조건이 참일 때 구문 */
} @else {
  /* 조건이 거짓일 때 구문 */
}

/* @if @else if */
@if (조건1) {
  /* 조건1이 참일 때 구문 */
} @else if (조건2) {
  /* 조건2가 참일 때 구문 */
} @else {
  /* 모두 거짓일 때 구문 */
}
```

조건의 `()`는 아래와 같이 생략 가능하다. 

```css
$bg: true;
div {
  @if bg {
    background: url("/images/a.jpg");
  }
}
```

또 조건에는 논리 연산자 `and`, `or`, `not` 을 사용할 수 있다. 

```css
@function limitSize($size) {
	@if $size >= 0 and $size <= 200px {
		@return 200px;
	} @else {
		@return 800px;
	}
}

div {
	/* limitSize는 함수이므로 바로 호출한다 */
	width: limitSize(180px);
	height: limitSize(350px);
}
```

컴파일되면 아래와 같다. 

```css
div {
	width: 200px;
	height: 800px;
}
```


아래는 `position` 속성을 이용해 가운데 정렬을 하기 위한 Mixin이다.   

- `position: relative` 와 `position: static`은 가운데 정렬을 할 수 없으므로 `@if` 조건문에서 `or not` 연산자를 이용해 걸러준다.
- 가운데 정렬을 하기 위해선 `width`와 `height`값이 필요하다. `if()` 함수와 내장함수를 이용해 단위여부를 파악한다. 
  - ✨ 내장함수 `unitless()`: 단위가 없으면 `true`, 있으면 `false`를 리턴한다.
  - 단위가 없으면 문자 보간(`#{}`)으로 매개변수 뒤에 px을 더해준다. 


```css
@mixin pCenter($w, $h, $p: absolute) {
	@if
		$p == absolute
		or $p == fixed
		or not $p == relative
		or not $p == static
	{
		width: if(unitless($w), #{$w}px, $w);
		height: if(unitless($h), #{$h}px, $h);
		position: $p;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		margin: auto;
	}
}

.box1 {
		/* pCenter는 Mixin이므로 @include를 통해 사용한다 */
  @include pCenter(10px, 20px);
}
.box2 {
  @include pCenter(50, 50, fixed);
}
.box3 {
  @include pCenter(100, 200, relative);
}
```

컴파일되면 아래와 같다.  
`.box3`은 `$p == relative`이므로 `@if` 조건문에 의해 속성들이 컴파일되지 않는다. 
 
```css
.box1 {
	width: 10px;
	height: 20px;
	position: absolute;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;
	margin: auto;
}

.box2 {
	width: 50px;
	height: 50px;
	position: fixed;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;
	margin: auto;
}
```
