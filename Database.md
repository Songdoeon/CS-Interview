# 데이터베이스
해당 답변은 mysql을 기준으로 답변합니다.
저의 생각과 서칭기반으로 작성한 내용이라 틀린점이 있을 수 있습니다.  
혹시 틀렸다고 생각이 든다면 의견 남겨주세요.
<details>
  <summary><h3>1. Key (기본키, 후보키, 슈퍼키 등등...) 에 대해 설명해 주세요.</h3></summary>
  
- 기본키는 수정이 가능한가요?
  - Alter를 이용해 기본키를 삭제하고 다시 생성한다.
    - 생성 : `Alter table 테이블명 add (constraint 제약조건이름) primary key(컬럼명, 컬럼명)`
    - 삭제 : `Alter table 테이블명 drop (constraint 제약조건이름) primary key`
  - 기본키가 auto_increment일 경우 auto_increment를 해제후 삭제한다.
- 사실 MySQL의 경우, 기본키를 설정하지 않아도 테이블이 만들어집니다. 어떻게 이게 가능한 걸까요?
  - 먼저 답으로는 기본적으로 사용자가 설정하지 않아도 auto_increment 속성의 Generated Invisibed PK가 생성됩니다.
  - 그렇기에 그냥 명시적으로 생성하고 해당 컬럼을 활용하는것이 좋습니다.
  - PK가 없는 테이블에서 대량의 변경작업이 발생한다면, secondary장비에서 복제 지연이 발생하는 경우를 간헐적으로 경험할 수 있다고합니다.
    - [리플리케이션 문제(feat.복제지연, semi-sync)](https://iiaii.tistory.com/entry/%EB%A6%AC%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98%EC%9D%98-%EB%AC%B8%EC%A0%9C%EC%99%80-%ED%95%B4%EA%B2%B0%EB%B0%A9%EB%B2%95-%EB%B3%B5%EC%A0%9C%EC%A7%80%EC%97%B0-semi-sync)
  - 기본키가 있는 경우 테이블 탐색에 용이합니다.
  - 기본키를 이용하여 중복데이터 제거 나 검색시 특정 인덱스만 찾아보는 등의 방식이 가능하기 때문입니다.
  - 하지만 기본키가 없다고 테이블이 아닌것은 아니나 관계형 데이터베이스가 지향하는점과 어긋나는 경향이 있습니다.
  - [기본키에 대한 탐구](https://blog.naver.com/PostView.naver?blogId=k65fac&logNo=222399911833&parentCategoryNo=&categoryNo=68&viewDate=&isShowPopularPosts=true&from=search)
  - [리플리케이션](https://iiaii.tistory.com/entry/%EB%A6%AC%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98Replication)

- 외래키 값은 NULL이 들어올 수 있나요?
  - 네 가능합니다.
  - 하지만 대부분의 외래키는 다른 테이블의 PK 또는 UK입니다.
  - 그렇기에 특별한 경우가 아니라면 참조 무결성을 지켜주는것이 좋습니다.
  - 하지만 모든사원은 부서를 가지고 있지만 신입사원은 부서가 정해지지 않았을 수도 있으니 NULL일 수도 있겠죠?

- 어떤 칼럼의 정의에 UNIQUE 키워드가 붙는다고 가정해 봅시다. 이 칼럼을 활용한 쿼리의 성능은 그렇지 않은 것과 비교해서 어떻게 다를까요?
  - 해당 문제가 pk같은 인덱스 활용을 묻는것인지 pk가 아닌 unique 칼럼을 묻는것인지 모르겠다.
    - 일단 대충 정리하도록 하겠음
  - row-based 환경에서 pk가 없는 테이블의 replication할때 활용 가능하다.
  - UNIQUE 칼럼은 secondary index로 활용이 가능합니다.
  - 테이블당 여러개를 가질 수 있다.
  - 테이블은 clustered index를 기준으로 정렬되어있기에 secondary index 기준으로는 정렬되어 있지않다.
  - 하지만 secondary index가 꼭 unique 칼럼을 이용하는것은 아니다.
</details>

<details>
  <summary><h3>2. RDB와 NoSQL의 차이에 대해 설명해 주세요.</h3></summary>
  
- RDB의 강점과, 약점이 무엇인가요?
  - 강점
    - ACID, Transaction을 지원한다.
    - 명확한 데이터 구조 보장
    - UPDATE가 잦은 시스템에 용이
  - 약점
    - ACID, Transaction을 지키며 수평적 확장하는 것이 쉽지않다.(join 연산의 복잡성, 샤딩의 어려움)
    - 스키마 변경의 어려움
    - 복잡한 쿼리 연산에서의 성능 저하 우려
    - 비정형 데이터 처리의 한계
      
- NoSQL의 강점과, 약점이 무엇인가요?
  - 강점
    - 유연하고 자유로운 데이터 구조
    - 새로운 필드 추가의 자유로움
    - 많은 데이터양 처리에 용이
    - 수평적 확장 용이
  - 약점
    - ACID, Transaction을 지원하지 않는다.
    - 데이터 중복 발생 가능
    - 많은 중복 데이터로 인해 데이터 변경시 모든 컬렉션에서 수정해야함
    - 데이터 일관성 저하 및 용량 증가
    - 명확한 데이터 구조 보장 X

- RDB의 어떠한 특징 때문에 NoSQL에 비해 부하가 많이 걸릴 "수" 있을까요? (주의: 무조건 NoSQL이 RDB 보다 빠르다라고 생각하면 큰일 납니다!)
  - RDB의 경우 데이터의 무결성이 특징이라고 생각합니다.
  - 그렇기에 데이터의 여러 무결성들을 지키려면 Transaction과 lock 같은 기능이 잘 활용 되어야 하는데
  - 그러한 기능들이 같은양의 트래픽일 때 NoSQL보다 부하가 더 걸릴 요소로 작용한다고 생각합니다.
  - 많은 트래픽이 요구되는 상황에서 RDB의 경우 분산 DB설계가 쉽지 않습니다.
    - master/slave 구조 에서의 데이터 동기화 문제
    - 데이터 샤딩시 HotSpot-key 문제
    - 안정 해시 전략 구조
- NoSQL을 활용한 경험이 있나요? 있다면, 왜 RDB를 선택하지 않고 해당 DB를 선택했는지 설명해 주세요.
  - ~~활용한 경험 없음 ㅇㅁㅇ~~
  - 비 관계형 데이터베이스가 바람직한 경우
    - 아주 낮은 응답 지연시간이 요구됨
    - 다루는 데이터가 비정형데이터
    - 데이터를 직렬화 하거나 역직렬화 할 수 있기만 하면됨
    - 아주 많은 양의 데이터를 저장해야함
      
</details>

<details>
  <summary><h3>3. 트랜잭션이 무엇이고, ACID 원칙에 대해 설명해 주세요.</h3></summary>
  
- ACID 원칙을 DBMS는 어떻게 보장하나요?
  - 원자성(Atomicity)
    - 현재 수행하고 있는 트랜잭션의 변경 내역을 유지하며, 이전 commit된 상태를 임시 영역에 저장한다.
    - 수행중 트랜잭션 오류 발생시 임시 영역에 저장한 상태로 rollback한다.
    - UNDO 로그를 활용하여 rollback을 진행한다.
  - 일관성(Consistency)
    - 일관성은 트랜잭션 수행 전,후 데이터 모델의 모든 제약 조건을 만족하는것을 통해 보장한다.
    - 테이블 간의 참조키를 변경시 두 테이블 모두 데이터를 변경함으로써 일관성을 보장합니다.
  - 고립성(Isolation)
    - 
- 트랜잭션을 사용해 본 경험이 있나요? 어떤 경우에 사용할 수 있나요?
 -
- 읽기에는 트랜잭션을 걸지 않아도 될까요?
</details>
