# Collection(2) - Set, Map

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/Collection-Set-Map-5931c8b7a797480dbc10dcb6cc8f8e60) (작성자)
- 정회형
- [김종근](https://github.com/Bellroute/TIL/blob/master/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0/Set%EA%B3%BC_Map.md)

---
## 들어가기

 앞서 알아본 [List](https://www.notion.so/Collection-List-c1327c79a3d547f88d2662e831b56436)는 중복된 데이터에 상관없이 자료를 선형적으로 관리하고 또한 데이터의 크기를 동적으로 관리하기 위한 인터페이스였다. 그와 반대로 이번에 알아볼 Set, Map은 중복되는 데이터를 무시하며 저장순서를 유지해서 관리하지 않는 데이터 집합이다.

 Set은 하나의 Value값만을 가지며, Map은 Key와 Value의 쌍으로 데이터를 관리한다. 그리고 Map이 Collection 인터페이스를 상속받고 있지는 않지만(Key, Value의 쌍으로 관리되는 데이터라 그런것으로 생각된다) Map까지 자바 컬렉션에 포함이 된다. 

![](https://camo.githubusercontent.com/fe7893d6f4d037c9d008bc74027e99a55d3c9abc54a54b31ec405abf707014f2/68747470733a2f2f74312e6461756d63646e2e6e65742f6366696c652f746973746f72792f323134343030343735374645314533333241)

# Set

- 저장순서가 유지되지 않음
- 중복 저장하지 않고, 하나의 null만 저장할 수 있음
- 종류
    - HashSet
    - LinkedHashSet
    - TreeSet
- 메소드 : add, iterator, size, remove, clear
- 데이터를 검색하기 위해서는 iterator() 메서드로 Iterator(반복자)를 생성하고 데이터를 가져와야 함

### HashSet

Set 인터페이스를 구현하는 대표적인 클래스로 Set 자료구조의 특징을 그대로 가지고 있음

- HashSet은 해싱을 이용하여 구현

### TreeSet

중복을 허용하지 않고 데이터를 저장하되 저장한 데이터가 Comparator에 의해 정렬되어있음

이름에서도 알 수 있듯이 데이터를 Tree구성으로 관리

- 이진 탐색 트리인 레드-블랙 트리를 사용
- 추가와 삭제에는 시간이 걸리지만, 정렬과 검색에 높은 성능을 보임

### LinkedHashSet

중복을 허용하지 않고 입력된 순서대로 데이터를 관리

# Map

- key와 value로 구성된 객체를 저장하는 Collection으로 중복되는 키를 저장할 수 없음
- 종류
    - HashMap
    - TreeMap
    - LinkedHashMap
    - HashTable

### TreeMap

- Comparator기준으로 정렬된 상태를 유지
- null 키는 가질 수 없지만 복수의 널 값을 가질 수 있음

### LinkedHashMap

- 입력된 순서를 기억하는 HashMap

### HashTable

- null값 허용
- 동기화 보장
- HashMap의 레거시 클래스

### HashMap

- null값 미허용
- 비동기
- 구조
    - key : 고유한 값
    - hash function : 키를 hash값으로 변환
    - hash : hash function의 결과
    - value : 키와 함께 버킷에 저장되는 값

#### 해시(Hash)란?

해시 함수란 임의 크기 데이터를 고정 크기 값으로 매핑하는 데 사용할 수 있는 함수를 말한다. 그리고 데이터를 인덱싱하기 위해 해시 함수를 사용하는 것을 해싱(Hashing)이라고 한다. 

![img](https://t1.daumcdn.net/cfile/tistory/25758F43580F530822)

#### 해시 충돌(Hash Collision)

해시 함수는 해쉬 값의 개수보다 대개 많은 키 값을 해쉬 값으로 변환(many-to-one 대응)하기 때문에 해시 함수가 서로 다른 두 개의 키에 대해 동일한 해시값을 내는 해시 충돌(collision)이 발생한다.

![해시 함수 - 위키백과, 우리 모두의 백과사전](https://upload.wikimedia.org/wikipedia/commons/thumb/5/58/Hash_table_4_1_1_0_0_1_0_LL.svg/1200px-Hash_table_4_1_1_0_0_1_0_LL.svg.png)

해시 충돌을 해결하기 위한 방법에는 3가지가 존재한다. 

**1. 개별 체이닝(Seperate Chaining)**

![img](https://t1.daumcdn.net/cfile/tistory/2525963E580F616926)

- 충돌 발생 시 연결리스트로 연결하는 방식.
- 모든 데이터가 해시 충돌이 일어나는 최악의 경우, 데이터가 연결 리스트로 이루어지게 되므로 시간복잡도가  `O(n)`이 될 수 있다.
- Java8 버전에서는 성능을 향상 시키기 위해 충돌된 데이터가 8개가 모이면 레드 블랙 트리로 변환하여 탐색에 유리하도록 한다.
- 장점 :
  - 한정된 저장소(Bucket)을 효율적으로 사용할 수 있다.
  - 해시 함수(Hash Function)을 선택하는 중요성이 상대적으로 적다.
  -  ~~상대적으로 적은 메모리를 사용한다.~~ 미리 공간을 잡아 놓을 필요가 없다.
- 단점 :
  - 한 Hash에 자료들이 계속 연결된다면(쏠림 현상) 검색 효율을 낮출 수 있다.
  - 외부 저장 공간을 사용한다.
  - 외부 저장 공간 작업을 추가로 해야 한다.

**2. 오픈 어드레싱(Open Addressing)**

![C++, Java 해시 테이블 구현 · The Missing Papers](https://upload.wikimedia.org/wikipedia/commons/thumb/b/bf/Hash_table_5_0_1_1_1_1_0_SP.svg/450px-Hash_table_5_0_1_1_1_1_0_SP.svg.png)

- 충돌 발생 시 탐사(probing)을 통해 빈 공간을 찾아나서는 방식.
  - 선형 탐색(Linear Probing): 해시충돌 시 다음 버켓, 혹은 몇 개를 건너뛰어 데이터를 삽입한다.
  - 제곱 탐색(Quadratic Probing): 해시충돌 시 제곱만큼 건너뛴 버켓에 데이터를 삽입(1,4,9,16..)
  - 이중 해시(Double Hashing): 해시충돌 시 다른 해시함수를 한 번 더 적용한 결과를 이용함.
- 빈 공간을 찾아 값을 저장하기 때문에 모든 원소가 반드시 자신의 해시 값과 일치하는 주소에 저장된다는 보장은 없음.

- 장점 :
  - 또 다른 저장공간 없이 해시테이블 내에서 데이터 저장 및 처리가 가능하다.
  - 또 다른 저장공간에서의 추가적인 작업이 없다.

- 단점 :
  - 해시 함수(Hash Function)의 성능에 전체 해시테이블의 성능이 좌지우지된다.
  - 데이터의 길이가 늘어나면 그에 해당하는 저장소를 마련해 두어야 한다.

**3. resizing**

- 버킷의 크기가 채워지다가 해당 임계점인(load factor)에 도달하면 버킷의 크기를 늘리고 버킷에 저장된 값들을 재정렬하는 과정

## Quiz

> Q. Set 인터페이스의 구현체의 종류와 그 차이점들에 대해서 설명하시오

> Q. hash 충돌에 대해서 설명해주세요

## 참고

> - [[JAVA/자바] Set - HashSet, TreeSet, LinkedHashSet](https://m.blog.naver.com/PostView.nhn?blogId=heartflow89&logNo=220994601249&proxyReferer=https:%2F%2Fwww.google.com%2F)
> - [https://coding-factory.tistory.com/555](https://coding-factory.tistory.com/555)
> - [https://brunch.co.kr/@springboot/57](https://brunch.co.kr/@springboot/57)
> - https://velog.io/@cyranocoding/Hash-Hashing-Hash-Table%ED%95%B4%EC%8B%9C-%ED%95%B4%EC%8B%B1-%ED%95%B4%EC%8B%9C%ED%85%8C%EC%9D%B4%EB%B8%94-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%9D%98-%EC%9D%B4%ED%95%B4-6ijyonph6o
> - https://preamtree.tistory.com/20
> - https://d2.naver.com/helloworld/831311
> - https://odol87.tistory.com/4
> - http://www.tcpschool.com/java/java_collectionFramework_map
> - https://limkydev.tistory.com/40
> - https://lelumiere.tistory.com/3
> - https://pridiot.tistory.com/72
> - https://coding-factory.tistory.com/554
> - https://coding-factory.tistory.com/555
> - https://withwani.tistory.com/150