# REST

![rest](../../../imgs/rest.png)

## REST란 
- `Representational State Transfer`의 약어로 자원을 표현(`representation`)으로 구분해 자원의 상태(`state`)를 주고 받는 모든 것을 의미한다.
  - `자원`의 `표현`에 의한 `상태` 전달 
  - 자원: 해당 소프트웨어가 관리하는 모든 것
	> 문서, 그림, 데이터, 해당 소프트웨어 자체 등  
  - 자원의 표현: 자원을 지칭하기 위한 이름 
	> DB의 사원 정보가 자원일 때, 그 자원의 표현으로 `employee`를 정한다. 
  - 상태(정보) 전달
  	- 데이터가 요청되어지는 시점에서 자원의 상태(정보를) 전달한다. 
  	- 일반적으로 `JSON` 혹은 `XML` 형식의 데이터를 주고 받는다. 
- 월드 와이드 웹(`www`)과 같은 분산 하이퍼미디어 시스템을 위한 소프트웨어 개발 아키텍처의 한 형식이다. 
  - 기본적으로 웹의 기존 기술과 HTTP 프로토콜을 활용하기 때문에 웹의 장점을 최대한 활용할 수 있다. 
  - 네트워크 상에서 Client와 Server 사이의 통신 방식 중 하나이다. 
- 구체적 개념 
	- `HTTP URI(Uniform Resource Identifier)`를 통해 자원을 명시하고, `HTTP Method(GET, POST, PUT, DELETE)`를 통해 해당 자원에 대한 `CRUD Operation`을 적용하는 것을 의미한다. 
	- 즉, 자원 기반의 구조(ROA, Resource Oriented Architecture) 설계를 기본으로 `HTTP Method`를 통해 자원을 처리하도록 설계된 아키텍처를 의미한다. 
	- 웹 사이트의 문자, 그림, DB 내용 등 모든 자원에 고유한 ID인 `HTTP URI`를 부여한다.
	- `CRUD Operation`    
      - Create: 생성(`POST`)
      - Read: 조회(`GET`)
      - Update: 수정(`PUT`)
      - Delete: 삭제(`DELETE`)
      - `HEAD`: header 정보 조회 
  
## 구성 요소 
1. 자원(Resource): `URI`
- Server에 존재하는 모든 자원에는 고유한 ID가 존재한다.
- 고유 ID는 `/groups/:group_id`와 같은 `HTTP URI` 형식이다.
- Client는 `URI`를 이용해 자원을 명시하고, 해당 자원의 상태(정보)에 대한 조작을 Server에 요청한다.
2. 행위(Verb): `HTTP Method`
- HTTP 프로토콜의 Method를 사용한다.
- HTTP 프로토콜은 `GET, POST, PUT, DELETE`와 같은 메서드를 제공한다. 
3. 표현(Representation of Resource)
- Client가 자원의 상태(정보)에 대한 조작을 요청하면 Server는 이에 대해 적절한 응답(`Representation`)을 보낸다.
- 자원은 `JSON, XML, TEXT, RSS` 등 여러 형태의 `Representation`으로 나타낼 수 있다.

## 특징
1. Server-Client
- 서버-클라이언트 구조로 자원이 있는 쪽이 Server, 자원을 요청하는 쪽이 Client가 된다.
	- `Server`: API를 제공하고 비즈니스 로직 처리 및 저장을 담당한다.
	- `Client`: 사용자 인증이나 context(세션, 쿠키) 등을 관리, 담당한다.
2. Stateless
- HTTP 프로토콜은 Stateless Protocol이므로 REST 역시 `무상태성`을 갖는다.
- Client의 context를 Server에 저장하지 않는다. 따라서 구현이 단순해진다.
- Server는 각각의 요청을 완전히 별개의 것으로 인식하고 처리한다.
3. Cacheable
- HTTP가 가진 강력한 특징 중 하나인 `캐싱` 기능을 REST에서도 적용할 수 있다. 
  - HTTP 프로토콜 표준에서 사용하는 `Last-Modified` 태그나 `E-Tag`를 이용하면 캐싱 구현이 가능하다.
- 대량의 요청을 효율적으로 처리하기 위해 캐시를 사용한다.
- 캐시 사용으로 인해 응답시간이 빨라지고, REST Server 트랜잭션이 발생하지 않으므로 전체 응답시간, 성능, 서버의 자원 이용률을 향상시킬 수 있다.
4. Layerd System(`계층화`)
- Client는 REST API Server만 호출한다.
- REST Server는 다중 계층으로 구성될 수 있다. 
  - API Server는 순수 비즈니스 로직을 수행하고 그 앞단에 보안, 로드밸런싱, 암호화, 사용자 인증 등을 추가해 구조상의 유연성, 확장성, 보안성을 향상시킬 수 있다. 
- PROXY, 게이트웨이 같은 네트워크 기반의 미들웨어를 사용할 수 있다. 
5. Code-On-Demand(optional)
- Server로부터 스크립트를 받아 Client에서 실행할 수 있다. 
6. Uniform Interface 
- URI로 지정한 자원에 대한 조작을 `일관된 인터페이스`로 수행한다. 
- 특정 언어나 기술에 종속되지 않고, HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다. 


> ℹ️ 출처  
> [https://gmlwjd9405.github.io](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)