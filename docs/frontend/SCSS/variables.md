# 변수

## 1. 선언
SCSS에서는 반복적으로 사용되는 값을 변수로 지정할 수 있다. 

```css
$변수이름: 속성값;
```
```css
$color-primary: #e96900;
$url-images: "/assets/images/";
.box {
  background: $color-primary url($url-images + "bg.jpg");
}
```

컴파일되면 아래와 같다. 

```css
.box {
  background: #e96900 url("/assets/images/bg.jpg");
}
```

## 2. 유효범위(Variable Scope)
변수는 선언된 블록(`{}`) 내에서만 유효범위를 갖는다. 

변수 `$color`는 `.box1` 의 블록 안에서 선언되었기 때문에, 그 바깥에선 사용할 수 없다. 

```css
.box1 {
  $color: #111;
  background: $color;
}
// Error
.box2 {
  background: $color;
}
```

## 3. 재할당
아래와 같이 변수에 다른 변수를 대입하는 것을 변수의 재할당이라고 한다. 

```css
$red: #0000FF;
$color-danger: $red;
.box {
  color: $color-primary;
}
```

컴파일되면 아래와 같다. 

```css
.box {
  color: #0000FF;
}
```

## 4. !global (전역 설정)
`!global` 플래그를 사용하면 변수의 유효범위를 전역으로 설정할 수 있다. 

```css 
.box1 {
  $color: #111 !global;
  background: $color;
}
.box2 {
  background: $color;
}
```

컴파일되면 아래와 같다.     

```css 
.box1 {
  background: #111;
}
.box2 {
  background: #111;
}
```

`.box1` 의 블록 안에서 `@at root` 키워드를 이용해 `.box2` 요소에 변수 `$color`를 사용할 수도 있다. 

`!global` 플래그를 사용할 경우, 이미 같은 이름의 변수가 존재한다면 값이 덮어씌어질 수 있음을 유의하자.  

```css
$color: #000;
.box1 {
  /* 기존 전역변수 $color 값이 #000에서 #111으로 대체된다. */
  $color: #111 !global; 
  background: $color;
}
.box2 {
  background: $color;
}
.box3 {
  $color: #222;
  background: $color;
}
```

`.box1`, `.box2` 요소의 `background`는 전역 변수인 `$color: #111`로 설정되고,    
`.box3` 요소는 해당 블록 안에서 **재선언된 변수** `$color: #222`로 설정된다.     
(가장 가까운 데에서 마지막으로 선언된 변수의 값이 들어간다.)
 
```css
.box1 {
  background: #111;
}
.box2 {
  background: #111;
}
.box3 {
  background: #222;
}
```


## 5. !default (초기값 설정)
`!default` 플래그는 **값이 할당되지 않은 변수**의 초기값을 설정한다. 

```css
$color-primary: red;
.box {
  $color-primary: blue !default;
  background: $color-primary;
}
```

컴파일되면 아래와 같다. 

```css
.box {
  background: red;
}
```

즉, 기존 변수에 할당된 값이 있을 경우엔 **현재 설정하는 변수의 값은 사용하지 않겠다**는 의미로 쓰인다.    
`!default` 플래그가 있다면 BootStrap 같은 외부 라이브러리를 쓰는 경우에도 기존 코드를 덮어씌우지 않고 사용할 수 있다. 



## 6. #{} (문자 보간)
`#{}` 를 이용해 코드의 중간에 변수 값을 넣을 수 있다. 

> ✨ unquote()   
> Sass의 내장함수로 문자에서 따옴표를 제거한다. 

```css
$family: unquote("Droid+Sans");
@import url("http://fonts.googleapis.com/css?family=#{$family}");
```

컴파일되면 아래와 같다. 

```css
@import url("http://fonts.googleapis.com/css?family=Droid+Sans");
```