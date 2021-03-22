# CPU 스케줄링

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/CPU-61ddfd0beea4476993e739ac1e4d7958) (작성자)
- [정회형](https://www.notion.so/hotheadfactory/739144eb8bb94d46a0f82275e9ea4499)
- [김종근](https://github.com/Bellroute/TIL/blob/master/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C/CPU%EC%8A%A4%EC%BC%80%EC%A5%B4%EB%A7%81.md)

---

## CPU 스케줄링

### CPU 스케줄링이란?

CPU에는 다양한 프로그램들이 동시에 실행이된다. 하지만 CPU의 자원은 한정적이기 때문에 CPU는 **자원을 어떠한 프로세스에 얼마큼 혹은 어느정도의 시간동안 할당**할지 정해야한다. 따라서 이러한 작업을 수행하는 일을 CPU 스케줄링이라고 한다.

### CPU 스케줄링이 필요한 이유?

 우리가 라면을 끓일때 물을 끓이면서 아무것도 하지않는 상태로 있지 않고 봉투를 뜯거나 재료를 미리 준비하는 것을 CPU 스케줄링과 비슷하다고 보면 된다. CPU에서 동작하는 프로세스 또한 프로세스의 생명주기에서 볼수 있듯이 실제 일을 처리하는 running단계도 있으며 또한 I/O 또는 Interrupt가 발생하여 waiting상태로 대기중일 때도 있다. 따라서 이러한 waiting단계로 자원을 사용하지 않으면서 점유하고 있는것은 곧 자원 낭비로 이어질 수 있다. 따라서 이러한 상태를 줄여 자원을 좀 더 효율적으로 사용하고자 함에서 시작하는 것이 바로 CPU 스케줄링이다.

## 스케줄링 기준
스케쥴링 알고리즘의 성능을 평가하는 척도는 다음과 같다.

1. 프로세서 이용률(CPU utilization) : 시간당 CPU를 사용한 시간의 비율
2. 처리율(Throughput) : 시간당 처리한 작업의 비율
3. 반환 시간 또는 소요 시간(Turnaround time) : 프로세스가 시작해서 끝날 때까지 걸린 시간
4. 대기 시간(Waiting time) : Ready Queue에 들어가서 CPU를 할당 받기까지 기다린 시간
5. 반응 시간 또는 응답 시간(Response time) : Interactive system에서 의뢰한 시간에서부터 반응이 시작되는 시간

시스템 입장에서의 CPU 스케줄링 성능에서 중요한 것 - CPU Utilization + Throughput (클수록 좋음)
사용자 입장에서의 CPU 스케줄링 성능에서 중요한 것 - Turnaround Time + Waiting Time + Response Time (작을수록 좋음)

## CPU 스케줄링의 구분

### 선점 스케줄링

- 프로세스가 어떠한 자원을 사용중이더라도, 우선순위에 의해 자원을 뺏길 수 있음
- 우선순위가 높은 프로세스를 빠르게 처리할때 유용
- 프로세스가 어떤 자원을 사용중일때 선점이 발생할경우 선점으로 인한 오버헤드 발생
- I/O 요청, I/O 응답, Interrupt발생, 작업완료의 상황에서 발생

### 선점 스케줄링의 종류

- SRT스케줄링 : 짧은 작업 순서대로 실행, 남은 시간이 더 짧은 프로세스가 존재하면 선점이 발생
- 라운드로빈 스케줄링 : 일정 시간만 실행하고 다 마치지 못한다면 다음 프로세스에 의해 선점. 적당한 할당시간을 정하는게 중요
- 다단계 큐 : Ready큐를 여러개 사용. 각각의 큐는 각자의 알고리즘을 수행하고, 큐사이의 우선순위가 존재, 어느한 Ready큐에 존재하는 프로세스는 다른 큐로 이동 불가
- 다단계 피드백 큐 : 다단계 큐와 비슷하지만, 프로세스가 다른 큐로 이동 가능

### 비선점 스케줄링

- 프로세스가 자원을 모두 사용하고 CPU에게 넘겨줄때 발생하는 스케줄링

### 비선점 스케줄링 종류

- SJF스케줄링 : 큐안에 있는 프로세스 중 수행시간이 짧은 것을 먼저 수행, 기아 프로세스 발생 가능성 있음
- 우선순위 스케줄링 : 응답속도를 증가시키기 위한 스케줄링으로 프로세스에게 우선순위를 부여해 우선순위가 높은 프로세스를 먼저 실행. 우선순위를 적용하기 위한 오버헤드가 많음
- FIFO 스케줄링 : Ready큐에 도착한 순서대로 CPU 자원을 사용
- 기한부 스케줄링 : 명시된 시간이나 기한내에 완료하도록 스케줄링을 구성하는방법
- HRN(Highest response ratio next) 스케줄링 : 수행시간과 대기시간을 모두 고려해 설정한 우선순위로 실행하는 방법

## 스케쥴링의 단계

### 장기 스케쥴링

어느 작업을 커널에 등록시켜 프로세스로 만들어 줄 것인가를 결정하는 것. 메모리와 디스크 사이 스케쥴링을 담당한다. 장기 스케줄러의 경우 수행 횟수가 적으며, 대부분 FIFO(First in First out)형태로 사용한다.

### 중기 스케쥴링

보류 상태의 프로세스들을 관리한다. 단기 스케쥴러가 복잡해졌을 때 우선순위가 낮은 작업을 빼오고, 나중에 혼잡이 해소되면 다시 메모리로 불러와 실행시킨다. (스왑핑) 장기와 단기 사이의 중간단계에 속한다.

### 단기 스케쥴링

CPU와 메모리 사이를 담당. 준비상태에 있는 프로세스들 중에서 어느 프로세스에게 CPU를 할당할지를 결정하는 것이며, 프로세스 스케줄러 또는 디스패쳐에 의해 수행된다. 가장 많이 호출되기에 가장 중요하다.

## Context Switching (문맥 교환)

현재 실행중인 프로세스가 다른 것으로 바뀌는 것을 Context Switching이라 한다.

이는 다음과 같은 과정이 필요하다.

1. A 프로세스의 내용 (PCB) 으로 채워져 있던 레지스터의 내용을 B 프로세스로 교체.
2. A 프로세스의 데이터들은 컨텍스트 메모리에 백업.

이를 빠르게 반복하며 여러 작업을 동시에 실행한다. 싱글 코어, 싱글 스레드의 PC라도 멀티태스킹이 가능한 건 이 덕분이다.

문맥교환은 비용이 많이 드는 작업이다. PCB를 갈아끼워야 하고 이 동안 해당 프로세서는 아무 작업도 못 하기 때문이다.

## Quiz
```
Q. SRT스케줄링과 SJF 스케줄링의 차이점은?
```
#### [참고]

> - https://m.blog.naver.com/4717010/60207137085
> - https://dduddublog.tistory.com/23
> - https://jcsoohwancho.github.io/2019-10-25-%EC%84%A0%EC%A0%90%ED%98%95-%EC%8A%A4%EC%BC%80%EC%A5%B4%EB%A7%81&%EB%B9%84%EC%84%A0%EC%A0%90%ED%98%95-%EC%8A%A4%EC%BC%80%EC%A5%B4%EB%A7%81/
> - https://m.blog.naver.com/PostView.nhn?blogId=xowns4817&logNo=221075021877&proxyReferer=https:%2F%2Fwww.google.com%2F
> - https://velog.io/@ss-won/OS-CPU-Scheduling-Algorithm
> - https://velog.io/@yerin4847/CPU-%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81CPU-Scheduling
> - https://jhnyang.tistory.com/8?category=815411
> - https://zzsza.github.io/development/2018/07/29/cpu-scheduling-and-process/
> - https://wonit.tistory.com/108
> - https://cocoon1787.tistory.com/124
> - https://jhnyang.tistory.com/28
> - https://taes-k.github.io/2019/07/16/java-class-loading/