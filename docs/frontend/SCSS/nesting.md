# 중첩

## 1. 중첩(Nesting)
SCSS는 중첩 기능을 사용하여 상위 선택자의 반복을 피하고 좀 더 편리하게 코드를 작성할 수 있다. 

```css
.section {
  width: 80%;
  .list {
    padding: 20px;
    li {
      float: left;
    }
  }
}
```

CSS로 컴파일되면 아래와 같다.     
이처럼 SCSS로 코드를 작성하면 반복되는 `.section`과 `.list` 선택자의 중복을 피할 수 있게 된다. 

```css
.section {
  width: 80%;
}
.section .list {
  padding: 20px;
}
.section .list li {
  float: left;
}
```


## 2. &(상위 선택자 참조)
중첩 구조 안에서 `&` 는 상위(부모) 선택자를 참조하여 치환한다. 

```css 
.btn {
  position: absolute;
  &.active {
    color: red;
  }
}
```

클래스로 `btn`을 가지면서 동시에 `active`를 갖는 버튼 요소를 선택하기 위해 중첩 안에서 `&` 키워드를 사용했다.    

```css
.btn {
  position: absolute;
}
.btn.active {
  color: red;
}
```

만약 중첩구조 안에서 `&` 대신 `.btn.active`라고 쓴다면 아래와 같은 결과가 나올 것이다. 

```css
.btn .btn.active {
  color: red;
}
```

아래의 `&`는 상위 선택자인 `li` 태그로 치환되어(_여기서 `&`의 상위 선택자는 `.list`가 아니라 `li` 이다!_)   
`.list` 요소의 자식 `li`태그들 중 가장 마지막 `li`태그가 선택된다. 

```css
.list {
  li {
    &:last-child {
      margin-right: 0;
    }
  }
}
```

컴파일되면 아래와 같다. 

```css
.list li:last-child {
  margin-right: 0;
}
```

아래처럼 꼭 중첩되는 구조가 아니어도 `&`의 치환 속성을 이용해 선택자의 중복을 피할 수도 있다. 

```css
.fs {
  &-small { font-size: 12px; }
  &-medium { font-size: 14px; }
}
```

컴파일되면 아래와 같다. 

```css
.fs-small {
  font-size: 12px;
}
.fs-medium {
  font-size: 14px;
}
```


## 3. @at-root(중첩 벗어나기)
`@at-root` 키워드를 선택자 앞에 붙이면 해당 요소는 중첩 구조에서 벗어나 최상위 루트에 정의된다. 

> ✨ 변수  
> - `$변수명` 
> - 유효범위: 중첩에서 선언한 변수는 그 안에서만 유효하다. 

```css
.list {
  $w: 100px; /* $w 라는 변수 선언 */
  li {
    width: $w;
  }
  @at-root .box {
    width: $w;
  }
}
```

중첩 안에 선언된 변수는 그 밖에서 사용할 수 없기 때문에 위의 예제처럼 `.list`의 중첩 안에서 `$w` 변수를 사용하되,     
`@at root` 를 이용해 `.box` 요소는 `.list` 요소와 부모-자식 관계가 아님을 나타내야 한다. 

```css
.list li {
  width: 100px;
}
.box {
  width: 100px;
}
```


## 4. 중첩된 속성

`font-`, `margin-`, `border-` 등과 같이 동일한 네임 스페이스를 갖는 속성들은 다음과 같이 중첩 구조를 사용할 수 있다. 

```css
.box {
  font: {
    weight: bold;
    size: 10px;
  };
  margin: {
    top: 10px;
    left: 20px;
  };
}
```

컴파일되면 아래와 같다. 

```css
.box {
  font-weight: bold;
  font-size: 10px;
  margin-top: 10px;
  margin-left: 20px;
}
```