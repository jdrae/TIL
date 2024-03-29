# 웹 101

## 브라우저 동작 방법

브라우저는 HTTP 를 이용해 웹 서버에서 웹 자원을 가져오거나, 서버에 정보를 송신하는 소프트웨어이다. URL 에 접속해 서버에서 가져온 내용을 브라우저 화면에 표시하기 위해 렌더링 엔진을 사용하는데, 크롬과 사파리는 웹킷(Webkit)을, 파이어폭스는 게코(Gecko) 엔진을 사용한다. 특히 html 과 css 파일은 W3C 에서 정해진 명세에 따라 파싱해서 화면에 보여준다. html 태그를 모두 DOM(문서 객체 모델, Document Object Model) 로 변환하여 렌더 트리를 생성하고, 화면의 정확한 위치에 노드를 표시한다. 이 때 모든 html 을 파싱할 때까지 기다리지 않고 먼저 받은 내용부터 차례로 화면에 보여준다.

## URL & URI

URI 는 고유한 자원 하나를 지칭하고, URL 은 그 자원이 위치한 곳을 의미한다. `http://test.com/main` 은 URL 이자 URI 이지만 `http://test.com/main?id=HELLO&page=1` 은 URL 이 아니라 URI 이다.

## Cookie & Session (+Cache)

HTTP 프로토콜은 클라이언트가 응답을 받으면 연결을 끊어버리고(connectionless), 클라이언트의 상태 정보는 유지하지 않는다(stateless). 따라서 서버는 클라이언트가 누구인지 매번 확인해야 하는데, 쿠키나 세션을 통해 이와 같은 불편함을 해결한다. 

쿠키는 서버가 생성한 쿠키를 브라우저에 저장하고, 브라우저가 종료되어도 쿠키 만료 기간에 따라 클라이언트에서 보관한다. 만약 같은 요청을 할 경우 HTTP 헤더에 쿠키를 함께 보내 클라이언트 정보를 재사용한다. (장바구니, 아이디 저장, 팝업 체크 등)

세션은 클라이언트의 세션 ID 를 서버에 저장하여 클라이언트를 식별한다. 정보는 서버에 저장되고 클라이언트는 session-id 만 갖는다는 점에서 쿠키처럼 변질되거나 request 에서 스니핑 당할 우려가 없어 보안성이 좋다. 다만 서버를 거쳐야 하기 때문에 쿠키보다 느리며, 많은 정보를 저장할 때 서버 부하가 커진다는 단점이 있다. 브라우저가 종료되면 만료기간에 상관없이 삭제되기 때문에 로그인 같이 보안상 중요한 작업을 수행할 때 사용한다. (한번 로그인 하면 탭 이동을 해도 로그인 유지)

캐시는 이미지, css, js 파일을 브라우저에 저장하고 사용하는 것이다. 따라서 한번 저장되면 서버에서 변경되어도 클라이언트에서는 변경이 없을 수도 있기 때문에 캐시를 주기적으로 삭제하거나, header 에 캐시 만료시간을 명시하여 사용한다. 

## JWT (JSON Web Token)

세션 ID 가 세션 저장소에 있는지 확인하는 작업을 거쳐 쿠키보다 보안성을 높였지만, 만약 세션 저장소에 장애가 생긴다면 정상적인 사용자가 인증을 하지 못하는 경우가 생긴다. 또, 세션 정보를 저장하는 것은 HTTP 의 stateless 조건을 위배하기 때문에 서버를 scale out 할 때 따로 세션 저장소를 만들어야한다는 번거로움이 있다. 

JWT 는 인증에 필요한 정보들을 token 에 담아 암호화하고, 서버의 개인키로만 풀 수 있게끔 하여 세션의 단점을 해결한다. `header.payload.signature` 로 이루어져 헤더에는 토큰의 타입과 서명의 알고리즘, 페이로드에는 토큰에서 사용할 정보인 클레임(claim)을 작성한다. 마지막으로 서명에는 헤더와 페이로드를 서버의 개인키로 암호화한 내용이 들어있다. 따라서 signature 는 서버의 개인키로만 복호화 할 수 있기 때문에, 이 signature 의 헤더와 페이로드 정보가 일치한지 확인하여 인증을 허용한다. 즉, 페이로드가 변경되었더라도 signature 의 페이로드가 다르기 때문에 인증이 거부된다.

