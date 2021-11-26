# Rsocket

**Rsocket**

다중화 양방향 소통을 위한 어플리케이션 프로토콜

\


* **Rsocket의 주요 모델**

1. Request-Response : 메시지 하나 전송 후 메시지 하나 응답으로 받음
2. Request-Stream : 메시지하나를 전송하고 메시지 스트림을 돌려 받음
3. Channel : 양방향 메시지 스트림을 전송한다
4. Fire-and-Forget : 메시지를 한번만 전송한다
5.
   1. —> 전송에 쓰인 세션이나 소켓은 어떻게 되는가? ( 사라질듯? but 연결을 맺는건가? 연결이 맺어진 채널에 신규세션을 생성해서 넣는다는건가?
   2.

한번 커넥션을 맺은순간 부터는, 클라이언트 ( 최초 커넥션 요청한 서버 ) 와 서버간 구분이 사라진다.( 양쪽다 클라이면서 서버이다 )

커넥션 이후, 두 서버모두 요청/응답을 할 수 있기에 요청마다 requester 와 responder 가 달라질 것이다. ( 클라는 계속 요청만 하는 주체라고 해석하고 있는 듯 하다 )

\


\


\


* **특징**

1. 요청제한

Channel, Request-Stream 같은 리액티브 스트림스의 특징( 논 블로킹 + backPressure ) 을 가진 요청에서는

requester와 responsder가 서로의 속도를 제한할 수 있는 backPressure 신호를 주고 받는다. -> 임시 대기 큐인 버퍼의 필요성이 떨어짐

1. **세션 재개**

세션의 커넥션이 종료되어도 상태를 유지해준다.(상태라는게 무엇일까?, )&#x20;

backPressure와 함께 사용시 state를 최소로 유지한다 ( —> backPressure로 메모리관리 등을 할수 있으니, 메모리관리나 상태유지에 사용하는 state가 적게 필요하다는 의미로 보임 )

\


1. **큰 메시지 단편화 및 재조립.** -> 큰 메시지를 나누어서 보내고, 나누어진걸 재조립하여야 할 때 , 그런것을 직접 구현할 필요없이 API가 있다는 뜻으로 보임.
2. KeepAlive (heartBeats) -> 커넥션 맺어진 대상들의 상태를 계속 확인한다는 의미인듯
3. 자바 에서는 Roskcet이 Reactor를 사용하고 있기 때문에, Publisher로 신호를 보낼 시 Rsocket을 통해 투명하게 전파된다 ( —> 투명하게? 가 무슨뜻일까.. )

\


\


* **프로토콜**

1. Connecting, 최초 Connect 요청 과정&#x20;
2.
   1. 클라가 서버로 커넥션 요청 (websocket or tcp) + SETUP 프레임 전송 (커넥션시 필요한 파라미터)  ( —> 커넥션할떄 어떤 파라미터가 필요할까? )   &#x20;
   2. 서버에서도 SETUP 프레임 클라에게 전송 후 Connection 완료.
   3.
      1. SETUP 프레임 안에 요청수를 제한하는 lease 플래그가 있다면, 클라와  한번더 LEASE 프레임을 주고 받는다.
3. Making Requests, 커넥션을 맺은 후 요청 주고받기&#x20;
4.
   1. Requestser가 위의 4개의 모델 프레임중 하나로 요청을 시작한다. ( RR - RS - RC - FNF )
   2. Responder는 응답 메시지를 PayLoad 프레임에 담아 보낸다.
   3.
      1. 전달받은 Request가 REQUEST\_CHANNEL 이었다면, Rquester가 다른 요청메시지를 Payload 프레임에 담아 전송한다.
      2. REQUEST\_CHANNEL, STREAM의 경우 Requester가 자기가 응답받을 수를 (demand) 프레임에 담아 responder 보낸다. 그만큼 응답을 받은 후에는 , REQUEST\_N 프레임으로 몇개가 필요한지 또 responder에게 Request를 보낸다. &#x20;

\


* **Java Implementation**

Rsocket은 Reactor로 구현되있고, TCP/웹소켓 전송은 Reactor Netty를 사용.

단일 메시지의 경우는 mMono

메시지 스트림일 경우는 Flux로

ByteBuffer나 메시지는 io.rsocket.Payload 구현체로 모델링 한다.

요청/응답시에는 해당 Rsocket구현체를 만들어 처리한다 ( —> RSOCKET구현체에 필요한것은 무엇일까? )

\


\[Connect 요청 예시]

RSocketRequester requester = RSocketRequester.builder()

&#x20; .connectTcp("localhost", 7000)

&#x20; .block(Duration.ofSeconds(5));

\


\


* **Spring Support**

RsocketRequester : Rsocket으로 요청을 만들고, 데이터/메타데이터를 객체로 인코딩/디코딩 할 수 있는 API

Annotated Resoinder : @MessageMapping을 사용해서 특정 요청에 대한 핸들링 할 수 있는 Rsocket 응답 구현체

\


\


* **ClientRequester Requester**

클라이언트 사이드에서 사용한다면, 서버로 SETUP 프레임을 통하여 커넥션을 요청한다.

\


\[RequesterBuilder]

RsocketRequester.Builder를 통해 SETUP 프레임에 다음과 같으 내용을 설정할 수 있다.

\-> dataMimeType / metadataMimeType / setupData ( 실제 전송할 데이터 ) / setupRoute(라우팅관련 메타데이터) / setupMetaData ( 메타데이터 ) / RsocketStrategies (routeMatcher,인코더,디코어,metaDataExtractor, reactiveAdapterRegistry 정보) —> (reactiveAdapterRegistry 이거뭐에 쓰이는거지..?)

\


커넥션에 필요한 보안 정보의 경우 위의 metaData 에 담을 수 있다.

\


\


* **Sever Requester**

\[ 현재 연결된 클라이언트로 요청 보내기 ]

서버에서 현재 연결된 클라이언트로 요청을 보낼시에는 연결된 클라이언트의 requester가 필요하다. 이것을 얻어오려면,

AnnotatedResponder를 사용하여, @ConnectMapping 혹은 @MessageMapping 메소드에서 해당 requester를 인자로 명시하여

자동으로 받아올 수 있다.

단, ConnectMapping 메소드는 요청 시작하기 전에 처리해야하는 SETUP 프레임을 위한 핸들러이다.

( —> ConnectMapping, MessageMapping 모두 요청 payload내 meta데이터 routing 정보를 읽어와서 처리한다. )

\


* **Requesters**

클라이언트나, 서버에서 requester를 얻어왔다면 다음처럼 Making Requests 단계를 시작할 수 있다.

requester.route("locate.radars.within") // (1) 라우팅 정보

&#x20;     .data(viewBox) // (2) 데이터&#x20;

&#x20;     .retrieveFlux(AirportLocation.class); // (3) 이런형태로 응답을 달라.(스트림이나 채널로)

\


* **Server Responders**

Controller빈에 있는 MessageMapping, ConnectMapping을 인식할 수 있도록 RsocketMessageHandler를 스프링 설정에 선언해야 한다.

RsocketMessageHandler는 MessageMappingHandler를 상속한 구현체로

Message,ConnectMapping에 사용되는 routeMatcher, 커스텀 메타데이터 타입 및 커스텀 메타데이터 추출기등, rsocketStrategies, defaultDataMimeType, encoder 등을 설정할 수 있다.

특히 RsocketStrategies는 서버/클라이언트 간에 설정을 공유한다면 유용하다.

routeMatcher에는 default로 _SimpleRouteMatcher_가_ _사용된다_.( _구분자로_ “.” _를_ _사용_ )_

\


* **MetaDataExtractor**

리스폰더는 메타데이터를 각자에 맞는 mimeType으로 읽어와서 해석할 수 있어야한다.

MetadataExtractor는 직렬화된 메타데이터를 받아 디코딩한 후, name-value 쌍을 리턴한다.&#x20;

그러므로, MetadataExtractor는 각 mimeType에 해당하는 decoder를 설정해주어야 한다.

ex) default void metadataToExtract(MimeType mimeType, Class\<?> targetType, @Nullable String name)

