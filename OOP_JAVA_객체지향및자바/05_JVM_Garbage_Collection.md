# JVM Garbage Collection

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/JVM-4-Garbage-Collector-f105cdb6c08848088f437839e77e1403)
- [정회형](https://www.notion.so/hotheadfactory/Java-Garbage-Collection-3519bf7a24a5482aacac3e9a284df42f)
- [김종근](https://github.com/Bellroute/TIL/blob/master/Java/JVM-Garbage%20Collector.md)

---

GC(Garbage Collector)란 JVM의 Heap 영역에서 사용 중인 객체와 그렇지 않은 객체를 식별하고, 사용하지 않는 객체를 삭제하는 프로세스를 말한다.

JAVA에서 new라는 키워드로 객체(Object 타입의 데이터)를 생성하는데, 이렇게 생성된 객체는 JVM의 Runtime Data Area 중 Heap 영역에 동적으로 메모리가 할당된다. 동적으로 할당된 메모리가 더 이상 사용되지 않을 때 메모리를 해제하는 것이 GC이다.

</br>

### GC의 대상

자바의 GC는 **Reachability**라는 개념을 사용한다.

- 어떤 객체에 유효한 참조가 존재한다면 **Reachable**
- 그렇지 않다면 **Unreachable**이라고 한다.

[![img](https://camo.githubusercontent.com/b1d6156b7965fb9445dceb704763111599767451e1b5a9d838ef7e6b5232f9a0/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f636738437a652f62747142446138685448492f7346696365704756794a593157645433494e326c46302f696d672e706e67)](https://camo.githubusercontent.com/b1d6156b7965fb9445dceb704763111599767451e1b5a9d838ef7e6b5232f9a0/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f636738437a652f62747142446138685448492f7346696365704756794a593157645433494e326c46302f696d672e706e67)

그림에서와 같이 객체들은 다른 여러 객체를 참조하고, 그 객체들도 다른 객체들을 참조하므로 참조 트리를 이룬다. 참조 트리에서 유효한 참조인지의 여부를 확인하기 위해선 항상 유효한 최초의 객체가 존재해야하는데 이를 'GC Roots'라고 한다.

GC Roots에서 출발해서 참조되고 있는(도달 가능한) 객체를 Reachable Object로 판별하고, 참조되지 않는(도달할 수 없는) 객체를 Unreachable Object로 판별되며, GC의 수거 대상이 된다.

GC Roots에 해당되는 대상은 다음과 같다.

- stack 영역의 데이터
- method 영역의 static 데이터
- JNI에 의해 생성된 객체

</br> 

### GC의 동작 과정: Mark & Sweep

GC의 기본 프로세스는 'Mark & Sweep'이다.

1. **Mark** - Garbage Collector가 GC Roots를 스캔하면서 어떤 객체를 참조하고 있는지 찾아서 마킹한다. (Reachable Object가 참조하고 있는 객체도 찾아서 마킹)
2. **Sweep** - 마킹되지 않은 객체를 Heap에서 제거한다.

추가로 **Compact**라는 과정이 존재하는데, Sweep 후 흩어져있는 데이터들을 Heap의 시작 주소 쪽으로 모아 메모리 단편화 를 막는 과정을 의미한다.

** 메모리 단편화(Fragmentation) : 메모리의 공간이 작은 조각으로 나뉘어져 사용가능한 메모리가 충분히 존재하지만 할당(사용)이 불가능한 현상.*

</br>

### GC의 발생 시점: Heap의 구조

[![img](https://camo.githubusercontent.com/0597ef942f7d0e1198b2963a22a9551ed91a71b1e6e5ecdfb2a2bd8068d3f342/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f6f3044325a2f62747142464c36336534622f73346e454136576b626d6a557037336e76427570366b2f696d672e706e67)](https://camo.githubusercontent.com/0597ef942f7d0e1198b2963a22a9551ed91a71b1e6e5ecdfb2a2bd8068d3f342/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f6f3044325a2f62747142464c36336534622f73346e454136576b626d6a557037336e76427570366b2f696d672e706e67)

자바의 Heap은 Yong Generation 영역과 Old Generation 영역으로 나누어 메모리를 관리한다.

- **Yong Generation** : 새로운 객체들이 할당되는 영역. Eden과 2개의 Survival 영역으로 나누어 지며 Young 영역에서 일어나는 GC 를 'Minor GC'라 한다.

- **Old generation** : Young 영역에서 오랫동안 살아남은 객체들은 Old 영역으로 이동하게 된다. 해당 영역에서 일어나는 GC를 'Major GC' 또는 'Full GC'라 한다.

- **MetaSpace** : 자바8부터 등장했으며 자바7까지는 Permanent generation영역 이었으나 제거 되었다. 클래스 메타데이터들이 이 영역에 할당된다.

  [![img](https://camo.githubusercontent.com/8449a49d1ba01d012da9fcf868344a937bd939d0ad1cc624d9ee63e41a08da3d/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f496b3459522f6274714c794d68376f47582f6b4672446e4455626577654e5a54684c58744b41476b2f696d672e706e67)](https://camo.githubusercontent.com/8449a49d1ba01d012da9fcf868344a937bd939d0ad1cc624d9ee63e41a08da3d/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f496b3459522f6274714c794d68376f47582f6b4672446e4455626577654e5a54684c58744b41476b2f696d672e706e67)

</br>

GC의 구체적인 동작 과정을 살펴보자.

1. 새로운 객체가 Eden 영역에 할당
2. Eden 영역이 가득차면 Minor GC 발생.
   - 살아남은 객체는 Survival 영역 중 한 곳(S0)으로 이동하고
   - Eden 영역은 빈 상태가 된다.
3. 다음 Minor GC가 일어나면 S0과 Eden에서 살아남은 객체들은 다른 Survivor 영역인 S1으로 이동한다.
   - S0과 Eden 영역은 빈 상태가 된다.
4. Minor GC가 일어날 때마다 살아남은 객체들은 각 Survival 영역으로 이동하게 된다.
   - Survival 영역으로 이동할 때마다 객체들은 age값이 증가한다.
5. 위와 같은 과정이 반복되면서 오랫동안 Young 영역에서 살아남은 객체들은 Old 영역으로 이동
   - 객체의 age가 특정 임계치에 도달하게 되었을 때 이동하게 된다.
   - 이를 Promotion이라 한다.
6. Promotion의 반복으로 Old 영역이 가득차게 되면 Major GC가 일어난다.

</br>

#### 왜 이런 구조를 가지게 되었을까?

만약, GC가 Heap의 모든 메모리를 뒤져가며 객체를 식별한다면 굉장히 비효율적일 것이다. 그래서 자바의 GC는 두 가지 전제 조건 하에 만들어졌다.

- 대부분의 객체는 금방 접근 불가능 상태(unreachable)가 된다.
- 오래된 객체에서 젊은 객체로의 참조하는 일은 거의 존재하지 않는다.

이러한 전제를 **weak generational hypothesis**라 하고, 이 전제 덕분에 효율적인 GC 과정이 가능해졌다.

but, **두 번째 전제인 '오래된 객체가 그렇지 젊은 객체로의 참조하는 일은 거의 존재하지 않는다'라고 했는데, 만약 그러한 일이 생기면 어떻게 할까?**

이러한 경우를 처리하기 위해 Old 영역에는 512바이트의 덩어리(chunk)로 되어 있는 card table이 존재한다.

- Old 영역에 있는 객체가 Young 영역의 객체를 참조할 때마다 카드 테이블에 정보를 기록.
- Minor GC를 실행할 때에는 Old 영역에 있는 모든 객체의 참조를 확인하지 않고, 이 카드 테이블만 뒤져서 GC 대상인지 식별한다.

</br>

### GC의 종류

#### 0. stop-the-world란?

GC를 실행하는 쓰레드 외에 모든 쓰레드가 작업이 중단되는 현상이다. GC 종류에 따라서 stop-the-world가 발생하는 시간이 다르다.

#### 1. Serial GC

- GC를 처리하는 쓰레드가 1개 (싱글 쓰레드)
- 다른 GC에 비해 stop-the-world 시간이 길다.
- Old 영역의 GC에서 Mark-Sweep-Compact 알고리즘 사용
- 적은 메모리와 CPU 코어 개수가 적을 때 적합한 방식

#### 2. Parallel GC

[![img](https://camo.githubusercontent.com/972adc825338ac75f1837442998c70b64341ca362218eef430c750b05d64bb7f/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f626261356d6a2f62747142453162736b37322f506e304a7977536d48487a616b4a584f6f527847314b2f696d672e706e67)](https://camo.githubusercontent.com/972adc825338ac75f1837442998c70b64341ca362218eef430c750b05d64bb7f/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f626261356d6a2f62747142453162736b37322f506e304a7977536d48487a616b4a584f6f527847314b2f696d672e706e67)

- Java 8의 default GC
- Young 영역의 GC를 멀티 쓰레드로 수행
- Serial GC에 비해 stop-the-world 시간 감소
- 메모리가 충분하고 코어의 개수가 많을 때 유리

#### 3. Parallel Old GC

- Parallel GC를 개선
- Old 영역에서도 멀티 쓰레드 방식의 GC 수행
- Old 영역에서 Mark-Summary-Compact 알고리즘 사용
  - Sweep은 살아있는 객체만 찾아내는 방식이라면
  - Summary는 이전에 GC를 수행하여 Compaction된 영역에 살아 있는 객체의 위치를 조사한다.

#### 4. CMS GC (Concurrent Mark Sweep)

[![img](https://camo.githubusercontent.com/8ca1d08273cd1f6f289f690e3211c645ba2a3ac1122ace4357e476ba36ab6214/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f52703461352f62747142443275377477722f4c59494e4d6b56715358636979467645676a4332416b2f696d672e706e67)](https://camo.githubusercontent.com/8ca1d08273cd1f6f289f690e3211c645ba2a3ac1122ace4357e476ba36ab6214/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f52703461352f62747142443275377477722f4c59494e4d6b56715358636979467645676a4332416b2f696d672e706e67)

- Stop-the-world 시간을 줄이기 위해 고안됨
- recheable한 객체를 한 번에 찾지 않고 순차적으로 찾는다.
- GC를 처리하는 쓰레드는 하나지만, 나머지지 쓰레드들은 작업을 계속 진행한다.
- 다음과 같은 4개의 과정 존재
  1. Initail Mark : GC Root에서 가장 먼저 참조하는 객체들만 식별한다. Reachable 객체들을 전부 찾는 것이 아니기 때문에 Stop The World 시간이 짧다.
  2. Concurrent Mark : 싱글 스레드로 수행되며 다른 스레드가 멈추지 않고 동시에 수행된다. Initial mark 단계에서 식별한 객체가 참조하고 있는 모든 객체들을 추적한다.
  3. Remark : Concurrent Mark 단계에서 식별한 객체를 다시 추적하여 추가되거나 참조가 끊긴 객체를 확인하고 확정한다. 멀티 스레드로 동작하기 때문에 Stop The World 시간이 짧다.
  4. Concurrent Sweep : 최종적으로 살아있는 객체를 제외한 객체들을 삭제한다. 싱글 스레드로 수행되며 다른 스레드와 동시에 수행되기 때문에 Stop The World가 발생하지않는다.
- Compact 과정이 존재하지 않기 때문에 메모리 단편화가 생겨 Concurrent Mode Failure(CMF) 가 발생할 수 있다. CMF가 발생하면 강제적으로 Compaction을 수행하므로 'Stop The World' 시간이 다른 GC보다 길어질 가능성이 있다.

#### 5. G1 GC (Garbage First)

[![img](https://camo.githubusercontent.com/201f7760d0c8afba73e45dc04297804071544fcd00daae67217c104a5c022e97/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f635a6935757a2f6274714244627a6c3175662f70427059614d5a7851495a39743854444b31776e67312f696d672e706e67)](https://camo.githubusercontent.com/201f7760d0c8afba73e45dc04297804071544fcd00daae67217c104a5c022e97/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f635a6935757a2f6274714244627a6c3175662f70427059614d5a7851495a39743854444b31776e67312f696d672e706e67)

- CMS GC를 개선
- Java 9+의 defualt GC
- Heap을 일정한 크기의 Region으로 나눔 (기존 GC처럼 Young, Old 영역을 물리적으로 나누지 않음)
  - Eden, Survivor, Old 영역은 고정된 크기로 나눈 이 Region을 사용한다.
  - 그외에 Homongous라는 영역이 존재. 객체의 크기가 Region의 50% 보다 큰 경우를 위한 영역.
- 전체 Heap이 아닌 Region 단위로 탐색
- compact 진행

</br>

#### [참고]

> - https://www.youtube.com/watch?v=Fe3TVCEJhzo
> - [https://sheerheart.tistory.com/entry/Java-Metaspace%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C](https://sheerheart.tistory.com/entry/Java-Metaspace에-대해서)
> - https://jeong-pro.tistory.com/91
> - https://advenoh.tistory.com/14
> - https://javabom.tistory.com/7
> - https://d2.naver.com/helloworld/1329