JWT 는 이미 인증된 정보이기 때문에 별도의 저장소가 필요하지 않아 stateless 하고, 암호화를 통한 보안성까지 갖추고 있는 셈이다. 하지만 토큰이 탈취당하면 만료될 때까지 대처가 불가능하다는 점은 단점이다. 그래서 exp 를 짧게 하고, sliding session 이나 refresh token 으로 짧은 유효시간을 보완한다.

sliding sessiong 은 서비스를 사용하고 있는 유저에 대해 만료 시간을 연장하는 방법이다. 글쓰기, 결제 등이 진행 중일 때 새롭게 만료시간을 늘린 JWT 를 다시 제공함으로써 보완한다. 하지만 단발적으로 접속한다면 sliding sessiong 으로 연장할 수 없는 상황이 생기고, exp 이 길 경우 sliding sessiong 때문에 토큰을 무한정 사용하는 상황이 발생할 수도 있다.

refresh token 은 access token 을 refresh 해주는 것을 보장하는 토큰이다. JWT 를 처음 발급할 때 발급하여, access token 이 만료되었을 경우 서버에게 새로운 access token 을 발급하도록 요청하는 방식이다. 서버는 refresh token 저장소를 만들어 클라이언트가 요청한 토큰과 일치한지 확인해야하는 작업이 필요하지만, 세션은 매번 저장소에 접근하는 한편 refresh token 은 만료되었을 때만 I/O 가 이루어진다는 점에서 차이가 있다. 이로써 토큰이 탈취되었더라도 refresh token 저장소를 삭제할 수 있다는 점에서 보안과 속도를 잡을 수 있다. 

## RESTful API

REST(REpresentational State Transfer)는 자원에 대한 주소를 지정하는 방법 등 네트워크 아키텍처 원리의 모음이다. HTTP URI 로 자원을 명시하고, HTTP Method 를 통해 해당 자원에 대한 CRUD 를 적용하는 자원 기반의 구조(ROA, Resource Oriented Architecture) 설계이다. 자원이 있는 쪽이 Server, 요청하는 쪽이 Client 가 되는데, 클라이언트의 요청이 서버에 저장되어서는 안되는 무상태(stateless)가 중요한 특징이다. 서버는 각각의 요청을 별개의 것으로 처리하기에 일관적이고 서비스의 자유도가 높아진다.

* 제약조건들: Clinet-Server, Stateless, Cache, Uniform Interface
* Uniform Interface: 
    * Resource-Based & Manipluation Of Resources Through Representations: URI로 지정한 리소스를 Http Method를 통해서 표현하고 구분한다
    * Self-Descriptive Message: API 문서가 REST API 응답 본문에 존재해야 한다
    * HATEOAS(Hypermedia As The Engine of Application State): Hypermedia (링크)를 통해서 애플리케이션의 상태 전이가 가능해야 한다. 응답메세지에 링크를 넣어서 구현한다.

* RPC(Remote Procedure Call): hypertext 가 없어서 navigate 할 수 없는 api 는 restful 하지 않다. 단순히 앱의 함수를 실행할 뿐이다. [참고](https://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)

* HAL(Hypertext Application Language): 자원("data") 부분과 링크("_links") 부분을 나누어 응답하는 api 규칙. Newer clients may take advantage of the new links, while legacy clients can sustain themselves on the old links. This is especially helpful if services get relocated and moved around. As long as the link structure is maintained, clients can STILL find and interact with things. [참고](https://stateless.group/hal_specification.html)

https://wonit.tistory.com/454

## CORS(Cross-Origin Resource Sharing)

웹 생태계에는 다른 출처로의 리소스 요청을 제한하는 것과 관련된 두 가지 정책이 존재한다. 한 가지는 이 포스팅의 주제인 CORS, 그리고 또 한 가지는 SOP(Same-Origin Policy)이다. 몇 가지 예외 조항을 두고 이 조항에 해당하는 리소스 요청은 출처가 다르더라도 허용하기로 했는데, 그 중 하나가 “CORS 정책을 지킨 리소스 요청”이다. 

브라우저는 요청 헤더에 Origin이라는 필드에 요청을 보내는 출처를 함께 담아보낸다. 브라우저는 자신이 보냈던 요청의 Origin과 서버가 보내준 응답의 Access-Control-Allow-Origin을 비교해본 후 이 응답이 유효한 응답인지 아닌지를 결정한다.

https://evan-moon.github.io/2020/05/21/about-cors/