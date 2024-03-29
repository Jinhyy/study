# Kafka
## 카프카의 탄생배경

-   기존 end-to-end 연결 방식의 아키텍처의 문제점
    
    -   통합, 중앙화된 전송 영역이 없어 end-to-end 연결이 갈수록 복잡해지므로 하나의 프로세스에 여러 데이터 시스템을 확인 해야 한다.
    -   데이터 파이프라인 관리의 어려움, end-to-end를 연결하는 연결선 마저도 end-to-end 마다 제각기 다른 방식으로 구현될 수 있어 복잡도가 증가, 추후에 통합데이터 분석을위해 파이프라인들이 연결되어야 할 때 문제 발생.
    -   이러한 배경에서 카프카는 다음과 같은 목표를 달성하기 위해 탄생하였다.
        -   프로듀서와 컨슈머의 분리
        -   메시징 시스템과 같이 영구 메시지 데이터를 여러 컨슈머에게 허용
        -   높은 처리량을 위한 메시지 최적화
        -   데이터가 증가함에 따라 스케일 아웃이 가능하도록 한다. ( 주키퍼 이용 )
    > <a href="#"><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fdm1kl4%2FbtqAavK4BAR%2FEKvMKV4xYnbkDkSki9gV80%2Fimg.png"></a>

---

## 카프카의 동작 방식과 원리

-   **기존 메시징 시스템은** 메시징 시스템 내부의 교환기의 부하, 컨슈머들의 큐관리를 직접하는 문제, 메시지의 정합성, 전달결과 관리등을 위하여 프로세스가 복잡하고 다양했기 때문에 **많은 양의 데이터를 관리할 수 없었다**.
-   **카프카**는 이러한 성능의 단점을 극복하기 위하여 메시지 교환 전달의 **신뢰성 관리를 프로듀서, 컨슈머 쪽으로 넘기고**, 부하가 많이 걸리는 교환기 기능 역시 컨슈머가 만들 수 있도록 하여, **메시지 전달 기능에 집중**하였다.
-   카프카의 메시지 전달 순서
    1.  프로듀서가 메시지 생성 후 카프카에 전달
    2.  카프카의 컨슈머 큐(토픽)에 저장 된다.
    3.  컨슈머가 카프카 서버에 접속하여 메시지를 가져간다.

---

## 카프카의 특징

  -   **프로듀서와 컨슈머의 분리**
      -   **pub / sub 모델로 메시지를 보내는 역할과 받는 역할을 완벽하게 분리함.**
  -   **멀티 프로듀서, 멀티 컨슈머**
      -   **하나의 토픽을 여러 컨슈머가, 프로듀서가 생산 및 관리할 수 있음.**
  -   **디스크에 메시지 저장**
      -   **메시지를 해당 메시지의 보관 주기 동안 디스크에 저장.**
      -   **Zero-copy 방식, 페이지 캐시 처리 등으로 디스크에 저장하지만 빠르게 처리**
          - Zero-Copy  : 디스크에서 소켓으로 데이터를 복사할때 커널레벨에서 데이터를 복사하는 방식( 카프카 브로커 )
          - 페이지 캐시 : 앞으로 필요할 것으로 예상되는 데이터를 디스크로부터 미리 읽어 들여 캐시하는 방식( 카프카 브로커 )
          > <a href="#"><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb5LuZG%2Fbtqz8oAxvmc%2FKNH9w16uxn19IfsZSoUlk1%2Fimg.gif"></a>
  -   확장성
      -   간편하고 무중단 스케일아웃(6장에서 자세히 다룸)
  -   배치 처리
      -   카프카에서는 작은 I/O가 빈번하게 일어나기 때문에 이러한 I/O들을 묶어 배치로 처리하는 방식을 취한다.(ex: 메시지 4개를 모은 후 한번만 I/O 작업을 한다.)

## 카프카 데이터 모델