\=> mimeType에 해당하는 메타데이터를 targetType으로 바꾸고, 지정한 name 값을 키값으로 저장한다.(name값 없을시 mimeType이 키 값)

\


해당 메타데이터 값은 @MessageMapping내 @Header, @Headers 메소드인자로 추출될 수 있다.&#x20;

\


* **Message Mapping**

MessageMapping으로 활용할 수 있는 메소드 인자

1. @Payload : 요청시 받은 Payload를 가져올 수 있음.&#x20;
2. RsocketRequester : 요청을 보낸 Requester를 가져올 수 있음.
3. @DestinationVariable : 라우팅 정보에서 추출한 변수 값 (@MessageMapping("find.radar.{id}”))
4. Header : 등록한 메타데이터를 추출한 값 ( MetadataExtractor 참고 )
5. Headers : 등록한 모든 메타데이터를 추출한 값&#x20;

\


\


\[MessageMapping 활용 예시]

@Controller

public class RadarsController {

\


&#x20; @MessageMapping("locate.radars.within")

&#x20; public Flux\<AirportLocation> radars(MapRequest request) {

&#x20;     // ...

&#x20; }

}

\


\[RsocketMessageHandler 빈 설정 예시]

@Bean

&#x20; public RSocketMessageHandler rsocketMessageHandler() {

&#x20;     RSocketMessageHandler handler = new RSocketMessageHandler();

&#x20;     handler.routeMatcher(new PathPatternRouteMatcher());

&#x20;     return handler;

&#x20; }

\


\


\


\


\


\


\


\


\
