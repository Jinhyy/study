# 네트워크 

# TCP 프로토콜
1. TCP 프로토콜의 구조
    > <a href="#"><img src="http://www.ktword.co.kr/img_data/1889_1.JPG"></a>
    - 개요 : Transmission Control Protocol, 네트워크 내 데이터 교환을 안정적, 순서대로, 에러없이 전송할 수 있는 프로토콜
    - 구조( 20 ~ 60 Byte )
        - Sourse/Destination Port Number (각 16bit) : 송, 수신포트
        - Sequence Number (32)
            - SYN 플래그가 (1)로 설정된 경우, 초기 시퀀스 번호. 실제 데이터의 최초 바이트 값과 ACK 번호는 이 값에 1을 더한 값이 된다.
            - SYN 플래그가 (0)으로 설정된 경우, 현재 세션 데이터의 최초 바이트 값에 대한 누적 시퀀스 번호이다.
        - Ack Number (32)
            - ACK 플래그가 설정된 경우, 수신자가 예상하는 다음 시퀀스 번호.
        - Offset( 4 ) : 헤더의 총 길이
        - Reserved( 3 ) : 예약된 필드로 
        - TCP Flags (9 비트, N/C/E/U/A/P/R/S/F) : TCP 통신 동작 종류를 알려주는 플래그.
            - U : 긴급비트, Urgent pointer 필드의 값이 유효함을 나타낸다.
            - A : 승인비트, Acknowledgment 필드의 값이 유효함을 나타낸다.
            - P : 밀어넣기 비트, 애플리케이션에 버퍼링된 데이터를 푸시해 줄지 여부를 질의하는 역할을 한다.
            - R : 리셋비트, 연결문제등이 발생하였을 때 현재 연결관계를 리셋하자고 알려주는 비트.
            - S : 동기화비트, 상대방과 연결을 시작할 때 사용(SYN)
            - F : 연결해제 비트, 연결 끊을 시 사용(FIN)
        - Window Size(16): 얼만큼 데이터를 더 보내도 되는지 (Tcp Socket Buffer 여유 공간 크기를) 알려주는 필드. 최대 64K
            - 슬라이딩 윈도우: 통신의 원할함 정도에 따라, 윈도우 사이즈를 늘리거나 줄이는 개념.
        - CheckSum(16) : 헤더 및 데이터의 에러 확인을 위해 사용
        - Urgent Pointer(16) : Tcp Flags가 U일때, 긴급한 데이터의 시작주소를 담고 있다.
        - Options( 0~320bit, 가변필드) : Offset 헤더 정보에 따라, 크기가 0일수도, 320일수도 있는 공간.
        
