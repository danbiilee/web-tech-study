# 단위


## 1. px
일반적으로 크기를 고정하는 단위이다. 


## 2. %
부모 요소 사이즈에 영향을 받는 단위이다.
```css
.parent {
	width: 500px;
}
.child {
	/* width: 250px; */
	width: 50%;
}
```
child 요소의 `width`를 50%로 변경해도 부모 요소 parent의 `width: 500px`의 50%로 적용되기 때문에 250px과 동일하다.


## 3. em
자기 자신의 `font-size`에 영향을 받는 단위이다.
```css
.parent {
	/* width: 600px; */
	width: 60em;
	font-size: 10px;
}
.child {
	/* width: 300px; */
	width: 30em;
}
```
parent 요소의 `width: 600px`을 `font-size: 10px`로 나누면 60px이므로 `width: 60em`으로 바꾸면 그 전과 동일하게 600px의 width를 가지게 된다. (60em x 10px)   

child 요소에는 `font-size` 속성을 지정하지 않았지만 **parent 요소의 CSS 속성을 상속받으므로** `font-size: 10px`이라 생각하고, `width: 30em`으로 바꾸면 그 전과 동일하게 300px의 width를 가지게 된다.  

```css
.container {
	font-size: 10px;
}
.parent {
	font-size: 2em; /* 20px */
}
.child {
	font-size: 2em; /* 40px */
}
```
parent 요소는 부모인 container 요소의 `font-size: 10px`을 상속받는데, 해당 요소에 지정한 `font-size: 2em`으로 인해 자기 자신의 `font-size`는 상속받은 10px의 **2배**인 20px이 된다.    
같은 방식으로 child 요소는 부모인 parent 요소의 `font-size`를 상속받고, `font-size: 2em`으로 인해 그 2배인 40px이 된다.   
parent 요소와 child 요소의 `font-size`는 상속받은 부모 container 요소의 고정된 `font-size`의 영향을 받기 때문에, container 요소의 `font-size`를 늘이거나 줄이면 자식 요소인 parent 요소와 child 요소의 `font-size`도 동시에 변경된다. 


## 4. rem
`em` 단위는 모든 부모 요소들의 `font-size`에 영향을 받았다면, `rem` 단위는 가장 root 요소인 **`html`태그의 `font-size`에만** 영향을 받는 단위이다. 

```css
html {
	font-size: 10px;
}
.container {
	font-size: 10px;
}
.parent {
	font-size: 2em; /* 20px */
}
.child {
	width: 20rem; 
	font-size: 2em; /* 40px */
}
```

child 요소의 `width`는 `html`태그의 `font-size: 10px`에만 영향을 받으므로 20rem x 10px 즉, 200px이 된다. 

```css
html {
	font-size: 10px;
}
body {
	font-size: 16px;
}
```
`rem` 단위에는 영향을 주지 않고, 요소들의 `font-size`를 특정 값으로 설정하고 싶다면 `body`태그 혹은 특정 요소에 `font-size` 속성을 _다시_ 지정해서 덮어씌우면 된다. 
