# CSS Reset(초기화)

## CSS를 초기화하는 이유
기본적으로 브라우저에서 제공하는 CSS 스타일이 조금씩 다르기 때문에    
직접 설정한 CSS를 모든 브라우저에서 동일하게 출력시키기 위해서는 CSS 초기화가 필요하다. 

## reset.css 라이브러리 
[reset-css CDN by jsDelivr](https://www.jsdelivr.com/package/npm/reset-css) 에서 
`reset.min.css` 를 COPY HTML 하여 코드에 붙여넣는다. 

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/reset-css@5.0.1/reset.min.css">
<link rel="stylesheet" href="./css/main.css">
```

브라우저의 CSS를 초기화한 후에 스타일을 적용하기 위해서는,   
반드시 **`reset.min.css` 파일이 직접 작성한 `main.css` 파일보다 먼저 호출될 수 있도록** 해야 한다.     
 