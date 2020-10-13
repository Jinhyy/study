### ZooKeeper
---
## Cooridation System.
- 분산 시스템 상의 Coordinater의 필요성 및 역할
  1. 분산된 시스템간의 정보 공유
  2. 클러스터내 시스템 헬스 체크
  3. 분산 시스템 간 동기화를 위한 락(lock) 처리

## 대표적인 Coordination System, Zookeeper 
  > <a href="#"><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile6.uf.tistory.com%2Fimage%2F9993623A5BF7B2CD28F28B"></a>
  - Zookeeper만의 특징
      - Leader-Follwer 구조
      - 디렉토리 구조기반으로 znode라는 Key-Value 형태의 데이타 저장 객체 및 zNode CRUD API 제공
        
## Zookeeper 동작방식
  1. 주키퍼 클라이언트들은 주키퍼 서버들로 이루어진 앙상블(Ensemble)에 접근
  2. znode의 데이터를 읽거나 데이터를 업데이트. 
  3. zNode가 업데이트 된 경우 Leader 주키퍼 서버에 데이터 변경을 알리고 자신의 데이터 업데이트. 
  4. Leader 주키퍼 서버는 그 정보를 다른 곳에 브로드캐스트(Broadcast) 형식으로 전파. 
  5. Follower 주키퍼 서버들은 전파된 변경내용을 반영, 주키퍼 앙상블 내 모든 서버 데이터 일관성 확보.
  > 참고 : https://engkimbs.tistory.com/660 [새로비]


## Zookeeper의 zNode
  > <a href="#"><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbqFEX5%2Fbtqz9I6HGPX%2FP4zJEh2yOx1Aphg4knRTOK%2Fimg.png"></a>
  1. zNode는 여러가지의 메타데이타를 제공.
    - 버전 넘버(version number) : znode의 데이터가 업데이트 될 때마다 버전 넘버도 업데이트. 이 정보는 다수의 주키퍼 클라이언트(client)들이 특정한 연산을 같은 znode에 수행할 때 꼭 필요한 정보입니다.
    - ACL(Action control list)
        - ACL Permissions ( zNode 제어 권한 플래그 )
          ---

## Cooridation System.
- 분산 시스템 상의 Coordinater의 필요성 및 역할
  1. 분산된 시스템간의 정보 공유
  2. 클러스터내 시스템 헬스 체크
  3. 분산 시스템 간 동기화를 위한 락(lock) 처리

- 대표적인 Coordination System, Zookeeper 
    > <a href="#"><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile6.uf.tistory.com%2Fimage%2F9993623A5BF7B2CD28F28B"></a>
    - Zookeeper만의 특징
        - Leader-Follwer 구조
        - 디렉토리 구조기반으로 znode라는 Key-Value 형태의 데이타 저장 객체 및 zNode CRUD API 제공
        
    - Zookeeper 동작방식
        1. 주키퍼 클라이언트들은 주키퍼 서버들로 이루어진 앙상블(Ensemble)에 접근
        2. znode의 데이터를 읽거나 데이터를 업데이트. 
        3. zNode가 업데이트 된 경우 Leader 주키퍼 서버에 데이터 변경을 알리고 자신의 데이터 업데이트. 
        4. Leader 주키퍼 서버는 그 정보를 다른 곳에 브로드캐스트(Broadcast) 형식으로 전파. 
        5. Follower 주키퍼 서버들은 전파된 변경내용을 반영, 주키퍼 앙상블 내 모든 서버 데이터 일관성 확보.
        > 참고 : https://engkimbs.tistory.com/660 [새로비]


- Zookeeper의 zNode
  > <a href="#"><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbqFEX5%2Fbtqz9I6HGPX%2FP4zJEh2yOx1Aphg4knRTOK%2Fimg.png"></a>
  1. STAT
    - zNode 메타데이터 제공.
      1. czxid    : znode를 생성한 트랜잭션의 id
      2. mzxid    : znode를 마지막으로 수정 트랜잭션의 id
      3. ctime    : znode가 생성됐을 때의 시스템 시간
      4. mtime    : znode가 마지막으로 변경되었을 때의 시스템 시간
      5. version  : znode가 변경된 횟수
      6. cversion : znode의 자식 node를 수정한 횟수
      7. aversion : ACL 정책을 수정한 횟수
      8. ephemeralOwner : 임시 노드인지에 대한 flag
      9. dataLength : data의 길이, 최대 1MB
      10. numChildren : 자식 node의 수

  2. ACL(Action control list) 제공
    - zNode 조작 권한 플래그
      1. CREATE, DELETE : zNode가 자식노드를 생성 혹은 삭제할 수 있는 권한
      2. READ           : zNode에 data를 읽고 자식들의 목록을 읽을 수 있는 권한.
      3. WRITE          : zNode에 data를 쓸 수 있는 권한.
      4. ADMIN          : ACL플래그를 설정할 수 있는 권한.
      
  3. SCHEME 제공
    - zNode 접근/인가 권한 플래그
      1. WORLD  : 모든 사용자 접근가능.
      2. AUTH   : 인증된 세션만 접근 가능.
      3. DIGEST : 허용된 id/password 등으로 만들어진 임의의 Hash값만 허용.
      4. HOST   : 허용된 Host만
      5. IP     : 허용된 IP만

    > 참고: https://engkimbs.tistory.com/660 [새로비]
    
  4. 여러 종류의 zNode 제공
    - Persistent Node : 노드에 데이타를 저장하면 일부러 삭제하지 않는 이상 삭제되지 않고 영구히 저장된다.
    - Ephemeral Node  : 노드를 생성한 클라이언트의 세션이 연결되어 있을 경우만 유효하다. 즉 클라이언트 연결이 끊어지는 순간 삭제 된다. 이를 통해서 클라이언트가 연결이 되어 있는지 아닌지를 판단하는데 사용할 수 있다. (클러스터를 구성할때 클러스터내에 서버가 들어오면, 이 Ephemeral Node로 등록하면 된다.) 
    - Sequence Node   : 노드를 생성할때 자동으로 sequence 번호가 붙는 노드이다. 주로 분산락을 구현하는데 이용된다.

---
