# 확장(Extend)

다른 선택자의 스타일을 전부 가져와 그대로 사용해야하는 경우 `@extend`로 확장 기능을 사용할 수 있다.

```css
@extend 선택자;
```

```css
.btn {
  padding: 10px;
  margin: 10px;
  background: blue;
}

.btn-danger {
  @extend .btn; /* .btn의 모든 스타일을 적용 */
  background: red;
}
```

컴파일되면 아래와 같다.   
결과를 보면 `,`로 구분하는 `다중 선택자`가 만들어졌다. 

```css
.btn, .btn-danger {
  padding: 10px;
  margin: 10px;
  background: blue;
}

.btn-danger {
  background: red;
}
```


**⚠ 다중 선택자로 인한 문제점**

- 현재 선택자가 어디에 첨부될 것인가?
    - `.btn-danger` 가 어느 부분에 다중 선택자로써 추가될 지 예측이 어렵다.
- 그로 인해 원치 않는 부작용이 발생할 수 있는가?
- 한 번의 확장으로 얼마나 큰 CSS선택자가 생성되는가? 
  - 조상 요소의 선택자들이 의도한 것과 다른 결과를 갖게될 수 있다. 
  
```css
.container {
  width: 300px;
  height: 300px;
  background: red;
  .item {
    width: 200px;
    height: 200px;
    background: blue;
    .icon {
      width: 100px;
      height: 100px;
      background: green;
    }
  }
}

.new-icon {
  @extend .icon;
}

.wrapper {
  .new-icon {
    @extend .icon;
  }
}
```

컴파일되면 아래와 같다.  

`new-icon` 요소에 `.icon`의 스타일은 적용이 되었지만 의도와는 완전히 다른 선택자가 생성됨을 볼 수 있다. 

```css
.container .item .icon, 
.container .item .new-icon {
  width: 100px;
  height: 100px;
  background: green;
}

.container .item .icon, 
.container .item .wrapper .new-icon, 
.wrapper .container .item .new-icon {
  width: 100px;
  height: 100px;
  background: green;
}
```

그러므로 확장보다는 Mixin 기능을 사용하는 걸 추천한다!

> (➕) Extend 사용을 권장하지 않는 이유   
> [Sass Guidelines Extend](https://sass-guidelin.es/ko/#extend)