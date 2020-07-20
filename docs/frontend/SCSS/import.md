# Import

## 1. 파일 가져오기 (Import)
`@import` 로 외부의 SCSS 파일을 가져올 수 있다.  
```css
@import "path";
```

이 때 주 파일과 가져온 파일은 모두 **하나의 CSS 파일로 병합**되어 컴파일된다.   
또한 가져온 파일에 정의된 변수 또는 Mixins 등은 주 파일에서 사용할 수 있다. 

### 📌 CSS 규칙으로 Import 되는 경우
SCSS에서 `@import` 는 기본적으로 SCSS 파일을 가져오지만,   
아래와 같은 경우엔 CSS 규칙으로 컴파일된다. 

- 파일 확장자가 `.css`인 경우 
- 파일 이름이 `http://` 또는 `https://`로 시작하는 경우
- `url()`를 사용한 경우
- 미디어쿼리가 있는 경우 

```css
@import "hello.css";
@import "http://hello.com/hello";
@import url(hello);
@import "hello" screen;
```

이 때는 아래와 같이 기존 CSS의 import문처럼 컴파일된다. 

```css
@import url("path");
```


## 2. 여러 파일 가져오기
하나의 `@import`로 여러 파일을 가져올 수 있다.    
이 때는 `,`를 이용해 파일을 구분한다.

아래처럼 `"./header.scss"`를 경로와 확장자를 생략하여 `"header"`로 표현할 수 있다.    

```css
@import "header", "footer";
```


## 3. 파일 분할 (Partials)
유지보수 관리나 성능 차원에서 컴파일할 때 모든 SCSS 파일들을 각각 `~.css` 파일로 컴파일하지 않기 위해,   
파일 이름 앞에 `_`를 붙여 `@import`로 가져오는 것을 Sass의 `Partials` 기능이라고 한다. 

다음과 같이 `scss/` 폴더 안에 3개의 SCSS파일이 있을 때,    
`main.scss`에서 나머지 SCSS파일을 `import`하고 컴파일한 경우 3개의 CSS파일으로 각각 컴파일된다. 

```
Sass-App
 ├─scss
  │  ├─header.scss
  │  ├─side-menu.scss
  │  └─main.scss
```

```css
/* main.scss */
@import "header", "side-menu";
```

`node-sass`를 이용해 scss 폴더의 파일들을 css폴더로 컴파일한다. 

```node
$ node-sass scss --output css
```

컴파일 후 디렉터리를 확인하면 아래와 같이 `css/` 폴더에 3개의 CSS파일로 각각 컴파일됐음을 확인할 수 있다. 

```
Sass-App
  ├─css
  │  ├─header.css
  │  ├─side-menu.css
  │  └─main.css
  ├─scss
  │  ├─header.scss
  │  ├─side-menu.scss
  │  └─main.scss
```

하지만 `import`로 가져올 파일 이름 앞에 `_`를 붙이면, 그 파일들은 따로 컴파일되지 않는다. 

```
Sass-App
  ├─scss
  │  ├─_header.scss
  │  ├─_side-menu.scss
  │  └─main.scss
```
```css
/* main.scss */
@import "header", "side-menu";
```

위와 같은 방법으로 컴파일하면, 최종적으로 `css/` 에는 `_`를 붙이지 않은 `main.scss` 이름의 CSS파일 1개로만 컴파일됐음을 확인할 수 있다. 

```
Sass-App
  ├─css
  │  └─main.css  # main + header + side-menu
  ├─scss
  │  ├─header.scss
  │  ├─side-menu.scss
  │  └─main.scss
```

> **❗ 유의**  
> 단, `Webpack` 이나 `Parcel`, `Gulp` 같은 빌드툴에서는 Partials 기능을 사용할 필요 없이, 설정된 값에 따라 자동 빌드된다.    
> (`_`를 붙여도 CSS파일로 컴파일될 수 있고, `_`를 붙이지 않아도 CSS파일로 컴파일되지 않을 수 있다. )   
> 하지만 컨벤션처럼 `_`를 사용하는 습관을 갖는 것을 추천한다.