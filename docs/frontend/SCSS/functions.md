# 함수(Functions)

SCSS에서는 `@function` 지시어를 이용해 Mixin과 유사한 `함수`를 정의하여 사용할 수 있다.  



## 1. 함수와 Mixin의 차이

### 1.1 반환값
Mixin은 특정 스타일(속성: 값)을 반환하는 반면,    
함수는 보통 연산된(Computed) 값을 `@return` 지시어를 통해 반환한다. 

```css
/* Mixins */
@mixin 믹스인이름($매개변수) {
  스타일;
}

/* Functions */
@function 함수이름($매개변수) {
  @return 값
}
```


### 1.2 사용 방법 
Mixin은 `@include` 지시어를 이용해 사용하는 반면, 함수는 함수이름으로 바로 호출하여 사용한다.     
또 Mixin은 전달할 인수가 없을 경우 `()` 없이 믹스인이름으로만 사용하지만, 함수는 인수가 없어도 `()`를 필수로 작성해야 한다. 

```css
/* Mixins */
@include 믹스인이름(인수);
@include 믹스인이름;

/* Functions */
함수이름(인수)
함수이름()
```

아래는 함수를 사용하는 예제이다.   
함수의 매개변수에 기본값을 설정하면 아래와 같이 반복되는 인수를 생략하여 코드를 간단히 할 수도 있다. 

```css
@function columns($number: 1, $colums: 12, $width: 1200px) {
  @return $number * ($width / $colums);
}

.container {
  $w: 1200px;
  .item:nth-child(1) {
    width: columns(); /* columns(1, 12, $width: $w) */
  }
  .item:nth-child(2) {
    width: columns(8); /* columns(8, 12, $width: $w) */
  }
  .item:nth-child(3) {
    width: columns(3); /* columns(3, 12, $width: $w) */
  }
}
```

컴파일하면 아래와 같다.

```css
.container .item:nth-child(1) {
  width: 100px;
}
.container .item:nth-child(2) {
  width: 800px;
}
.container .item:nth-child(3) {
  width: 300px;
}
```

> ❗️❗️❗️   
> 키워드 인수를 사용하려면 Mixin의 매개변수에 기본값이 설정되어있어야 한다. 


## 2. 함수명 중복 

함수는 별도의 지시어 없이 사용하기 때문에 내장 함수와 이름이 충돌할 수  있다.    
따라서 내가 지정한 함수에는 별도의 접두를 붙이는 것이 좋다. 

```css
/* 내가 정의한 함수: extract-red() */
@function extract-red($color) {
  /* 내장 함수: red() */
  @return rgb(red($color), 0, 0);
}

div {
  color: extract-red(#D55A93);
}
```

혹은 모든 내장 함수의 이름을 알고 있을 수 없으므로 특별한 이름을 접두어로 사용할 수도 있다. 

```css
my-custom-func-red()
```