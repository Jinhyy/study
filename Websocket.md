# Reactive Websocket
## 채팅용 웹소켓 서버 설계 시 고려해야 할 점
### Server
1. 세션 인증
    - Http 요청을 통한, 웹 소켓 프로토콜로의 업그레이드 로직을 수행할 수 있어야 한다.
    - 클라이언트 측으로 부터 받은 Session Secret Key에 대한 인가처리
2. 세션 관리
    - 세션정보를 가지고 있는 객체는 여러 스레드에서 동시에 접근 될 수 있으므로, critical section로 접근되어야 한다. ( lock or mutex 필요 )
    - (자바 내 ConCurrent 패키지 자료구조 사용하면 멀티 스레드 환경에서 안전하게 동작.)
3. 클라이언트의 액션을 동일한 그룹 ( 채팅방 ) 에게 전송해주어야 한다.
    - Hot flux / Subscriber, Publisher 사용

---
## Websocket API
### Common
1.  WebsocketSession
    - 웹 소켓 세션 구현체. ( 세션 정보 조회, 세션 조작 )
    - getId() / close() : 세션 닫기
    - 메시지 송신 : send() / binaryMessage() / textMessage()
    - 메시지 수신 : receive()
### Server
1. WebsocketHandler
    - WebsocketClient가 execute 메소드를 보냈을 시 handleling. ( handle() 메소드 내부에서, receive 및 send 구현 필요 )
2. WebsocketHandlerAdapter
    - http 으로 Connect:Upgrade 요청에 대하여, handshake 및 프로토콜 업그레이드 처리. ( RequestUpgradeStrategy를 인자로 사용 )
3. RequestUpgradeStrategy 
    - 인터페이스 내 upgrade를사용하여 handshake 정보들을 이용할 수 있다.
### Client
1. WebsocketClient
    - 세션 생성
      - execute(uri, handler) : 지정된 uri에 handshake 수행 및 handler로 생성된 session 을 넘겨준다.
    - 