-   **토픽의 이해**
-   **파티션의 이해**
    -   토픽을 분할한 것
    -   EX) 4개의 토픽을 보내는 상황에서 파티션이 1개라면, A->B->C->D 순서로 보내기 때문에 총 4초가 걸리는 반면, 프로듀서와 파티션을 늘려서 4개의 프로듀서가 각각 1개의 파티션에 메시지를 보내면, 4개의 메시지가 보내진다고 해도 1초밖에 안걸리게 된다.
-   **파티션 확장의 단점**  
    -   파일 핸들러의 낭비
        -   각 파티션은 카프카 브로커의 디렉토리에 연결되어 있고, 데이터마다(인덱스와 실제 데이터) 파일이 있으므로, 파티션이 많으면 많아질 수록 많은 파일 핸들을 열어 두게 되고, 결과적으로 안열어도 될 파일 리소스를 열게 되므로 리소스를 낭비할 수 있다.
    -   장애 복구 시간 증가
        -   만약 A 브로커가 다운 시, A브로커가 담당하고 있던 1000개의 파티션이 있다고 하면, 해당 파티션들에 대해 리더 선출을 다시 해야하므로, 장애복구에 1000 X (리더선출시간) 만큼의 걸리게 된다.
-   내 토픽의 적절한 파티션 수는
    -   프로듀서가 카프카에 보내는 메시지 양/속도와 컨슈머에서 처리하는 메시지 양/속도를 고려하여 프로듀서양을 고려하여 적절하게 파티션 수와, 컨슈머/프로듀서 수를 조정해야 한다.
    -   토픽을 8개의 파티션으로 나누어, 8개의 프로듀서가 5메시지/초 보낸다면 40메시지/초 가걸리고, 컨슈머가 처리하는 속도를 고려하여 컨슈머 수를 조정해야 한다.
-   오프셋과 메시지 순서
    -   카프카에서는 메시지가 저장되는 위치를 오프셋 이라고 하며, 파티션마다 고유의 오프셋이 존재하고, 오프셋은 순차적으로 증가하는 숫자 형태로, 프로듀서가 해당 파티션에 쓸 때는 쓰기 오프셋 위치부터 쓰게 되고, 컨슈머가 해당 파티션을 가져 갈 때는 읽기 오프셋 위치부터 순차적으로 가져가게 된다.

---

## 카프카의 고가용성과 리플리케이션

-   **리플리케이션 팩터와 리더, 팔로워의 역할**
    -   카프카의 리플리케이션 기능
        -   카프카의 리플리케이션은 토픽이 아니라, 토픽을 이루는 각각의 파티션을 리플리케이션 한다.
        -   리플리케이션 팩터를 지정하여 파티션들을 여러 서버에 복제하고, 하나의 브로커가 죽어도 다른 서버에서도 이미 복제하여 데이터를 갖고 있기 때문에 장애를 대비할 수 있다.
        -   해당 파티션의 리더가 죽었을 시, ISR이 발생하여 FOLLOWER가 새로운 리더로 자동으로 선출된다.
-   **리더와 팔로워의 관리**
    -   팔로워는 리더의 데이터를 주기적으로 보면서 자신에 없는 데이터를 리더로부터 가져온다.(pull)
    -   하지만, 팔로워가 데이터를 제대로 가져오지 못하여 데이터가 안맞는 상황에서 리더까지 다운된다면?
    -   이러한 문제를 카프카에서는 ISR(In Sync Replica) 개념으로 해결하고 있다.
    -   ISR 동작방식
        1.  프로듀서가 A 메시지를 토픽의 리더에 보낸다.
        2.  A 메시지는 토픽의 리더가 저장.(리더만 해당 토픽 혹은 파티션의 정보를 읽고 쓸 수 있다.)
        3.  팔로워들은 해당 리더 데이터를 주기적으로 확인하며, 확인했다는 확인 응답을 보내고, 리더는 이러한 확인응답을 기반으로 팔로워들의 상태를 감시하고, 응답이 없는 팔로워는 추방당하며, 리더자격도 상실한다.
        4.  리더가 다운될 시, 리더자격이 있는 팔로워중 하나가 리더로 승격하여 작업을 진행하게 된다.

