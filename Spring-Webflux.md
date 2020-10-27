# Spring Webflux

## 소개
1. 개요
   - Spring 5.0 에서 새로등장한 Web 리액티브 프로그래밍 API를 제공하는
     프레임워크.
---
2. 특징 및 용도
   - 비동기 - 논블록킹 리액티브 개발 ( 스레드 / CPU 메모리 자원 사용을
     극대화 하기 위함 )
   - 서비스간 호출이 많은 MSA 구조에 적합
---
3. 개발방식
   1. 기존의 @MVC 방식과 유사한 방법을 제공 ( @Controller,
      @RequestMapping, ... )
   2. 새로운 함수형 모델
      1. Annotation을 사용하지 않고, 명시적인 함수 정의로 웹개발을 할수
         있게 해줌.( RouterFunction, HandlerFunction, ... )
      - Router Function : 함수형 스타일의 웹 매퍼, 요청 매핑.
      - Handler Function : 함수형 스타일의 웹 핸들러, 요청 바인딩 / 핸들러
        실행 / 응답 생성.
      - (Example) Webflux로 함수형 Hello/{name} 작성
      ```java
          // Router Function 작성
          RouterFunction router = req -> RequestPredicates.path("/hello/{name}").test(req) ? Mono.just(helloHandler) : Mono.empty();  
            
          // Handler Function 작성
          HandlerFunction helloHandler = req -> {
              String name = req.pathVariable("name"); // 요청 바인딩
              Mono<String> result = Mono.just("Hello " + name);   // 요청 처리
              Mono<ServerResponse> res = ServerResponse.ok().body(result, String.class);   // 응답 생성
              return res;
          }

          혹은

          HandlerFunction helloHandler = req -> ok().body(fromObject("Hello" + req.pathVariable("name")));
          
          // Router Function + Hadler Function 결합
          RouterFunction router = RouterFunction.route(RequestPredicate.path("/hello/{name}"), req -> ServerResponse.ok().body(fromObject("Hello" + req.pathVariable("name")));)
      ```
      -

      2. 기존의 서블릿 스택과 API에서 탈피 ( 서블릿 API는 리액티브 함수형
         스타일에 적합하지 않으므로 )
         - 삭제 : HttpServletRequest, HttpServletResponse
         - 추가 : ServerRequest, ServerResponse

---
4. Webflux 함수형 스타일의 장/단점
   - 장점 : 모든 웹 요청 처리 작업을 명시적인 코드로 작성
      - 기존방식의 불편함 해결 : RequestMapping을 달고, Param 타입에 따라 변수에 변환하여 받고, ... 결과를 리턴하는 일종의 관례에 따라 명령형으로 코딩 해야함..
      - 선언적인 코드 작성가능
      - 명확한 타입체크
      - 선언적인 코드로 체이닝해 나가기 때문에 자신만의 프레임워크를 만들 수 있음.
      - 테스트 작성 편리함 : 핸들러 로직/요청매핑을 모두 함수형 스타일로 로직 작성 편리.( 기존 Mock처럼 모든 Bean을 미리 구현하지 않아도됨) 

   - 단점 : 함수형 스타일 코드 작성에 대한 학습곡선..
   
---
5. Webflux와 성능
   - 성능을 올리기 위한방법 :블로킹IO를 사용하는 코드를 최대한 지양해야 한다. ( HTTP API 호출 / DB Access / 기타 네트워크 등... )
      1. DB Access 성능개선 방법
         - 현재는, JDBC API 자체가 전부 블로킹 메소드이다.. 현재는 답이없음.
         - 그나마, @Async 비동기호출과 결과를 CFuture로 받아와서 최소한 데이터를 받아오는 동안은 스레드를 점유하지 않도록 하는 방법이 있다.
         - NOSQL은 일부 Nonblocking 지원:  MongoDB / Cassandra / Redis
      
      2. HTTP API 성능개선 방법
         - AsyncRestTemplate 구현체 사용 ( 요청, 응답을 Mono/Flux로 처리할 수 있는 WebClient )
      
      3. 정리
         - Webflux + 리액티브 리포지토리 / 리액티브 API 호출 / @Async 블로킹 IO

---
6. Webflux와 관련된 주요 개념
   - 자바8 + 함수형 프로그래밍
   - CompleteableFuture 같은 비동기 작업의 조합,결합에 대해 뛰어난 툴의 사용법
   - ReactorCore ( Mono/Flux, 오퍼레이터, 스케줄러 )
   - WebFlux + 스프링 리액티브 스택 ( 리포지토리, ... )
   - 비동기 논블로킹 성능과 관련된 모니터링 / 디버깅 방법 연구


> Study 출처 : 유튜브 https://www.youtube.com/watch?v=2E_1yb8iLKk&t=2171s

