# 데이터 종류

| 데이터 | 설명 | 예
|---|---|---|
| Numbers | 숫자 | `1`, `20px`, `2rem` ... 
| Strings | 문자 | `bold`, `"/imgs/a.png"`, `"sans-serif"` ... 
| Colors | 색상 | `red`, `#FFF`, `rgba(255,0,0.5)` ...
| Booleans | 논리 | `true`, `false`
| Nulls | 아무것도 없음 | `null`
| Lists | 값의 목록 | `(a, b, c)`, `a b c`
| Maps | `key: value` 형태의 목록 | `(apple: a, banana: b))`

## 특이사항
- Numbers: 숫자에 단위가 있거나 없다.
- Strings: 문자에 따옴표가 있거나 없다.
- Nulls: 값으로 `null`이 사용되면 컴파일하지 않는다.
- Lists
  - `공백`이나 `,` 로 값을 구분한다. 
  - `()`를 붙이거나 붙이지 않는다.  
- Maps: `()` 를 꼭 붙여야 한다. 