---

## 모든 브로커가 다운된다면

-   **리플리케이션 팩터와 리더, 팔로워의 역할**
    -   모든 브로커 다운 시, 가장 많은 데이터를 가지고 있는 브로커를 살려야 할 것이다, 하지만 서비스 측면에서 봤을 땐 메시지가 일부 유실되더라도, 가장 빨리 복구되는 브로커를 새로운 리더로 선출해야 하는 방법도 있다.
        -   이와 관련된 설정 : unclena.leader.election.enable=false ( 기존 리더가 살아나기를 기다리는 방법) 혹은 true( 새로운 리더를 뽑아서 빠르게 서비스를 재개하는 방법)
-   카프카에서 사용하는 주키퍼 지노드의 역할
    -   zkCli.sh을 실행 후  ls명령어를 사용하여 다음과 같은 카프카와 관련된 디렉토리들을 확인할 수 있다. 
    -   **/kafka/controller** : 카프카는 클러스터 내 브로커중 하나를 컨트롤러로 선정하고, 컨트롤러는 브로커 레벨에서의 실패를 감지하고, 실패한 브로커에 의해 영향받는 **모든 파티션의 리더 변경을 책임**진다. 컨트롤러를 통해 매우 빠르게 배치 형태로 리더십을 변경할 수 있다. 만약 컨트롤러인 브로커가 다운된다면, 남아있는 브로커 중 하나가 컨트롤러가 된다.
    -   **/kafka/brokers** : 브로커와 관련된 정보들이 저장된다. 대표적으로는, 카프카 broker들은 자신의 broker.id를 brokers/ids 디렉토리에 broker.id로 지노드를 작성하여 자기 자신을 등록한다. 이 때, 사용되는 노드는 임시노드로 카프카 클라이언트와 주키퍼의 연결이 해제 될 시, 자동으로 삭제되는 노드이다. 또 한, **/brokers/topics 디렉토리에서 토픽에 대한 정보(ISR, 파티션 수, 리더정보) 를 확인**할 수 도 있다.
    -   **/kafka/consumers **
        -   컨슈머가 각각의 파티션에 대해 어디까지 읽었는지를 기록하는 오프셋 정보 저장.
        -   오프셋 정보는 지워지면 안되기 때문에 영구노드로 저장한다.
        -   하지만, **0.9버전 이상부터는 카프카는 컨슈머 오프셋을 자신의 토픽으로 저장하여 사용한다.(\_consumer\_offesets)**
    -   **/kafka/config**
        -   토픽, 유저, 컨슈머, 프로듀서 등의 상세 설정을 확인.

---
# 프로듀서
---
## 프로듀서 주요 옵션

-   bootstrap.servers
    
    -   카프카 클러스터는 기본적으로 마스터가 없는 구조이므로 클러스터 내 모든 서버가 클라이언트의 요청을 처리 할 수 있다. 그러므로, 하나의 서버가 장애가 나도 클라이언트는 자동으로 다른 서버로 재접속 한다.
-   **acks**(default: 1)
    -   프로듀서가 토픽 혹은 파티션의 리더에게 메시지를 보낸 후 요청을 완료하기 전 ack의 수. 
        -   0인 경우 : 서버로부터 완료됬다는 ack를 기다리지 않음
        -   1인 경우 : 리더에 데이터가 기록되었는지만 확인
        -   all 또는 -1 인 경우 :: 리더 뿐만 아니라 모든 팔로워로부터 데이터 기록 응답을 기다림.
-   buffer.memory(default: 33554432 byte) : 프로듀서가 카프카 서버로 데이터를 보내기 전, 잠시 대기하는 메모리 바이트.(**딜레이**)
-   **batch.size**(default: 16384) : 같은 파티션으로 보내는 여러 데이터를 배치로 보낼 때 사용하는 크기.
    -   정의된 batch.size 보다 메시지 크기가 클 때는 바로 해당 메시지를 보내게 된다.
    -   하지만, **batch가 쌓이던 중, client가 장애가 났을 시 배치내 메시지는 전달되지 않는다**.
