# IF


## 1. if(함수)

자바스크립트의 조건부 삼항 연산자와 비슷하다.

조건의 값이 `true`이면 `표현식1`을,    
조건의 값이 `false`이면 `표현식2`를 반환한다.

```css
if(조건, 표현식1, 표현식2)
```

아래의 if 함수는 조건의 값이 `true`이므로 `표현식1`이 반환된다.   

> ️❗️️️️️️️❗️❗❗️
> SCSS의 데이터 타입 중 `null`은 값이 없다는 뜻이 아니라 그 속성을 사용하지 않겠다, 즉 해당 속성이 컴파일되지 않음을 뜻한다. 

```css
$px: 555px;
div {
  width: if($px > 300px, $px, null);
  height: if($px > 800px, $px, null);
}
```

컴파일되면 아래와 같다. 

```css
div {
  width: 555px;
}
```



## 2. @if(지시어)

`@if` 지시어는 자바스크립트의 if문 과 유사하여 조건에 따른 분기 처리가 가능하다.   
`@else`, `@else if`와 함께 사용하여 좀 더 복잡한 조건문을 작성할 수 있다. 

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
