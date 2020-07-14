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
child 요소의 `width`를 50%로 설정하면 부모인 parent 요소의 `width`의 50%로 적용되기 때문에,    
결과적으로 child 요소는 250px만큼의 `width`를 갖게 된다.


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
parent 요소의 `width: 600px`을 `font-size: 10px`로 나눈 값이 60이므로,    
`width: 60em`으로 바꾸면 그 전과 동일하게 600px의 width를 가지게 된다. 
> 🧮 60em x 10px(`font-size`) = 600px(`width`)  

child 요소에는 `font-size` 속성을 지정하지 않았지만 **parent 요소의 CSS 속성을 상속받아** `font-size: 10px`이 되므로,     
 `width: 30em`으로 바꾸면 그 전과 동일하게 300px의 width를 가지게 된다.  
> 🧮 30em x 10px(`font-size`) = 300px(`width`)  

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
parent 요소는 부모인 container 요소의 `font-size: 10px`을 상속받는데,   
해당 요소에 지정한 `font-size: 2em`으로 인해 결과적으로 parent 요소의 `font-size`는 10px의 **2배**인 20px이 된다.  

같은 방식으로 child 요소는 부모인 parent 요소의 `font-size`를 상속받고,   
`font-size: 2em`으로 인해 그 2배인 40px이 된다.   

parent 요소와 child 요소의 `font-size`는 상속받은 부모 container 요소의 고정된 `font-size: 10px`의 영향을 받기 때문에,    
container 요소의 `font-size`를 늘이거나 줄이면 자식 요소인 parent 요소와 child 요소의 `font-size`도 동시에 변경되는 특징이 있다. 


## 4. rem
`em`은 모든 조상 요소들의 `font-size`에 영향을 받았다면,    
`rem`은 가장 root 요소인 **`html`태그의 `font-size`에만** 영향을 받는 단위이다. 

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

child 요소의 `width`는 `html`태그의 `font-size: 10px`에만 영향을 받으므로 200px이 된다. 
> 🧮 20em x 10px(`font-size`) = 200px(`width`)  

```css
html {
	font-size: 10px;
}
body {
	font-size: 16px;
}
```
`rem` 단위에는 영향을 주지 않고 요소들의 `font-size`를 특정 값으로 설정하고 싶다면,  
 `body`태그 혹은 특정 요소에 `font-size` 속성을 **다시 지정해서 덮어씌우면 된다.**


## 5. vw, vh
`viewport`는 요소들이 그려지는 브라우저 화면 전체를 의미한다.

이 때 **백분율 기준**으로 `viewport`의 `width`를 단위로 정의한 것이 `vw`, `height`를 단위로 정의한 것이 `vh`이다. 

```css
.container {
	/* width: 100vw; */ 
	width: 50vw;
	height: 50vh;
}
```

container 요소에 `width: 100vw`를 설정하면 해당 요소는 화면 전체 너비만큼 차지하게 되고,   
그 절반인 `width: 50vw`로 설정하면 화면 전체 너비의 절반만큼 차지하게 된다.   
즉, 100이 최대 사이즈라고 생각하면 된다.  

전체 화면 사이즈가 작아지거나 커지면, 자동적으로 container 요소의 사이즈도 변경된 화면 사이즈에 반응해 변경되는 특징이 있다. 



## 5. vmin, vmax
`vmin`은 `viewport`의 최소 너비, `vmax`는 `viewport`의 최대 너비를 의미한다.      
이 때 최소 너비는 `viewport`의 width와 height 중 더 작은 값을, 최대 너비는 더 큰 값을 가리키므로 유의하자. 