-   **linger.ms**(default : 0, 지연없음) : 배치로 쌓인 메시지를 보내기 전, 기다리는 시간. 하지만, 배치 사이즈가 이미 꽉찼을 경우엔 해당 옵션과 상관없이 바로 메시지를 보낸다. 이 옵션은, 메시지들이 배치 크기만큼 쌓이지 않아도 해당 메시지를 보내는 최대 대기시간을 설정한다.
-   max.request.size(1MB) : 최대 메시지 바이트 사이즈
-   compression.type(default: none) : 데이터 압축 형식 지정
-   retries(default: INT\_MAX) : 전송 실패한 데이터를 다시 보내는 횟수
-   **프로듀서의 acks=all과 브로커의 min.insync.replicas 옵션**
    -   프로듀서가 acks=all 옵션으로 브로커로 메시지 전송
    -   브로커는 min.insync.replicas 옵션에 설정된 숫자 만큼 복제가 이뤄졌는지 확인 후 acks 응답을 보낸다.

---
# 컨슈머
---
## 컨슈머 주요 옵션

-   bootstrap.servers
    
    -   카프카 클러스터의 주소
-   fetch.min.bytes (df: 1 bytes)
    -   한번에 가져올 수 있는 최소 사이즈로, 만약 가져오는 데이터가 지정한 사이즈보다 작으면 요청에 응답하지 않고, 데이터가 누적될 때 까지 기다린다.
-   fetch.max.wait.ms(df: 500sec = 0.5sec) : fetch.min.bytes에 설정된 데이터보다 데이터 양이 적은 경우 요청에 응답을 기다리는 최대시간을 설정
-   group.id: 컨슈머가 속한 컨슈머 그룹의 id
-   enable.auto.commit (df: true)
    -   백그라운드로 주기적으로 오프셋을 커밋할지 안할지 설정하는 옵션
-   auto.commit.interval.ms (df: 5000ms = 5sec) : 주기적으로 오프셋을 커밋하는 시간
-   auto.offset.reset (df: latest) 
    -   카프카에서 초기 오프셋이 없거나, 현재 오프셋이 더 이상 존재하지 않는경우 다음 옵션으로 리셋한다.
        -   earlist: 가장 초기의 오프셋 값으로 설정
        -   latest: 가장 최근의 오프셋 값으로 설정
        -   none: 이전 오프셋값을 찾지 못하면 에러를 발생시킵니다.
-   fetch.max.bytes (df : 52428800 bytes = 50mb) : 한번에 가져올 수 있는 최대 데이터 사이즈
-   request.timeout.ms(df: 30000ms = 30sec) : 요청에 대한 응답을 기다리는 최대 시간으로, 지정한 시간만큼 요청에 대한 응답이 안오면 retry 한다.
-   session.timeout.ms(df: 10000ms = 10sec)
    -   컨슈머와 컨슈머 해당 그룹 코디네이터(컨슈머 내 백그라운드 인스턴스) 사이의 세션 타임 아웃 시간으로, 그룹 코디네이터가 해당 컨슈머가 살아있는지 판단하는 시간.
    -   컨슈머가 그룹 코디네이터에게 하트비트를 해당시간만큼 보내지 않으면, 해당 컨슈머는 장애가 생겼다고 판단하여 컨슈머 그룹은 리밸런스(rebalanace)를 시도한다.
-   heartbeat.interval.ms(df: 3000ms = 3sec)
    -   그룹 코디네이터에게 얼마나 KafkaConsumer.poll() 메소드를 통하여 하트비트를 보낼 것인지 조정한다.
    -   일반적으로, session.timeout.ms의 1/3 정도로 설정한다.