2. TCP 통신과정
    1. 연결 생성
        1. SYN: 클라이언트가 서버에게 SYN 메시지를 보낸다. 이 메시지에 포함된 시퀀스 번호는 클라이언트가 임의로 설정한 값 A.
        2. SYN-ACK: 서버가 클라이언트에게 SYN-ACK 메시지로 응답한다. 이 메시지에 포함된 시퀀스 번호는 서버가 임의로 설정한 값 B, 응답 번호는 (A + 1).
        3. ACK: 클라이언트가 서버에게 ACK 메시지를 보낸다. 이 메시지에 포함된 시퀀스 번호는 (A + 1), 응답 번호는 (B + 1).
    
    2. 자료 전송 ( 예시 )
        1. 최초 송신 클라이언트 측 : 플래그 ( PUSH + ACK ) / 시퀀스, 응답번호 ( 연결 수립시 사용한거 그대로 ) + 데이터
        2. 서버 측 : 플래그 ( PUSH + ACK ) / 시퀀스번호 = 전달받은 응답번호, 응답번호 = ( 받은 시퀀스 번호 + 받은 데이터 크기 ) + 데이터
        3. 서버 응답 데이터 받은 클라이언트 측 : 플래그 ( ACK ) / 시퀀스 = ( 받은 응답번호 + 받은 데이터 크기 ), 응답번호 = 받은 시퀀스 번호 
    
    3. 연결 해제
        1. [Client(FIN_WAIT1) -> Server(CLOSE_WAIT)] FIN+ACK
        2. [Client(FIN_WATI2) <- Server(CLOSE_WAIT)] ACK
        3. [Client(TIME_WAIT) <- Server(LAST_ACK)] FIN+ACK
        4. [Client(TIME_WAIT) -> Server(CLOSED] ACK
   
3. TCP 상태전이도
    > <a href="#"><img src="https://image3.slideserve.com/7080583/slide10-l.jpg"></a>
     1. 클라이언트 측
        1. SYN_SENT : 클라이언트 측에서, 연결 수립을 시도한 상태, ( active open )
        2. ESTABLISHED : 연결이 수립된 상태.
        3. FIN_WAIT1 : 연결해제 요청한 상태
        4. FIN_WAIT2 : 연결해제 요청후, 서버측 응답 기다리고 있는 상태.
        5. TIME_WAIT : 지연됬을 수 있는 응답데이터를 위해 기다리고 있는 상태.
        
     2. 서버측
        1. LISTEN : 클라이언트의 요청을 기다리고 있는 상태. (passive open)
        2. ESTABLISHED : 연결이 수립된 상태.
        3. SYN_RCVD : 연결 요청 받은 상태.
        4. CLOSE_WAIT : 클라이언트 측으로 부터 연결해제 요청 받은후, 종료되기를 기다리는 상태
        5. LAST_ACK : 연결 종료 후 클라이언트측으로 부터 최종 ACK 기다리는 상태
        6. CLOSED : 완전히 연결이 종료된 상태
>
4. TCP 통신 분석
    1. tcpdump ( Linunx )
        - 예제
            - tcpdump -i eth0 => eth0 이더넷 덤프
            - tcpdump -i eth0 -w dump_test.log => eth0 이더넷 덤프를 dump_test.log 파일로 저장
            - tcpdump -r dump_test.log => dump_test.log 덤프 파일을 확인
            - tcpdump -i eth0 -c 10 => eth0 이더넷 패킷 10개만 덤프
            - tcpdump -i eth0 tcp port 80 | grep "GET /"=> eth0 이더넷 80포트에 /GET 으로 들어온 패킷 덤프
            - tcpdump -i eth0 tcp port 8080 and host 192.168.0.100 => eth0 이더넷의 ip는 192.168.0.100 port는 8080 의 패킷을 덤프
        - [ 주요 옵션]
            1. -i (이더넷명)
            2. -w : 덤프 패킷 헤드를 지정할 파일명
            3. -r : 덤프 패킷 헤드를 지정한 파일 확인
            4. -c : 캡쳐 갯수
            5. -s 1500 : 패킷의 전체길이 의미(모든 패킷 캡쳐)
            6. port : 덤프할 포트 지정
            7. host : 덤프할 host 지정
            > 출처: https://arrkaize86.tistory.com/entry/리눅스-tcpdump-명령어 [송군함대]

# HTTP 프로토콜
1. HTTP 프로토콜 개요
    1. HTTP 1.0 : 거래 당 매번 TCP 연결수립 후 해제
    2. HTTP 1.1 : 연결 유지 기능 추가. ( Connection 헤더, Keep-Alive )
    3. HTTP 2.0 : 
    4. 요청 및 응답 프로토콜 구조
    > <a href="#"><img src="https://lh3.googleusercontent.com/proxy/HfNBiweGakLdsNa0zHbp1NAXQ8yn2syyfT_gPCG_wol9ed1jp0N1dHfla3DwIMVYVHPh0Imr6GbKoQEcehod7idb8A"></a>
   
2. 요청 프로토콜
    > <a href="#"><img src="https://lh3.googleusercontent.com/proxy/I-a1XXsN92z6PxvyJuxc_OJOEZB4yXM4Je6_ytGRhILjOKbZVFPJuD0LLkXW0ivvrOShZF7cPcE0P3_6uj5jus6-ISNCXvL2t-p0QUbzzQgJzMMtCQsQ5kt-GDK8gNBNK4Ig1Lc1hwkGF7MoWy3aRsyRDLwSO1nnSXXIk_feXPND9fad9MkK"></a>
    1. Request Line: METHOD / 공백 / URL / 공백 / HTTP 버전
    2. HEADER ( 일반, 요청 , 항목 헤더 )
    3. 공백
    4. BODY

3. 응답 프로토콜
    > <a href="#"><img src="https://media.vlpt.us/images/dnjscksdn98/post/42caeb0f-83f0-41e3-bfc7-ad169dbed518/http_response.png"></a>
    1. Status Line : HTTP버전 / 공백 / 상태코드 / 공백 / 상태문구 
    2. HEADER ( 일반, 응답 , 항목 헤더 )
    3. 공백
    4. BODY

4. HTTP 헤더
    1. 일반 : DATE , Connection ( TCP 커넥션 연결유지 여부 ), Cache-Control ( 캐시 수명, Etag-캐시ID ), Pragma, Trailer
    2. 항목 : Content-Type, Content-Language, Content-Dispoistion ( 응답 Body를 어떤식으로 표시 할 것인가 )
    3. 요청 : Host(1.1에서 필수), User-Agent, Cookie, Referer( 직전에 머물렀던 웹링크 주소 ), Authorization, Origin ( 요청 시작 주소 )
    4. 응답 : Server, Set-Cookie ( 서버측에서 클라이언트에게 세션쿠키 정보 설정 ), Expires ( 리소스 유효기간 ), Allow ( 허용메소드 리스트 ), Access-Control-Allow-Origin( 서버측에서 이곳에 프론트단 주소를 적어야 CORS 에러 발생 X )