# REST API 만들기

## 1. Mongo DB 설치, 환경변수 등록
- [DB 생성](https://www.a-mean-blog.com/ko/blog/%EB%8B%A8%ED%8E%B8%EA%B0%95%EC%A2%8C/_/mongoDB-Atlas-%EA%B0%80%EC%9E%85-%EB%B0%A9%EB%B2%95-%EB%AC%B4%EB%A3%8C-mongo-DB-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EC%84%9C%EB%B9%84%EC%8A%A4)
- [환경변수 등록](https://www.a-mean-blog.com/ko/blog/Node-JS-%EC%B2%AB%EA%B1%B8%EC%9D%8C/%EC%A3%BC%EC%86%8C%EB%A1%9D-%EB%A7%8C%EB%93%A4%EA%B8%B0/Environment-Variable-%ED%99%98%EA%B2%BD%EB%B3%80%EC%88%98)
- ⚠ 주의 
  - 몽고DB connection string 가져올 때 버전을 `2.2.12 or later`로 해야 에러나지 않는다.


## 2. 프로젝트 생성
### 2.1 npm 설치
폴더 생성 후 해당 폴더에서 command line으로 `package.json` 파일을 생성한다.

```javascript
$ npm init
```

`--yes` 옵션을 사용하면 모든 항목을 스킵하고 기본값으로만 구성된 `package.json` 파일을 생성한다.     

```javascript
$ npm init --yes
```

### 2.2 Package 설치
- `express`: node.js 웹서버 제작 프레임워크
- `mongoose`: mongoDB 라이브러리
- `body-parser`: HTTP의 body를 오브젝트로 변환시켜 주는 미들웨어. form으로 전송된 데이터를 서버에서 쉽게 사용하기 위해 사용한다.
 
```javascript
$ npm install express mongoose body-parser --save
```

> 📌 그 외 참고할만한 Package들 
> - `nodemon`
>   - 코드 변경시 자동으로 서버를 재시작해준다. 
>   - `--global` 옵션을 사용해 특정 프로젝트 내부가 아닌 컴퓨터에 직접 설치하자. 
> - `method-override` 
>   - `HTTP Methods` 중 `put`과 `delete`를 사용하기 위해 설치한다. 대부분 브라우저의 form은 `get`과 `post`말곤 허용하지 않기 때문이다. 
>   - 브라우저에선 허용하지 않지만 나중에 API로 연결할 땐 문제가 되지 않는다.    

### 2.3 디렉토리 구조 
```
📦practice-make-restapi
 ┣ 📂api
 ┃ ┗ 📜heroes.js
 ┣ 📂models
 ┃ ┗ 📜hero.js
 ┣ 📜index.js
 ┗ 📜README.md
```