-   max.poll.records(df: 500) : 폴링루프에서 이뤄지는 한건의 KafkaConsumer.poll() 메소드에 대한 최대 레코드수를 조정한다. 
-   max.poll.interval.ms(df: 300000ms = 30sec) : 컨슈머가 하트비트를 보냄에도 불구하고, poll을 하지 않으면 장애이기 때문에 poll 주기를 설정하여 장애를 판단하는데 사용한다. 해당 옵션보다 poll 주기가 길었을 경우 컨슈머 그룹에서 제외한 후, 다른 컨슈머가 해당 파티션을 처리할 수 있도록 한다.

---

## 파티션과 메시지 순서

-   카프카는 파티션이 여러개인 경우 라운드 로빈 형식으로 파티션에 데이터를 분산 저장하고, 컨슈머에서 해당 데이터를 불러올 때, 파티션1의 데이터 / 2의 데이터 , ... 이런 순서대로 불러오기 때문에 메시지의 순서가 보장되지 않는다.
    
-   메시지의 순서를 보장하려면 파티션을 1개만 사용해야 하는데, 이런 경우 분산저장이 힘들고 **컨슈머그룹 안의 하나의 컨슈머만 파티션과 연결 할 수 있기 때문에 처리량이 낮다.**

---

## 컨슈머 그룹

-   카프카는 컨슈머 그룹이라는 개념을 통해, 하나의 토픽을 여러개의 컨슈머 그룹이 다른 방식으로 처리할 수 있다.
-   카프카는 컨슈머 그룹에 리밸런스라는 개념을 통하여, 컨슈머에 장애가 발생했을 시 다른컨슈머로 해당 작업을 자동으로 위임한다. 이 때, 장애가 발생함을 알기 위해서 주기적으로 컨슈머에서 브로커로 보내는 하트비트와 poll() 메소드 등의 주기를 감시한다.
-   하나의 컨슈머가 너무 많은 파티션을 담당하고 있는지 확인하는게 필요하다.

---

## 커밋과 오프셋

-   컨슈머는 각각의 파티션에 대하여 자신이 가져간 메시지의 오프셋을 저장한다.
-   커밋 : 컨슈머가 처리하고 있는 각 파티션 안에다가 자신의 오프셋을 업데이트 하는 동작
-   자동커밋 - enable.auto.commit 옵션등을 사용하여 설정 할 수 있으며, 정한 주기에 따라서 자동으로 commit 요청을 보내는데, 주의할 점으로는 작업이 처리된 상태이고, 자동 커밋이 되기전 상황에 리밸런스가 되버리면, commit 이 안된 상태이기 때문에 처리했던 작업을 다시 중복으로 처리하게 될 수 있다.
-   수동커밋 - DB에 저장하여 중복이 최대한 없도록 할 때 사용하는데, Consumer 프로젝트 내에서, consumer.commitSync() 라는 메소드를 통하여, 원하는 작업이 끝난 후 commit을 하도록 호출 할 수도 있다. 하지만 ,이 경우에도 DB에 저장은 되고, commit이 되기전 해당 서버가 죽게 된다면 해당 작업을 중복으로 처리 될 가능성은 있다.
-   특정 파티션 할당  
    -   특정 파티션에 할당해야 하는 상황은 아래와 같은 경우가 있을 수 있다.
        -   키-값의 형태로 파티션에 저장되어 있고, 특정 파티션에 대한 메시지만 가져와야 할 때
        -   컨슈머 프로세스가 가용성과 장애복구 능력이 높아서 브로커가 컨슈머의 실패를 감지, 리밸런스할 필요 없고 자동으로 컨슈머 프로세스가 다른 시스템에서 시작되는 경우(YARN, Mescos)
        -   (컨슈머에서 해당 토픽의 특정 파티션만 처리하도록 설정하는 예제)

```
TopicPartition p0 = new TopicPartition(topic, 0);
TopicPartition p1 = new TopicPartition(topic, 1);
consumer.assign(Arrays.asList(p0, p1));
```

-   파티션의 특정 오프셋으로부터 메시지 가져오기

