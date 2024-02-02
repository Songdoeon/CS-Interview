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
  - [기본키에 대한 탐구](https://blog.naver.com/PostView.naver?blogId=k65fac&logNo=222399911833&parentCategoryNo=&categoryNo=68&viewDate=&isShowPopularPosts=true&from=search)
  - [리플리케이션](https://iiaii.tistory.com/entry/%EB%A6%AC%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98Replication)
  - [리플리케이션 문제(feat.복제지연, semi-sync)](https://iiaii.tistory.com/entry/%EB%A6%AC%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98%EC%9D%98-%EB%AC%B8%EC%A0%9C%EC%99%80-%ED%95%B4%EA%B2%B0%EB%B0%A9%EB%B2%95-%EB%B3%B5%EC%A0%9C%EC%A7%80%EC%97%B0-semi-sync)
  - 먼저 답으로는 기본적으로 사용자가 설정하지 않아도 auto_increment 속성의 hidden PK가 생성됩니다.
  - 그렇기에 그냥 명시적으로 생성하고 해당 컬럼을 활용하는것이 좋습니다.
  - PK가 없는 테이블에서 대량의 변경작업이 발생한다면, secondary장비에서 복제 지연이 발생하는 경우를 간헐적으로 경험할 수 있다고합니다.
    - 해당 내용은 좀 더 찾아본 후 추가하도록 하겠습니다.
  - 기본키가 있는 경우 테이블 탐색에 용이합니다.
  - 기본키를 이용하여 중복데이터 제거 나 검색시 특정 인덱스만 찾아보는 등의 방식이 가능하기 때문입니다.
  - 하지만 기본키가 없다고 테이블이 아닌것은 아니나 관계형 데이터베이스가 지향하는점과 어긋나는 경향이 있습니다.
- 외래키 값은 NULL이 들어올 수 있나요?
- 어떤 칼럼의 정의에 UNIQUE 키워드가 붙는다고 가정해 봅시다. 이 칼럼을 활용한 쿼리의 성능은 그렇지 않은 것과 비교해서 어떻게 다를까요?

</details>
