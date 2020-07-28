# 반복


## 1. @for

`@for`는 자바스크립트의 `for문`과 유사하며 `from`, `through`, `to` 키워드와 함께 사용된다.   

`through`를 사용하는 형식과 `to`를 사용하는 형식으로 나뉜다.

- `through`: 종료만큼 반복한다.
- `to`: 종료 직전까지 반복한다. 
- 변수: 관례상 `$i` 혹은 `$index`를 사용한다.
  
```css
@for $변수 from 시작 through 종료 {
	/* 반복 내용 */
}

@for $변수 from 시작 to 종료 {
	/* 반복 내용 */
}
```

(❗) 변수를 스타일 속성 값이 아닌 문자로써 넣고자 할 때는 문자 보간(`#{}`)을 이용해야한다. 

```css
/* 1부터 3까지 3번 반복 */
@for $i from 1 through 3 {
	.through:nth-child(#{$i}) {
		width: 20px * $i
	}
}

/* 1부터 3 직전까지 2번 반복 */
@for $i from 1 to 3 {
	.to:nth-child(#{$i}) {
		width: 20px * $i
	}
}
```

컴파일되면 아래와 같다. 

```css
.through:nth-child(1) { width: 20px; }
.through:nth-child(2) { width: 40px; }
.through:nth-child(3) { width: 60px; }

.to:nth-child(1) { width: 20px; }
.to:nth-child(2) { width: 40px; }
```



## 2. @each

`@each`는 자바스크립트의 `for in 문`과 유사하며, List와 Map 데이터를 반복할 때 사용한다.   
(List는 자바스크립트의 배열과 유사, Map은 객체와 유사하다.)

```css
@each $변수 in 데이터 {
	/* 반복 내용 */
}
```

아래는 List 데이터를 반복하는 예제이다. 

```css
$fruits: (apple, orange, banana, mango); /* List Data */

.fruits {
	@each $fruit in $fruits {
		li.#{$fruit} {
			background: url("/images/#{fruit}.png");
		}
	}
}
```

컴파일되면 아래와 같다. 


```css
.fruits li.apple {
  background: url("/images/apple.png");
}
.fruits li.orange {
  background: url("/images/orange.png");
}
.fruits li.banana {
  background: url("/images/banana.png");
}
.fruits li.mango {
  background: url("/images/mango.png");
}
```



## 3. @while

