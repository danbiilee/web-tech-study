# Compile SCSS to CSS

## 1. SassMeister
간단한 SCSS 코드는 컴파일러를 설치하지 않고 [SassMeister](https://www.sassmeister.com/) 페이지를 이용할 수 있다.      
페이지 상단의 옵션에서 SCSS Syntax를 선택 후 왼쪽 칸에 SCSS 코드를 작성하면 된다. 


## 2. Parcel
터미널에서 프로젝트 루트로 이동한 후 아래의 과정을 따라 parcel을 설치 및 실행한다. 

### 2.1 package.json 생성
```node
$ npm init -y
``` 
- `-y`(`-yes`): 자동적으로 기본 설정값을 가진 package.json 파일을 생성해주는 플래그이다.


### 2.2 parcel 설치
```node
$ npm i -D parcel-bundler
```

`npm i`(`install`) 명령어로 `./node_modules` 디렉터리에 패키지를 설치한다.

- `--save`
  -  `package.json` 파일의 `dependencies` 항목에 플러그인(패키지) 정보가 저장된다. 
  -  빌드 시(`--production`) 해당 플러그인이 포함된다. 
- `-D`(`--save-dev`)
  -  `package.json` 파일의 `devDependencies` 항목에 플러그인(패키지) 정보가 저장된다. 
  -  빌드 시(`--production`) 해당 플러그인이 포함되지 않는다.     


설치가 완료되면 `package.json`파일에서 아래와 같이 확인할 수 있다. 

```js
"devDependencies": {
	"parcel-bundler": "^1.12.4"
}
```

### 2.3 parcle 실행
```node
$ npx parcel index.html
```

`npx` 명령어를 이용해 전역에서 `parcel`로 `index.html`을 컴파일하여 실행한다. 

![parcel-complie](../../../imgs/parcel-complie.JPG)

서버가 실행중인 http://localhost:1234 에 접속해 컴파일된 index.html 파일을 확인하자. 
> ✔️ 서버가 실행 중일 때 scss 파일의 내용을 수정하면 바로 적용되는 것을 확인할 수 있다!  
  

