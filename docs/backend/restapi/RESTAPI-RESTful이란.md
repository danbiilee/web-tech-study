# REST API

![rest](../../../imgs/restapi.png)

## API란
- `Application Programming Interface`의 약어로 응용 프로그램에서 사용할 수 있도록 데이터와 기능의 집합을 제공하는 인터페이스를 의미한다. 
- 주로 파일 제어, 창 제어, 문자 제어 등을 위한 인터페이스를 제공한다. 



## REST API란
- `REST 기반`으로 서비스 API를 구현한 것을 의미한다.
- 최근 OpenAPI, 마이크로 서비스 등을 제공하는 업체 대부분은 REST API를 제공한다. 

## 특징
- REST 기반으로 시스템을 분산해 확장성과 재사용성을 높여 유지보수 및 운용을 편리하게 할 수 있다.
- HTTP 표준을 기반으로 구현하므로, HTTP를 지원하는 언어로 Client, Server를 구현할 수 있다. 즉, `REST API`를 구현하면 델파이 클라이언트 뿐만 아니라 JAVA, C# 등을 이용해 Client를 제작할 수 있다. 

## 설계 
### 기본 규칙
> **✔️ 참고: [리소스 원형]**
> - `도큐먼트`: 객체 인스턴스나 DB 레코드와 유사한 개념
> - `컬렉션`: 서버에서 관리하는 디렉토리
> - `스토어`: 클라이언트에서 관리하는 자원 저장소

1. `URI`는 자원의 정보를 표현해야 한다.
  - 자원의 표현은 동사보다는 `명사`를, 대문자보다는 `소문자를` 사용한다.
  - 자원의 도큐먼트 이름엔 `단수 명사`를 사용한다.
  - 자원의 컬렉션 이름엔 `복수 명사`를 사용한다.
  - 자원의 스토어 이름엔 `복수 명사`를 사용한다. 
	> `GET /Members/1` -> `GET /members/1` 
2. 자원에 대한 행위는 `HTTP Method(GET, PUT, POST, DELETE 등)` 로 표현한다.
- URI에 HTTP Method가 들어가면 안된다. 
  > `GET /members/delete/1` -> `DELETE /members/1`
- URI에 행위에 대한 동사표현이 들어가면 안된다. 즉, CRUD 기능을 나타내는 것은 URI에 사용하지 않는다.
  > `GET /members/show/1` -> `GET /members/1`  
	> `GET /members/insert/2` -> `POST /members/2`
- `:id`는 하나의 특정 자원을 나타내는 고유값이다. 
	> `DELETE /students/12`: id가 12인 student를 삭제하는 루트 

### 설계 규칙
> **✔️ 참고: [응답 상태 코드]**
> - `1xx`: 전송 프로토콜 수준의 정보 교환
> - `2xx`: 클라이언트 요청이 성공적으로 수행됨
> - `3xx`: 클라이언트는 요청을 완료하기 위해 추가적인 행동을 취해야 함
> - `4xx`: 클라이언트의 잘못된 요청
> - `5xx`: 서버단에서의 오류 발생 





## RESTful이란 
`REST API`를 제공하는 웹 서비스를 `RESTful`하다고 할 수 있다.

### 목적
- 이해하기 쉽고, 사용이 쉬운 `REST API`를 만드는 것
- 근본적인 목적은 일관적인 컨벤션을 통한 API의 이해도 및 호환성을 높이는 것이므로, 성능이 중요한 상황에서는 굳이 `RESTful`한 API를 구현할 필요는 없다.

### RESTful하지 않은 경우
- CRUD 기능을 모두 POST로만 처리하는 API
- 루트에 자원, id 외의 정보가 들어가는 경우




> ℹ️ 출처  
> [https://gmlwjd9405.github.io](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)