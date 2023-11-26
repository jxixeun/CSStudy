# :dizzy: REST

'Representational State Transfer'의 약자로 월드 와이드 웹(WWW)과 같은 하이퍼미디어 기반 분산 시스템을 구축하기 위한 아키텍처 스타일이다.

주고 받는 자원(Resource)에 이름을 규정하고 URI에 명시해 HTTP 메서드(GET, POST, PUT, DELETE)를 통해 해당 자원의 상태를 주고받는 것을 의미한다.

# :dizzy: REST API란?

API는 'Application Programming Interface'의 약자로 애플리케이션에서 제공하는 인터페이스를 의미한다. API를 통해 서버 또는 프로그램의 사이를 연결할 수 있다.

REST API는 REST 아키텍처를 따르는 시스템/애플리케이션 인터페이스이다. REST 아키텍처를 구현하는 웹 서비스를 'RESTful 하다'고 표현한다.

# :dizzy: REST의 특징

## 유니폼 인터페이스

일관된 인터페이스를 의미한다. REST 서버는 HTTP 표준 전송 규약을 따르기 때문에 언어와 플랫폼 및 기술에 종속되지 않고 다양한 언어, 플랫폼, 기술 등과 호환해 사용할 수 있다.

## 무상태성

무상태성이란 서버에 상태 정보를 따로 보관하거나 관리하지 않는 다는 의미이다. 

서브는 클라이언트가 보낸 요청에 대해 세션이나 쿠키 정보를 별도로 보관하지 않는다. 그렇기 때문에 한 클라이언트가 여러 요청을 보내든 여러 클라이언트가 각각 하나의 요청을 보내든 개별적으로 처리한다.

이렇게 구성된 서비스는 서버가 불필요한 정보를 관리하지 않으므로 비즈니스 로직의 자유도가 높고 설계도가 단순하다.

## 캐시 가능성

HTTP 표준을 그대로 사용하므로 HTTP의 캐싱 기능을 적용할 수 있다. 이 기능을 이용하기 위해선 응답과 요청이 모두 캐싱 가능한지(Cacheable) 명시가 필요하며, 캐싱이 가능한 경우 클라이언트에서 캐시에 저장해두고 같은 요청에 대해서는 해당 데이터를 가져다 사용한다.

이 기능을 사용하면 서버의 트랜잭션 부하가 줄어 효율적이며 사용자 입장에서 성능이 개선된다.

## 레이어 시스템

REST 서버는 네트워크 상의 여러 계층으로 구성될 수 있다. 그러나 서버의 복잡도와 관계없이 클라이언트는 서버와 연결되는 포인트만 알면 된다.

## 클라이언트-서버 아키텍처

REST 서버는 API를 제공하고 클라이언트는 사용자 정보를 관리하는 구조로 분리해 서로에 대한 의존성을 낮춘다.

# :dizzy: REST의 URI 설계 규칙

## URL 규칙
### URI의 마지막에는 '/' 를 포함하지 않는다.
- 옳은 예 : `http://localhost.com/product`
- 나쁜 예 : `http://localhost.com/product/`

### 언더바(_)는 사용하지 않는다. 대신 하이픈(-)을 사용한다.
하이픈은 리소스의 이름이 길어지면 사용한다.
- 옳은 예 : `http://localhost.com/provider-company-name`
- 나쁜 예 : `http://localhost.com/provider_company_name`

### URL에는 행위(동사)가 아닌 결과(명사)를 포함한다.
행위는 HTTP 메서드로 표현할 수 있어야 한다.
- 옳은 예 : `http://localhost.com/product/1`
- 나쁜 예 : `http://localhost.com/delete-product/1`

### URI는 소문자로 작성해야 한다.
일부 웹 서버의 운영체제는 리소스 경로 부분의 대소문자를 다른 문자로 인식하기 때문에 URI 리소스 경로에는 대문자 사용을 피하는 것이 좋다.

### 파일의 확장자는 URI에 포함하지 않는다.
HTTP에서 제공하는 Accept 헤더를 사용하는 것이 좋다.


# :dizzy: 참고하면 좋은 링크
[RESTful 웹 API 디자인](https://learn.microsoft.com/ko-kr/azure/architecture/best-practices/api-design)
