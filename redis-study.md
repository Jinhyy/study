#Redis

## 1. 개요
  - In-memory Data Storage / Single Thread (Atomic 보장) / 여러가지 Collection 제공 ( memcached와의 차이)
  - 활용 예시 : 인증 토큰 저장 / 랭킹 보드 ( sorted set 활용 ) / 유저 별 api limit / job queue, ...

## 2. Redis Collection
  - 컬렉션 사용시 주의사항
    - expire 옵션의 경우 컬렉션 내 원소별로 걸리지 않고, 해당 컬렉션 자체로 걸림.
    - 하나의 컬렉션안에 10000개 원소로 유지하는게 좋음. ( 레디스 성능에 유리 )
    
  - Strings
    - Set <key> value
    - Get <key> value
    - key 에 따라, 어디에 분산 저장 될지 결정됨.
  
  - List
    - Lpush/Rpush <key> <원소 목록>
    - Lpop/Rpop <key>
    - lragnge <key> <start index> <end index>
    - BLPOP/BRPOP <key> <timeout seconds> : 블로킹 pop 명령어, pop할 데이터가 없을 경우 최대 <timeout seconds> 만큼 대기한다.
  
  - Set
    - SADD <key> <value>
    - SMEMBERS <key> : 모든 value 리턴
    - SISMEMBER <key> <value> : 해당 set에 포함 여부 확인.
    - sdiff/sunion/sinter <key1> <key2> : key1 <-> key2 차/합/교집합 리턴.
    
  - Sorted Sets
    - 소개
      - value 중복 허용X / score가 같을시 value 로 sort 처리 됨
    - 명령어
      - ZADD <Key> <Score> <Value> : 추가 / 해당 value가 이미 key에 있으면 score 변경됨.
      - ZRANGE <key> <start index> <end index> : 해당 범위에 속한 값들을 리턴.
      - zrangebyscore <key> <min> <max> : 스코어범위로 값 리턴.
  
  - Hash
    - 소개
      - key 하나에 여러개의 field와 value로 구성 / key 하나에 최대 40억개 쌍 저장 가능.
    
## 3. Redis 운영
  - 메모리 관리
    - 물리적 메모리에 영향을 받음 / 메모리 스왑 발생시 디스크를 사용하게 되므로 성능저하가 발생.
    - 레디스 설정값 내 maxmemory 설정을 하더라도, 실제로 사용되는 메모리값이 이것보다 높을 수 있다.(메모리 파편화 가능성)
    - 실제 메모리 사용량(RSS 값) 및 스왑여부를 모니터링 해야한다.
    - 컬렉션 타입별로 여러가지 메모리 관리를 효율적으로 해주는 인코딩이 존재한다.(ziplist, hashtable, ... https://great-song2.tistory.com/4 참고 )
  - O(N) 관련 명령어는 주의하자.
    - Redis는 싱글스레드 이므로, 한번에 하나의 커맨드만 수행이 가능하므로 긴 명령어는 주의하자! ( keys, flushall, flushdb, ... )
    - keys명령어가 필요할 경우 다가져오지 말고, scan 명령어로 부분적으로 가져오도록 하자. ( 해당명령어 disable 권장 )
    - 큰 컬렉션의 아이템을 가져와야 할 경우? -> 작은 여러개의 컬렉션으로 만들어 두어야한다.


## 4. Redis Replication
  - Redis는 Async Replication 이다.
    - Original과 replication lag이 발생가능.
  - Secondary 인스턴스에서 replicaof or slaveof 명령어로 replication 관계 설정가능하다.
    - replication 설정 시, original 인스턴스에서 fork 가 발생 ( 햔재 상태의 메모리 스냅샷을 디스크에 저장 후 전달 ) => 주의 필요.
    - replication 이 너무 많이 설정되어 있을 시, original => replica server로의 정보 전달이 네트워크 대역폭을 넘을수 있으므로 주의.

## 5. Redis 데이터 분산

## 3. Redis Failover
