# JVM Runtime Data Area

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/JVM-2-Runtime-Data-Area-38bbf667a2e244a788728a2029a3b158)
- [정회형](https://www.notion.so/hotheadfactory/Java-Runtime-Data-Area-4364d7bb71044454949d634905dd4926)
- [김종근](https://github.com/Bellroute/TIL/blob/master/Java/JVM-Runtime%20Data%20Area.md)

---

Runtime Data Area는 JVM이 프로그램을 수행하기 위해 운영체제로부터 할당 받는 메모리 영역이다. Runtime Data Areas는 각각의 목적에 따라 5 개의 영역으로 나뉘게 된다.

- PC Registers
- JVM Stacks
- Native Method Stacks
- Method Area
- Heap

PC Register와 JVM Stack, Native Method Stack은 Thread 별로 생성되고, Method Area와 Heap은 모든 Thread에 공유된다.

## Runtime Data Area의 구조

![img](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F63ace912-0e3f-4b63-b9b7-c1964cf2aa52%2F_2021-03-07__10.49.18.png?table=block&id=9a618384-b14d-4000-a307-bbd7add50ef7&width=960&userId=&cache=v2)

이 중 PC Register, JVM Stack, Native Method Stack은 스레드별로 존재하며, Heap, Method Area는 모든 스레드가 공유한다.

### PC register

각 스레드가 생성될 때마다 생성되는 영역으로 현재 스레드가 수행 중인 부분의 주소를 가지고 있다. PC Register를 통해 다수의 스레드가 명령에 혼란을 빚지 않고 순차적으로 잘 실행됨을 보장해 준다.

**PC Register는 Register-Base가 아닌 Stack-Base로 작동한다.**

프로그램의 실행은 CPU에서 명령(Instruction)을 수행하는 과정으로 이루어진다. CPU는 이러한 명령을 수행하는 동안 필요한 정보를 레지스터라고 하는 CPU 내의 기억장치(=register)를 사용하는데 이와 같은 방법을 Register-Base라 한다.

[![registerAdd](https://camo.githubusercontent.com/4a0ea0d49c0e6058d713ad730f87550731ec996b5f22e40644fef6b2a5d49ba8/68747470733a2f2f6d61726b66616374696f6e2e66696c65732e776f726470726573732e636f6d2f323031322f30372f72656769737465726164645f7468756d622e706e673f773d34353626683d323234)](https://camo.githubusercontent.com/4a0ea0d49c0e6058d713ad730f87550731ec996b5f22e40644fef6b2a5d49ba8/68747470733a2f2f6d61726b66616374696f6e2e66696c65732e776f726470726573732e636f6d2f323031322f30372f72656769737465726164645f7468756d622e706e673f773d34353626683d323234)

```
1. ADD R1, R2, R3 ;        # Add contents of R1 and R2, store result in R3
```

그림을 보면 알 수 있듯이 Register 기반 모델에서는 한 줄의 명령으로 연산을 수행한다는 점에서 간단하지만, 피연산자들의 주소를 모두 명시적으로 참조해야한다. (Stack 모델에 비해 명령이 짧고, 오버헤드 적고, 빠름)

반면, Java의 PC Register는 Stack-Base로 작동한다. JVM은 CPU에 직접 Instruction을 수행하지 않고 Stack에서 Operand를 뽑아 내어 이를 별도의 메모리 공간에 저장하는 방식을 취한다.

[![stackAdd](https://camo.githubusercontent.com/4728747faf6bb70e090930bd46650414272bef8608c3d354f9daba77984a8c07/68747470733a2f2f6d61726b66616374696f6e2e66696c65732e776f726470726573732e636f6d2f323031322f30372f737461636b6164645f7468756d622e706e673f773d33353626683d313333)](https://camo.githubusercontent.com/4728747faf6bb70e090930bd46650414272bef8608c3d354f9daba77984a8c07/68747470733a2f2f6d61726b66616374696f6e2e66696c65732e776f726470726573732e636f6d2f323031322f30372f737461636b6164645f7468756d622e706e673f773d33353626683d313333)

```
1. POP 20
2. POP 7
3. ADD 20, 7, result
4. PUSH result
```

Stack 기반 모델에서는 데이터를 꺼내서 처리하고 결과를 LIFO방식으로 밀어 넣는 방식으로 수행되기 때문에, POP이나 PUSH와 같은 연산 수행으로 오버헤드가 발생하지만, 모든 피연산자들에 주소를 부여할 필요없이 Stack pointer만 이용하면 된다. (피연산자들의 주소를 전부 명시하지 않아도 됨. 추상화 수월)

**왜 Stack-Base를 선택했나?**

자바는 다기종의 디바이스에서 균일하게 동작할 수 있음을 보장하고자 했다. 디바이스마다 레지스터 수는 달라서 가정할 수 없었음. 그래서 레지스터 기반으로 하게되는 순간 하드웨어의 구현에 종속된다. 스택을 쓰게되면 계산과정은 복잡할 수 있어도 하드웨어의 스펙에 최소한의 관여만 할 수 있게 된다.

### JVM Stacks

> Thread 별로 하나씩 존재하며, 메소드가 호출될 때 수행 정보(메소드 호출 주소, 매개 변수, 지역 변수, 연산 스택)가 프레임(Frame)이라는 단위로 추가(push)하고 메소드가 종료되면 해당 프레임을 제거(pop)하는 동작을 수행
>
> Thread가 쓸 수 있는 스택의 사이즈를 넘기게 되면 `StackOverflowError`가 발생한다.

**stack frame**

- 프레임은 메소드가 호출될 때마다 만들어지며, 메소드 상태 정보를 저장한다.

- 다음의 3가지로 구성되어 있다.

  [![img](https://camo.githubusercontent.com/28b5fa786f77649c9fef2ee2ca50f68f23ea892b2a1c164e035391169dbc2e78/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f323338584c2f6274717a5a6336513972542f3574345861694f4f73365a6157525a596751446f32302f696d672e706e67)](https://camo.githubusercontent.com/28b5fa786f77649c9fef2ee2ca50f68f23ea892b2a1c164e035391169dbc2e78/68747470733a2f2f626c6f672e6b616b616f63646e2e6e65742f646e2f323338584c2f6274717a5a6336513972542f3574345861694f4f73365a6157525a596751446f32302f696d672e706e67)

  - Local Variables
    - 메소드의 지역 변수(파라미터 포함)들을 저장
  - Operand Stack
    - 메소드 내 계산을 위한 작업 공간.
    - CPU Resgister와 유사
    - 대부분의 Bytecode Instruction은 값을 Local Variable과 Operand Stack 사이에서 양방향으로 옮기는 작업을 포함 ([참고](https://mia-dahae.tistory.com/108))
  - Frame Data
    - Constant pool 정보 (== constant pool의 포인터 정보)
    - Normal Method Return (method가 정상 종료 했을 때의 정보들)
    - Exception Dispatch (비정상 종료했을 때 발생하는 Exception 관련 정보들)

### Native Method Stack

자바 외의 언어로 작성된 네이티브 코드(CPU와 운영체제(OS)가 직접 실행할 수 있는 코드)를 위한 스택. Java Native Interface를 통해 호출하는 C/C++ 코드를 수행하기 위한 스택이며, 다른언어에서 제공되는 Method 정보가 저장.

### Heap Area

Heap Area또한 모든 스레드가 공유하는 영역으로, 객체를 위한 영역이다. 즉, 생성이 되면 소멸도 되는 데이터들을 관리하고 있다. 이러한 데이터로는 new를 통해 생성된 객체, 배열, 불변객체가 있다. 이러한 객체는 Stack Area의 변수나 다른 객체의 필드에서 참조가 가능하다. 그리고 한정된 메모리 공간에 무한정으로 객체가 생성 될 수는 없고 따라서 필요에 의해 Heap 메모리 영역을 관리할 필요가 있는데 이 역할을 담당하는 것이 Garbage Collector*([JVM구조(4) - Garbage Collector](https://www.notion.so/JVM-4-Garbage-Collector-f105cdb6c08848088f437839e77e1403)에서 따로 다룰 것이다.)*이다.



### Method Area

> Class Loader가 적재한 클래스(또는 인터페이스)에 대한 메타데이터 정보가 저장되는 영역
>
> JVM이 시작할 때 생성되고 모든 스레드가 공유하는 영역으로, 런타임 상수풀(runtime constant pool), 필드(field) 데이터, 메소드(Method) 데이터, 메소드 코드, 생성자(constructor) 코드 등 클래스와 인터페이스와 관련된 데이터들을 분류하고 저장한다.
>
> 프로그램의 시작부터 종료가 될 때까지 메모리에 남아있음.

- Type Information: 클래스와 관련한 모든 정보
- Constant Pool : 상수로 선언한 데이터들이 저장되는 곳
- Field Information : 클래스의 멤버 변수
- Method Information : Class 멤버메서드의 이름, 리턴타입, 매개변수, 접근제어자에 대한 정보
- Static Variable: static으로 선언되는 모든 클래스 변수

