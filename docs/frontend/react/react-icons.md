# 리액트에서 아이콘 사용하기

`react-icons` 라이브러리를 사용하면 Font Awesome, Ionicons, Material Design Icons 등의 아이콘들을 **컴포넌트 형태**로 쉽게 사용할 수 있다. 

사용법과 아이콘의 종류는 해당 라이브러리의 [문서](https://react-icons.github.io/react-icons/#/)에서 참고할 수 있다. 



## 1. 설치하기

프로젝트 디렉터리에서 라이브러리를 설치한다.

```js
yarn add react-icons
``` 



## 2. 사용하기

[Material Design Icons](https://react-icons.github.io/react-icons/icons?name=md) 의 MdCheckBox, MdCheckBoxOutlineBlank 아이콘을 사용하여 체크박스를 구현해보자. 

```js
// Checkbox.js 

import React from 'react';
import { MdCheckBox, MdCheckBoxOutlineBlank } from 'react-icons/md';

function CheckBox({ children, checked, ...rest }) {
  return (
    <label>
      <input type="checkbox" checked={checked} {...rest} />
      <div>{checkd ? <MdCheckBox /> : <MdCheckBoxOutlineBlank />}</div>
    </label>
  );
}
```

