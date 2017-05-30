# OAuth 2.0 기본 개념 정리

## OAuth 2.0이란?
웹, 모바일 앱, 그리고 어플리케이션에서 권한 인증을 수행할 수 있는 단순하고 표준 방법인 Open 프로토콜. OAuth 2.0으로 인증을 하게되면 인증 서버는 권한 부여의 결과로 `access token`을 발급하게 되고, 클라이언트는 이 `access token`을 이용해서 리소스를 요청하게 된다. 서버는 `access token` 기반으로 클라이언트를 인식하고 리소스 접근 여부를 결정한다.

## OAuth 2.0 인증과 REST API
서버는 `access-token`기반으로 클라이언트를 인식하기 때문에 세션(Session)이나 쿠키(Cookie)를 이용해 클라이언트의 상태 정보를 유지할 필요가 없다. 이것은 RESTful API 디자인의 요구사항인 `stateless`를 만족시키기 때문에 REST API 인증 방식으로 적합하다.

## 관련 용어
### Authentication
- 인증
- 리소스 접근시 등록된 사용자인지 확인하는 것
- 로그인

### Authorization
- 인가
- 특정 행위를 수행할 권한을 가진 사용자인지 검증하는 과정
- 주로 인가(권한을 검증)하기 전에 인증이 요구됨
- 권한에 따라 사용할 수 있는 기능이 제한됨ㅍ
- 사용자 등급 혹은 롤 (user, admin)

## OAuth 2.0 구성 요소와 역할
- Resource Owner : 사용자(user)
- Client : Third patry Application(웹브라우저, mobile app 등)), API를 사용하는 애플리케이션
- Authorization Server : 인증 서버
- Resource Server : 리소스를 제공하는 API 서버, 보통 REST API

## OAuth 2.0에서 권환을 획득하는 방법 (Grant types)
### 1. Authorization Code
웹 서버에서 API를 호출하는 등의 시나리오에서 Confidential Client가 사용하는 방식이다. 서버사이드 코드가 필요한 인증 방식이며 인증 과정에서 client_secret 이 필요하다.
로그인시에 페이지 URL에 `response_type=code` 라고 넘긴다.

### 2. Implicit
Token과 scope에 대한 스펙 등은 다르지만 OAuth 1.0a과 가장 비슷한 인증방식이다. Public Client인 브라우저 기반의 어플리케이션(Javascript application)이나 모바일 어플리케이션에서 이 방식을 사용하면 된다. Client 증명서를 사용할 필요가 없으며 실제로 OAuth 2.0에서 가장 많이 사용되는 방식이다.
로그인시에 페이지 URL에 response_type=token 라고 넘긴다.

### 3. Resource Owner Password Credentials
이 방식은 2-legged 방식의 인증이다. Client에 아이디/패스워드를 저장해 놓고 아이디/패스워드로 직접 access token을 받아오는 방식이다. Client 를 믿을 수 없을 때에는 사용하기에 위험하기 때문에 API 서비스의 공식 어플리케이션이나 믿을 수 있는 Client에 한해서만 사용하는 것을 추천한다.
로그인시에 API에 POST로 `grant_type=password` 라고 넘긴다.

### 4. Client Credentials
어플리케이션 이 Confidential Client일 때 id와 secret을 가지고 인증하는 방식이다.
로그인시에 API에 POST로 `grant_type=client_credentials` 라고 넘긴다.

#### 1st party client 에 OAuth2.0의 어떤 인증 타입을 적용할 것인가?
`Authorization Code`와 `Implicit` 타입은 OAuth2 스펙을 보면 credential client 즉, 서비스에 접근하는 *외부 앱*을 위한 타입이다.

`Client Credentials` 타입은 어떤 사용자든 상관없이 클라이언트 그 자체만을 인증하기 위한 방식으로써 사용자별로 인증하여 access token을 발행해야 하는 1st party client 인증용으로는 역시 적절하지 않다.

1st party client 는 특정 서비스를 위한 공식 클라이언트이다. 이 때문에 1st party client 는 등록된 사용자로부터 직접 id 와 password를 입력받아서 Authorization server 를 통해 사용자 인증을 하는데 그것들을 쓸 수 있다. 이러한 특성을 고려했을 때, 클라이언트 및 사용자 인증에 가장 적합한 인증 타입은 `Resource Owner Password Credentials`이다.

1st party client 는 자신의 Authozation server 에 인증 요청을 하는 것이므로, 어떤 페이지로의 리다이렉션 없이 직접 사용자의 id, password를 입력받고 자기의 client id와 secret을 Authorization server로 인증 및 access token을 요청한다. 이 경우, 1st party client 는 사용자 단말에 설치되어 있어서 하드 코딩된 client id 와 secret 이 노출되는 것이 위험할 수도 있으나, 이 타입의 경우 사용자의 id, password 를 함께 보내야만 인증 가능하므로 사용자 id, password 만 단말 어딘가에 저장하지 않는다면 안전하다고 할 수 있겠다. 그래서 OAuth2.0 스펙에도 access token을 얻은후에 client는 반드시 사용자의 credentials 즉, id와 password를 제거해라고 언급되어 있다.