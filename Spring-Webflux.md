# Spring Webflux

## 소개

1. 개요
   - Spring 5.0 에서 새로등장한 Web 리액티브 프로그래밍 API를 제공하는
     프레임워크.
2. 특징 및 용도
   - 비동기 - 논블록킹 리액티브 개발 ( 스레드 / CPU 메모리 자원 사용을
     극대화 하기 위함 )
   - 서비스간 호출이 많은 MSA 구조에 적합

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


