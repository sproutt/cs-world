# 인덱스

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/c559ccd05be949c9986c2e3738f1b3e3) (작성자)
- [정회형](https://www.notion.so/hotheadfactory/817612921be64b18898bd76dbb4cce65)
- [김종근](https://github.com/Bellroute/TIL/blob/master/Database/%EC%9D%B8%EB%8D%B1%EC%8A%A4(index).md
)

---

## 인덱스란?

인덱스(index)의 원래 뜻은 색인. 데이터베이스에서 조회 및 검색을 더 빠르게 할 수 있는 방법/기술, 혹은 이에 쓰이는 자료구조 자체를 의미하기도 한다.

## **사용 이유**

`select`문을 사용하여 원하는 조건의 데이터를 검색할 때, 저장된 데이터의 양이 엄청나게 많다면 검색을 위한 순회에 많은 자원과 시간이 소모될 것이다. 이때 도움이 되는게 인덱스이다.

자주 조회되는 Column 에 대한 Index Table을 따로 만들어 SELECT 문이 들어왔을 때 Index 테이블에 있는 값들로 결과 값을 조회해 온다. 그래서 Index를 잘 사용한다면 "검색" 연산을 실행했을 때 성능을 올릴 수 있게 된다.

## **인덱스 저장 위치**

초기 Table 생성시, {table명}.frm, {table명}.MYD, {table명}.MYI 3개의 파일이 만들어진다.

- **.frm** : 테이블 구조가 저장되어 있는 파일
- **.MYD**: 실제 데이터가 있는 파일
- **.MYI** : INDEX 정보가 들어있는 파일

인덱스를 사용하지 않는 경우, MYI파일은 비워져 있다. 인덱스를 해당 컬럼에 만들게 되면 해당 컬럼을 따로 인덱싱하여 MYI파일에 입력한다. 이후 사용자가 SELECT 쿼리로 인덱스를 사용하는 쿼리를 사용시 해당 Table을 검색하는 것이 아니라 MYI 파일의 내용을 검색한다.

## **동작**

- Index Table에서 `where`에 포함된 값을 검색
- 해당 값의 table_id PK를 획득
- 가져온 table_id PK값으로 원본 테이블에서 값을 조회

DBMS는 인덱스를 다양한 알고리즘으로 관리를 하고 있는데 일반적으로 사용되는 알고리즘은 B+ Tree 알고리즘이다.

## **장점**

- 테이블 조회 시 속도를 개선함으로서 성능을 향상시킬 수 있다.
- 시스템의 부하를 줄일 수 있다.

## **단점**

- 인덱스를 관리하기 위해 DB의 약 10%에 해당하는 저장공간이 필요하다.
- 인덱스를 관리하기 위해 추가 작업 (오버헤드) 이 필요하다.
- 인덱스를 잘못 사용할 경우 오히려 성능이 저하될 수도 있다.

    → 만약 CREATE, DELETE, UPDATE가 잦은 테이블에 인덱스를 걸게 되면 인덱스의 크기가 비대해져서 성능이 오히려 저하될 수도 있다. 그러한 이유 중 하나는 DELETE와 UPDATE 연산 때문이다. UPDATE와 DELETE는 기존의 인덱스를 삭제하지 않고 '사용하지 않음' 처리를 하는데, 만약 어떤 테이블에 UPDATE와 DELETE가 빈번하게 발생된다면 실제 데이터에 비해 인덱스가 지나치게 많아져 SQL문 처리 시 오히려 성능이 떨어지게 될 것이다.

## **인덱스의 종류**

인덱스의 종류에는 크게 2가지가 있다.

### **Clustered Index**

- 테이블의 데이터를 지정된 컬럼에 대해 물리적으로 재배열
- 테이블 당 한개만 존재 (Primary key를 기준으로 자동으로 생성됨)

### **Non-Clustered Index**

- 테이블의 데이터를 지정된 컬럼에 대해 물리적으로 재배열 안함
- 테이블 당 여러개 존재 가능
- 클러스터형 인덱스처럼 자동 정렬되지는 않음.

## 인덱스를 사용하면 좋은 경우

- 규모가 큰 테이블
- INSERT, UPDATE, DELETE가 자주 발생하지 않는 컬럼
- JOIN이나 WHERE 또는 ORDER BY에 자주 사용되는 컬럼
- 데이터의 중복도가 낮은 컬럼

## **B+tree**

- 실제 데이터가 저장된 리프노드(Leaf nodes)
- 리프노드까지의 경로 역할을 하는 논리프노드(Non-leaf nodes)
- 경로의 출발점이 되는 루트 노드(Root node)

B+tree는 리프노드에 이르기까지에 대한 자식 노드에 포인터가 저장되어 있다. 즉, B+트리의 검색은 루트노드에서 어떤 리프 노드에 이르는 한 개의 경로만 검색하면 되므로 매우 효율적이다.

## **B+tree 사용 이유**

- **왜 index 생성 시 b-tree를 사용하는지? hash table이 더 효율적이지 않은지?**
    - SELECT 질의 조건에는 부등호 연산(<>)도 포함
    - hash table은 동등 연산에 특화된 자료구조이기 때문에 부등호 연산 사용 시 문제 발생

## **주의할 점**

- 인덱스는 따로 테이블의 형태로 관리가 된다. 자원을 소모한다는 의미. 때문에 무분별한 인덱스의 사용은 성능에 부정적인 영향을 미칠 수 있다.
- 또한 인덱스는 이진트리를 사용하기 때문에 기본적으로 정렬되어 있다. 이로인해 검색과 조회의 속도를 향상시킬 수 있지만 잦은 데이터의 변경(삽입, 수정 삭제)가 된다면 인덱스 데이블을 변경과 정렬에 드는 오버헤드 때문에 오히려 성능 저하가 일어날 수 있다.
    - INSERT : 테이블에는 입력 순서대로 저장되지만, 인덱스 테이블에는 정렬하여 저장하기 때문에 성능 저하 발생
    - DELETE : 테이블에서만 삭제되고 인덱스 테이블에는 남아있어 쿼리 수행 속도 저하
    - UPDATE : 인덱스에는 UPDATE가 없기 때문에 DELETE, INSERT 두 작업 수행하여 부하 발생
- 데이터의 중복이 높은 컬럼(카디널리티가 낮은 컬럼)은 인덱스로 만들어도 무용지물 (예: 성별)
- 다중 컬럼 인덱싱할 때 카디널리티가 높은 컬럼->낮은 컬럼 순으로 인덱싱해야 효율적

## Quiz
```
Q. 인덱스에서 B tree를 사용하는 이유
```
## 참고

- [https://github.com/WeareSoft/tech-interview/blob/master/contents/db.md#index란](https://github.com/WeareSoft/tech-interview/blob/master/contents/db.md#index%EB%9E%80)
- [https://lalwr.blogspot.com/2016/02/db-index.html](https://lalwr.blogspot.com/2016/02/db-index.html)
- [https://k39335.tistory.com/26](https://k39335.tistory.com/26)
- [https://brunch.co.kr/@skeks463/25](https://brunch.co.kr/@skeks463/25)
- [https://m.blog.naver.com/PostView.nhn?blogId=rladlaks123&logNo=220475359364&proxyReferer=https:%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.nhn?blogId=rladlaks123&logNo=220475359364&proxyReferer=https:%2F%2Fwww.google.com%2F)
- [https://swconsulting.tistory.com/381](https://swconsulting.tistory.com/381)
- [https://serverwizard.tistory.com/93](https://serverwizard.tistory.com/93)