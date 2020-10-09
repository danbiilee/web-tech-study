# yarn을 이용한 패키지 관리

`yarn`을 이용해 프로젝트의 패키지를 관리하는 방법을 알아보자. 


## 1. yarn 설치하기
`yarn`을 설치하기 위해선 Node.js와 npm이 설치되어 있어야 한다. 

```js
$ npm install -g yarn
```


## 2. 커맨드 명령어 

### 1️⃣ 프로젝트 초기화  
Node.js 프로젝트를 초기화하고, package.json 파일을 생성한다.   

**✔️ 옵션**   
- `-y` 또는 `--yes`: package.json을 설정하는 질문들을 모두 건너뛰고 파일이 생성된다. 

```js
$ yarn init -y
```

### 2️⃣ 패키지 설치
package.json 파일의 `dependencies`와 `devDependencies`로 관리되고 있는 의존성 모듈 패키지들을 설치한다. 설치하면 프로젝트 루트에 `node_modules`라는 디렉터리가 추가된다. 

```js
$ yarn 또는 yarn install 
```

### 3️⃣️ 패키지 추가
npm과 다르게 `--save` 옵션을 명시하지 않아도 package.json의 `dependencies`에 자동으로 추가된다. 

**✔️ 옵션**
- `global`: 패키지를 현재 프로젝트가 아니라 전역에 설치한다.
- `--dev`: 패키지를 package.json의 `devDependencies`에 추가한다.

```js
$ yarn add 패키지명
```

### 4️⃣ 패키지 삭제

```js
$ yarn remove 패키지명
```

### 5️⃣ 패키지 업그레이드

프로젝트에 설치된 모든 패키지들의 버전을 지정된 범위 내에서 업그레이드 하고, yarn.lock을 재생성 한다. 
```js
$ yarn upgrade
```

또는 특정 패키지를 특정 버전으로 업그레이드 할 수도 있다. 
```js
$ yarn upgrade 패키지명@버전
```

### 6️⃣ yarn 버전 업데이트

```js
yarn self-update
```




## 3. 패키지 종류 

### 서버 구축에 필요한 패키지들 
- `express`: 웹 서버를 구축할 수 있는 Node.js 기반의 프레임워크.
- `mongoose` 
  - MongoDB기반 ODM(Objet Data Mapping) 라이브러리. 
  - `ODM`은 DB와 객체지향 프로그래밍 언어 사이에 호환되지 않는 데이터를 변환하는 프로그래밍 기법으로, **MongoDB에 있는 데이터를 JS 객체로** 사용할 수 있도록 해준다. 
- `body-parser`
  - POST Request의 본문을 해석해주는 미들웨어.
  - 보통 **폼 데이터나 AJAX요청**의 데이터를 처리한다. 
  - express에도 `body-parser`가 내장되어 있으나, 내장된 `body-parser`는 JSON과 URL-encoded 형식의 본문 외에 Raw, Text 형식의 본문은 해석할 수 없다. 

### 서버 실행에 필요한 패키지들 

- `nodemon`: 코드 변경시 자동으로 서버를 재시작해준다.
- `concurrently`: 동시에 여러 서버를 실행시켜준다. 

다음은 pacakge.json 의 scripts 명령어 설정이다.    
명령어창에서 `yarn dev` 를 하면, `concurrently` 로 인해 client 디렉터리와 server 디렉터리의 코드가 동시에 실행될 것이다. 

```js
{
  "scripts": {
    "client": "cd client && yarn start",
    "server": "nodemon server/server.js",
    "dev": "concurrently --kill-others-on-fail \"yarn server\" \"yarn client\""
  },
}
```
 