```
consumer.assign(Arrays.asList(p0, p1));
consumer.seek(p0, offset1);
consumer.seek(p1, offset2);
```
---
## 카프카 메커니즘
---
    1. 주키퍼 내 카프카 관련 zNode
    <table style="border-collapse: collapse; width: 100%;" border="1"><tbody><tr><td>/kafka-main/controller</td><td>카프카 클러스터의 컨트롤러 정보가 저장됨</td></tr><tr><td>/kafka-main/brokers</td><td>브로커 ID 정보는 /brokers/ids에 토픽 정보는 /brokers/topics에 저장됨</td></tr><tr><td>/kafka-main/config</td><td>토픽의 설정 정보가 저장됨</td></tr><tr><td>/kafka-main/consumers</td><td>컨슈머의 파티션 오프셋 정보가 저장됨<i>(카프카 버전 0.8 까지만 존재함. 0.9 부터는 __consumer_offsets라는 토픽에 저장됨)</i></td></tr></tbody></table>
    
    2. 주키퍼를 이용해 브로커를 관리하는 흐름
        - 모든 카프카 브로커는 고유 식별자(ID)를 가짐
        - 카프카 브로커는 시작될 때 마다 주키퍼의 /brokers/ids에 임시 노드로 자신의 ID를 등록함
        - 만일 동일한 ID로 브로커를 시작하려고 하면 에러 발생함(ID는 브로커의 구성 파일에서 설정되거나 자동으로 생성됨)
        - 브로커가 추가 혹은 삭제되거나 주키퍼와의 연결이 끊어지면 /brokers/ids에 등록한 임시노드는 자동으로 삭제됨
        - 주키퍼에서 노드가 삭제될 때 watch 기능을 통해 카프카 컴포넌트들은 이를 감지 할 수 있음(리플리카 내역을 통해 중단된 브로커 정보들을 다시 이어갈 수 있음)
    
    3. 컨트롤러
        - 카프카 브로커 중 하나이다
        - 파티션 리더를 선출하는 책임이 있다
        - 클러스터에서 시작하는 첫 번째 브로커가 컨트롤러가 된다

    4. 컨트톨러가 선정되는 방법
        - 클러스터에서 시작하는 첫 번째 브로커는 /controller에 임시노드를 생성.
        - 컨트롤러가 이미 존재할 경우, 컨트롤러가 아닌 브로커들은 /controller 노드에 watch 기능을 걸어 노드의 변경을 감지 한다.

    5. 컨트롤러 fail-over
        - 컨트롤러 브로커가 중단되거나 주키퍼와 연결이 끊어지면 /controller에 생성한 임시노드가 삭제 됨
        - /controller에 watch를 걸었던 다른 브로커들은 이 사실을 알게 되고 /controller에 임시노드 생성을 시도하게 됨
        - 시도에 성공한 친구가 컨트롤러가 되고, 시도에 실패한 친구들은 다시 새로운 controller 노드로 watch를 설정 함
        - 이 때, controller 노드의 세대 번호(epoch)를 함께 저장하게 된다. 따라서 변경 전의 컨트롤러와 혼동되지 않으며, 이전 세대 번호로 된 컨트롤러 메세지를 받으면 무시한다. 
---
## 카프카 스트림즈 API
---
1. 스트림 프로세싱 기초
    1. 스트림 프로세싱
        - 개요 : 지속적으로 유입되는 실시간으로 연속된 데이터에 대해 분석이나 질의를 수행하는 것
        - 장점
            1. 실시간 처리로, 실시간 이벤트들에 빠르게 대응
            2. 저장하지 않고 바로 분석하므로, 데이터베이스에 대한 요구를 줄일 수 있다.
    
    2. 상태기반과 무상태 스트림 처리 방식
        1. 상태기반 처리 방식 : 이전 스트림을 처리한 결과를 참조하며 처리를 하는 방식. 상태 저장소 필요. ( ex: 단어 수 세기, 실시간 추천 프로그램 )
        2. 무상태 처리 방식   : 이전 스트림 결과와 상관없이, 현재 스트림만을 대상으로 처리하는 방식.
        
