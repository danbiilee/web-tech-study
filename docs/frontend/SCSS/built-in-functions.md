# 내장함수(Built-in Functions)

[Sass Built-in Functions](https://sass-lang.com/documentation/modules)에서 모든 내장함수를 확인할 수 있다. 

**✔알아두기**
- `[]`는 생략 가능한 인수를 타나낸다. 
- `Zero-based numbering`을 사용하지 않는다. 


## 1. 색상(RGB / HSL / Opacity) 함수

- `mix($color1, $color2)` : 두 개의 색을 섞는다.
- `lighten($color, $amount)` : 더 밝은색을 만든다.
- `darken($color, $amount)` : 더 어두운색을 만든다.
- `saturate($color, $amount)` : 색상의 채도를 올린다.
- `desaturate($color, $amount)` : 색상의 채도를 낮춘다.
- `grayscale($color)` : 색상을 회색으로 변환한다.
- `invert($color)` : 색상을 반전시킨다.
- `rgba($color, $alpha)` : 색상의 투명도를 변경한다.
  - CSS의 순수함수 rgba(r, g, b, a)
  - SCSS의 rgba(red, .5) 🆚 CSS의 rgba(0, 256, 256, .5)
- `opacify($color, $amount)` / `fade-in($color, $amount)` : 색상을 더 불투명하게 만든다.
- `transparentize($color, $amount)` / `fade-out($color, $amount)` : 색상을 더 투명하게 만든다.


## 2. 문자(String) 함수

- `unquote($string)` : 문자에서 따옴표를 제거한다.
- `quote($string)` : 문자에 따옴표를 추가한다.
- `str-insert($string, $insert, $index)` : 문자의 index번째에 특정 문자를 삽입한다.
- `str-index($string, $substring)` : 문자에서 특정 문자의 첫 index를 반환한다.
- `str-slice($string, $start-at, [$end-at])` : 문자에서 특정 문자(몇 번째 글자부터 몇 번째 글자까지)를 추출한다.
  - 3번째 인수 `$end-at`의 기본값은 -1이고 생략 가능하다. 
  - 생략할 경우 index -1까지 즉, 문자열의 끝까지 추출한다. 
- `to-upper-case($string)` : 문자를 대문자를 변환한다.
- `to-lower-case($string)` : 문자를 소문자로 변환한다.


```css
div {
	/* str-insert: a의 앞에서부터 1씩 세기 시작하여 4번째인 c와 d 사이에 xxx를 삽입한다.  */
	width: str-insert("abcd", "xxx", 4); 
	height: str-index("abcd", "c"); 
	position: str-slice("abcd", 3); 
	
}
```

컴파일하면 아래와 같다.


```css
div {
	width: "abcxxxd";
	height: 3;
	position: "cd";
}
```


## 3. 숫자(Number) 함수

- `percentage($number)` : 숫자(단위 무시)를 백분율로 변환한다.
- `round($number)` : 정수로 반올림한다.
- `ceil($number)` : 정수로 올림한다.
- `floor($number)` : 정수로 내림(버림)한다.
- `abs($number)` : 숫자의 절대 값을 반환한다.
- `min($numbers…)` : 숫자 중 최소 값을 찾는다.
- `max($numbers…)` : 숫자 중 최대 값을 찾는다.
- `random()` : 0 부터 1 사이의 난수를 반환한다.



## 4. List 함수

모든 List 내장 함수는 원본 List 데이터를 변경하지 않고, 새 List 데이터를 반환한다.   
그리고 모든 List 내장 함수는 Map 데이터에서도 사용할 수 있다. 

- `length($list)` : List의 개수를 반환한다.
- `nth($list, $n)` : List에서 n번째 값을 반환한다.
- `set-nth($list, $n, $value)` : List에서 n번째 값을 다른 값으로 변경한다.
- `join($list1, $list2, [$separator])` : 두 개의 List를 하나로 결합한다.
- `zip($lists…)` : 여러 List들을 하나의 다차원 List로 결합한다.
- `index($list, $value)` : List에서 특정 값의 index를 반환한다.



## 5. Map 함수
모든 Map 내장 함수는 원본 Map 데이터를 변경하지 않고, 새 Map 데이터를 반환한다.

- `map-get($map, $key)` : Map에서 특정 key의 value를 반환한다.
- `map-merge($map1, $map2)` : 두 개의 Map을 병합하여 새로운 Map를 만든다.
- `map-keys($map)` : Map에서 모든 key를 List로 반환한다.
- `map-values($map)` : Map에서 모든 value를 List로 반환한다.



## 6. 관리(Introspection) 함수

- `variable-exists(name)` 
  - 변수가 현재 범위에 존재하는지 여부를 반환한다.
  - 인수는 $없이 변수의 이름만 사용한다.
- `unit($number)` : 숫자의 단위를 반환한다.
- `unitless($number)` 
  - 숫자에 단위가 있는지 여부를 반환한다.
  - 단위가 없으면 `true`, 있으면 `false`를 반환한다.
- `comparable($number1, $number2)` : 두 개의 숫자가 연산 가능한지 여부를 반환한다.