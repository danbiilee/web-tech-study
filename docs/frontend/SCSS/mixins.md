# Mixin(재활용)

Sass의 `Mixin`은 StyleSheet 전체에서 **재사용할 CSS 선언 그룹**을 정의하는 기능이다.   
SCSS에서는 `@mixin`으로 선언하고, `@include`로 포함하는 방식으로 사용한다! 



## 1. @mixin

`@mixin` 지시어를 이용해 스타일을 정의한다. 

```css
@mixin 믹스인이름 {
	스타일;
}
```

Mixin은 선택자를 포함할 수 있고, `&`를 이용해 상위 요소를 참조할 수도 있다. 

```css
@mixin large-text {
	font: {
		size: 20px;
		weight: bold;
	}
	color: orange;

	&::after {
		content: "!!";
	}

	span.icon {
		background: url("/images/icon.png");
	}
}
```



## 2. @include

`@include` 지시어로 선언된 Mixin을 포함, 사용한다.   

```css
@include 믹스인이름;
```

아래는 위에 선언한 `large-text` Mixin을 사용하는 예제이다. 

```css
.box {
	@include large-text;
}
```

컴파일되면 아래와 같다. 

```css
.box {
	font-size: 20px;
	font-weight: bold;
	color: orange;
}

.box::after {
	content: "!!";
}

.box span.icon {
	background: url("/images/icon.png");
}
```



## 인수(Arguments)

Mixin은 함수(Functions)처럼 인수를 가질 수 있다. 

```css
@mixin 믹스인이름($매개변수) {
	스타일;
}

@include 믹스인이름(인수);
```

> 🌟 인수와 매개변수
> - `매개변수(Parameters)`: 변수의 한 종류로 전달된 값을 받아 그 안에서 사용할 수 있도록, 특정 데이터 중 하나를 가리키는 데 사용된다.
> 	-	변수를 뜻하는 `$` 지시어가 붙어있다. 
> - `인수(Arguments)`: 인자라고도 하며, 전달되는 값을 의미한다. 


```css
@mixin size($w, $h) {
	width: $w;
	height: $h;
}

.box {
	@include size(100px, 100px);
}
```

컴파일되면 아래와 같다.

```css
.box {
	width: 100px;
	height: 100px;
}
```


### 2.1 기본값 설정

인수는 기본값을 가질 수 있다.   
`@include` 단계에서 별도의 인수가 전달되지 않으면 기본값이 사용된다. 

```css
@mixin size($w: 100px, $h: 100px) {
	width: $w;
	height: $h;
}

.box1 {
	@include size;
}

.box2 {
	@include size(300px);
}
```

컴파일되면 아래와 같다.    
`box1` 요소에서는 `size` Mixin에 인수를 전달하지 않아 기본값이 사용됐고,   
`box2` 요소에서는 매개변수를 하나만 전달했으므로, 첫 번째 매개변수인 `$w`에 300px이 전달되어 컴파일됐다.  

즉, 인수는 매개변수가 선언된 순대로 전달된다! 

```css
.box1 {
	width: 100px;
	height: 100px;
}

.box2 {
	width: 300px;
	height: 100px;
}
```


### 2.2 키워드 인수 
`@include` 단계에서 전달할 인수를 입력할 때, 내가 값을 전달하고자 하는 매개변수를 명시적으로 **키워드(변수명)** 를 이용해 작성할 수 있다. 

단, 인수를 작성하지 않은 경우에도 인수가 적용될 수 있도록 기본값을 설정해주는 것이 좋다. 


```css
@mixin size($w: 100px, $h: 100px) {
	width: $w;
	height: $h;
}

.box2 {
	@include size($h: 300px);
}
```

위에서와는 달리 `$h: `라고 키워드로 전달하려는 값의 매개변수를 명시해줬기 때문에,   
`box2` 요소의 `width`가 아닌 `height`에 값이 전달되어 아래와 같이 컴파일된다. 

```css
.box2 {
	width: 100px;
	height: 300px;
}
```

매개변수와 인수가 많아질 경우, 가독성을 위해 아래 예제처럼 줄바꿈을 해준다. 

```css
@mixin position(
  $p: absolute,
  $t: null,
  $b: null,
  $l: null,
  $r: null
) {
  position: $p;
  top: $t;
  bottom: $b;
  left: $l;
  right: $r;
}

.absolute {
	@include position($b: 10px, $r: 20px);
}

.fixed {
	@include position(
		fixed,
		$t: 30px,
		$r: 40px
	);
}
```

컴파일되면 아래와 같다. 

```css
.absolute {
  position: absolute;
  bottom: 10px;
  right: 20px;
}
.fixed {
  position: fixed;
  top: 30px;
  right: 40px;
}
```


### 2.3 가변 인수(Variable Arguments) 

**1️⃣ 매개변수 뒤에 `...`을 붙이는 경우**  

입력할 인수의 개수가 불확실한 경우, 매개변수 뒤에 `...`을 붙여 가변 인수를 사용한다. 

```css
/* 3번째 매개변수 $bg는 인수의 개수에 상관없이 값을 전달받는다. */
@mixin var ($w, $h, $bg...) {
	width: $w;
	heigth: $h;
	background: $bg;
}

div {
	/* 위의 Mixin 설정에 맞게 인수를 순서대로 전달하다가, 
	3번째 이후부터는 개수에 상관없이 전달 가능 */
	@include var (
		100px,
		200px,
		url("/images/a.png") no-repeat 10px 20px, 
		url("/images/b.png") no-repeat,
		url("/images/c.png")
	)
}
```

컴파일되면 아래와 같다. 

```css
div {
  width: 100px;
  height: 200px;
  background: url("/images/a.png") no-repeat 10px 20px,
              url("/images/b.png") no-repeat,
              url("/images/c.png");
}
```

**2️⃣ 전달할 인수 뒤에 `...`을 붙이는 경우** 

```css
@mixin font(
  $style: normal,
  $weight: normal,
  $size: 16px,
  $family: sans-serif
) {
  font: {
    style: $style;
    weight: $weight;
    size: $size;
    family: $family;
  }
}

div {
	/* 
		변수 font-values에 값을 쉼표(,)로 구분하여 List 형태로 저장,
		인수로 전달 시 ...에 의해 값이 풀어지고, 
		매개변수가 선언된 순서대로 값이 전달되는 방식. 
	*/
  $font-values: italic, bold, 16px, sans-serif;
  @include font($font-values...);
}

span {
	/* 
		변수 font-values에 값을 Map 형태로 저장,
		인수로 전달 시 ...에 의해 값이 풀어지고, 
		키워드 인수 방식으로 명시해놓은 매개변수에만 값이 전달되는 방식. 
	*/
  $font-values: (style: italic, size: 22px);
  @include font($font-values...);
}

a	{
	/*
	 별도의 변수 작성 없이 바로 인수로 필요한 값만 전달하는 방식 */
	@include font((weight: 900, family: monospace)...);
}
```

컴파일되면 아래와 같다. 

```css 
div {
  font-style: italic;
  font-weight: bold;
  font-size: 16px;
  font-family: sans-serif;
}
span {
  font-style: italic;
  font-weight: normal;
  font-size: 22px;
  font-family: sans-serif;
}
a {
  font-style: normal;
  font-weight: 900;
  font-size: 16px;
  font-family: monospace;
}
```