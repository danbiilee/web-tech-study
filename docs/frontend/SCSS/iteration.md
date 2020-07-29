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


### 2.1 List 데이터 반복하기 

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

반복할 때 index 값이 필요하다면 아래와 같이 내장함수 `index()`를 사용할 수 있다. 

> **✨ 내장함수 `index(list, item)`**   
> List 데이터 중에 특정 item의 index값을 반환한다. 

```css
$fruits: apple, orange, banana, mango; /* List Data는 괄호가 없어도 된다 */

.fruits {
	@each $fruit in $fruits {
		$i: index($fruits, $fruit);
		li:nth-child(#{$i}) {
			left: 10px * $i;
		}
	}
}
```

컴파일되면 아래와 같다.

```css
.fruits li:nth-child(1) {
  left: 10px;
}
.fruits li:nth-child(2) {
  left: 20px;
}
.fruits li:nth-child(3) {
  left: 30px;
}
.fruits li:nth-child(4) {
  left: 40px;
}
```

동시에 여러 개의 List 데이터를 반복 처리할 수도 있다.  

(❗) 단, 각 List 데이터의 length가 같아야 한다. 

```css
$apple: (apple, 50);
$orange: (orange, 100);
$banana: (banana, 150);

@each $fruit, $width in $apple, $orange, $banana {
	.box-#{$fruit} {
		width: #{$width}px;
	}
}
```

컴파일되면 아래와 같다. 

```css
.box-apple {
  width: 50px;
}
.box-orange {
  width: 100px;
}
.box-banana {
  width: 150px;
}
```


### 2.2 Map 데이터 반복하기

Map 데이터를 반복할 경우 `key`와 `value`의 값을 담을 두 개의 변수가 필요하다.

```css
@each $key, $value in 데이터 {
	/* 반복 내용 */
}
```

Map 데이터에서는 내장함수 `index()` 사용이 불가능하기 때문에     
필요한 경우에는 List 데이터로 변환해서 index 값을 추출해야 한다.

> **✨ Map을 List로 변환하는 내장함수**     
- `map-keys($map-data)`: 인수로 전달한 Map 데이터의 `key`만 추출해서 List 데이터로 변환한다.
- `map-values($map-data)`:  인수로 전달한 Map 데이터의 `value`만 추출해서 List 데이터로 변환한다.


```css
$fruits-data: (
	apple: korea,
	orange: china,
	banana: japan
) /* Map 데이터는 괄호가 필수이다 */

@each $fruit, $country in $fruits-data {
	$fruits-data-key-list: map-keys($fruits-data); /* (apple, orange, banana) */
	$index: index($fruits-data-key-list, $fruit);
	.box-#{$fruit} {
		width: 100px * $index;
		background: url("/images/#{$country}.png");
	}
}
```

컴파일되면 아래와 같다.

```css
.box-apple {
	width: 100px;
  background: url("/images/korea.png");
}
.box-orange {
	width: 200px;
  background: url("/images/china.png");
}
.box-banana {
	width: 300px;
  background: url("/images/japan.png");
}
```



## 3. @while

`@while` 은 자바스크립트의 `while문`과 유사하여 조건이 `false`가 될 때까지 반복한다.   
잘못된 조건으로 인해 컴파일 중 무한 루프에 빠질 수 있으므로 사용을 권장하지 않는다!

```css
@while 조건 {
	/* 반복 내용 */
}
```

```css
$i: 6;

@while $i > 0 {
	.item-#{$i} {
		width: 2px * $i;
	}
	$i: $i -2;
}
```

컴파일되면 아래와 같다. 

```css
.item-6 { width: 12px; }
.item-4 { width: 8px; }
.item-2 { width: 4px; }
```