---
2. 카프카 스트림즈
    1. 개념과 특징
        - 개념 : 카프카에 저장된 데이터를 처리하고 분석하기 위해 개발된 클라이언트 라이브러리.
        - 특징
            1. 간단하고 가벼운 클라이언트 라이브러리
            2. 시스템이나 카프카에 대한 의존성이 없음.
            3. 이중화된 로컬 상태 저장소 지원.
            4. 카프카 브로커 혹은 클라이언트에 장애가 생기더라도, 스트림에 대해 1번만 처리 되도록 보장.
            5. 처리 지연을 줄이기 위해, 한번에 하나의 레코드만 처리.
            6. 스트림 프로세서를 쉽게 만들 수 있는 고수준의 DSL 및 저수준의 API 지원.
    
    2. 카프카 스트림즈 아키텍처
    > <a href="#"><img src="https://img1.daumcdn.net/thumb/R720x0.q80/?scode=mtistory2&fname=http%3A%2F%2Fcfile29.uf.tistory.com%2Fimage%2F99AFC23E5C98CDEA2D64B0"></a>
    3. 카프카 스트림의 용어 및 주요 개념
    <table style="border-collapse: collapse; width: 100%;" border="1" width="784"><tbody><tr><td><p><b>용어&nbsp;</b></p></td><td><p><b>설명</b>&nbsp;</p></td></tr><tr><td><p>스트림(Stream)&nbsp;</p></td><td><p>스트림은 카프카 스트림즈 API를 사용해 생성된 토폴로지로, 끊임없이 전달되는 데이터 세트를 의미한다. 스트림에 기록되는 단위는 key-value 형태이다.&nbsp;</p></td></tr><tr><td><p>스트림 처리 애플리케이션(Stream Processing Application)&nbsp;</p></td><td><p>카프카 스트림 클라이언트를 사용하는 애플리케이션으로서, 하나 이상의 프로세서 토폴로지에서 처리되는 로직을 의미한다. 프로세서 토폴로지는 스트림 프로세서가 서로 연결된 그래프를 의미한다.&nbsp;</p></td></tr><tr><td><p>스트림 프로세서(Stream Processor)&nbsp;</p></td><td><p>프로세서 토폴로지를 이루는 하나의 노드를 말하여 여기서 노드들은 프로세서 형상에 의해 연결된 하나의 입력 스트림으로부터 데이터를 받아서 처리한 다음 다시 연결된 프로세서에 보내는 역할을 한다.&nbsp;</p></td></tr><tr><td rowspan="1"><p>&nbsp;소스 프로세서(Source Processor)</p></td><td rowspan="1"><p>위쪽으로 연결된 프로세서가 없는 프로세서를 말한다. 이 프로세서는 하나 이상의 카프카 토픽에서 데이터 레코드를 읽어서 아래쪽 프로세서에 전달한다.&nbsp;</p></td></tr><tr><td rowspan="1"><p>싱크 프로세서(Sink Processor)&nbsp;</p></td><td rowspan="1"><p>토폴로지 아래쪽에 프로세서 연결이 없는 프로세서를 뜻한다. 상위 프로세서로부터 받은 데이터 레코드를 카프카 특정 토픽에 저장한다.&nbsp;</p></td></tr><tr><td><p>Stateless Transformation&nbsp;</p></td><td><p>들어오는 메시지들 각각에 대해 변환하는 방법(이전상태 활용X)&nbsp;</p></td></tr><tr><td><p>Stateful Transformation&nbsp;</p></td><td><p>Streams가 작동할 때 상태값을 저장해두어서, 해당 상태값을 활용하는 변환 방법.&nbsp;</p></td></tr><tr><td><p>Windowing&nbsp;</p></td><td><p>Stateful Transformation 에서 상태를 계속 저장하지 않고, 기간 혹은 사이즈 만큼만 이전 상태값이 저장 되도록 해주는 방법.&nbsp;</p></td></tr></tbody></table>
    > 참고:[https://coding-start.tistory.com/138#recentEntries](https://coding-start.tistory.com/138#recentEntries)\[코딩스타트\]

