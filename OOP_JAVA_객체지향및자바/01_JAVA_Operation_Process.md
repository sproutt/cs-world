# JAVA 프로그램 구성
----------------

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/JVM-1-8c03a7155c5549278b36fd0fcad0c7af)
- [정회형](https://www.notion.so/hotheadfactory/Java-JVM-7ac82825736e4305affbf291cef138aa)
- [김종근](https://github.com/Bellroute/TIL/blob/master/Java/JVM%20%EA%B5%AC%EC%A1%B0.md)

```
자바의 동작과정을 이해하려면 자바의 프로그램 구성을 먼저 이해해야한다고 생각했다.
```

![_2021-03-02__3 31 17](https://user-images.githubusercontent.com/40922963/110082056-31605b00-7dd0-11eb-9b9e-664807c07c6c.png)

[대략적인 구조 파악에 적합한 이미지라 생각한다.](https://www.dazhuanlan.com/2020/05/05/5eb177b463a57/)  

![스크린샷 2021-03-02 오후 7 23 32](https://user-images.githubusercontent.com/40922963/110082067-345b4b80-7dd0-11eb-9aff-ee92b38a12e0.png)

[자세한 구조를 알기에 적합한 이미지](https://www.notion.so/JAVA-8690a40b3b3b4b06ba03c33428805b1f)

- JDK(Java Development Kit) = JRE + Dev Tools

  - Dev Tools `기본적으로 알고있는 자바 컴파일러 외에 무엇이 있는지 알아봤다.`

  `↑ [Compile Time Environment] : .java파일이 .class(바이트코드)로 컴파일되는 과정`

  ------

  `↓ [Runtime Environment] : 컴파일된 바이트 코드가 기계어로 번역되어 실행되는 과정`

  - JRE(Java Runtime Environment) : JVM + JAVA API

    - JAVA API : 

      ```
      사용자가 작성한 코드를 실행시키기 위해 미리 작성해둔 패키지 형태의 파일
      ```

      - User Interface Toolkits
      - Integration Libraries
      - Other Base Libraries
      - lang and util Base Libraries

    - JVM(Java Virtual Machine) : 

      ```
      사용자가 작성한 코드를 실행시키는 역할
      ```

      (JVM의 구조에 대해선 별개의 주제로 자세히 다룰 예정입니다.)

      - [Class Loader : 클래스 로더](https://www.notion.so/JVM-1-8c03a7155c5549278b36fd0fcad0c7af) 실행 중에 동적으로 클래스를 로드 읽은 바이트 코드를 Runtime Data Areas에 배치

      - Execution Engine : 실행 엔진 명령어 단위로 읽어들여 실행

        - Interpreter
        - JIT compiler
        - Garbage Collector

      - Runtime Data Areas : 런타임 데이터 영역 운영체제를 통해 할당받은 메모리 영역 크게 Method Area, Stack, Heap으로 구성

        추가 [JVM의 종류]

        1. Java HostSpot Client VM • 일반적으로 사용되는 클라이언트 어플리케이션 용 • 시작시간과 메모리 사용공간을 줄이는데 맞춰져있음

        2. Java HostSpot Server VM • 프로그램 실행 속도가 최대화 되도록 설계되어있음 [JVM의 규격] `규격마다 JavaAPI 구성에는 차이가 있음`

        3. Java SE : 기본 플랫폼

        4. Java EE : 기업용 서버환경

        5. Java ME : 스마트폰과 같은 저사양 임베디드 시스템에 사용

        6. Java CARD

        ![스크린샷 2021-03-02 오후 7 38 59](https://user-images.githubusercontent.com/40922963/110082083-391fff80-7dd0-11eb-98a4-24dbf138be8a.png)
	
           			[이미지 출처](https://honbabzone.com/java/java-jvm/)

### 정리

자바의 특징 중 하나는 `Write once, Run anywhere` 이다. 즉,  Write/Run을 한다면 JDK가 필요하고, Run만 하고자 한다면 JRE가 필요하다.

## JAVA 프로그램 동작과정

![_2021-03-02__8 17 39](https://user-images.githubusercontent.com/40922963/110082096-3d4c1d00-7dd0-11eb-8765-8c9329e8e092.png)
																			
[이미지 출처](http://www.tcpschool.com/java/java_intro_programming)

1. 코드 작성 : 이부분은 아직 JDK의 영역이 아니다. IDE(Eclipse, IntelliJ ...)의 부분

2. Build : 소스코드를 실행가능한 소프트웨어로 만드는 과정으로 이부분이 JDK의 영역

   - 컴파일 타임 환경 : 자바 클래스 파일(.java)를 dev tool인 자바 컴파일러가 자바 바이트코드(.class)로 변환

3. RUN : 컴파일 타임에서 만들어진 바이트 코드 및 실행가능한 소프트웨어를 실행하는일

   - 런타임 환경

     1 .클래스 로더 : 바이트 코드를 일정 과정을 통해 RDA에 로드

     1. 실행 엔진 : 자바 인터프리터 혹은 JIT 컴파일러를 통해 바이트코드를 기계어로 변환

     - `인터프리터 방식` : 최초의 방식. 자바 바이트 코드를 명령어 단위로 읽어서 실행. 한 줄씩 실행하기 때문에 느리다.

     - ```
       JIT(Just-In-Time) Compiler
       ```

        : 인터프리터 방식의 단점을 보완하기 위해 등장

       - 인터프리터 방식으로 실행하다가 적절한 시점에 바이트 코드 전체를 컴파일하여 네이티브 코드로 변경하고 이후에는 더 이상 인터프리팅하지 않고 네이티브 코드로 직접 실행하는 방식이다.
       - 네이티브 코드는 캐시에 보관하기 때문에 한 번 컴파일된 코드는 빠르게 수행된다.
       - 한 번만 실행되는 코드라면 JIT 컴파일러가 컴파일하는 게 인터프리팅하는 것보다 오래 걸리므로 인터프리팅하는 것이 유리하다.
       - 이처럼 해당 메소드가 얼마나 자주 수행되는지 체크하고 일정 정도를 넘을 때만 컴파일을 수행하는 게 좋다.

     1. RDA : 바이트 코드에 대한 내용, 작성된 코드의 변수, 프로그램 실행시 생성될 객체들을 저장하고 프로그램 실행시에 OS로부터 일정한 메모리를 할당 받고 JVM은 필요에 따라 Thread Synchronization과 GC 같은 관리 작업을 수행한다.

### Quiz

빌드와 컴파일의 차이점에 대해서 설명하시오 참고 : https://freezboi.tistory.com/39

JDK와 JRE의 차이에 대해 설명하시오

JDK에 대해서 아시나요??? 추가) 우리가 개발시에 자주 사용하는 Math와 같은 기본 라이브러리는 JDK 디렉토리 구조에서 어디에 위치할까요?

### 참고 URL

- [JVM 구조와 자바 런타임 메모리 구조 (자바 애플리케이션이 실행될 때 JVM에서 일어나는 일, 과정을 설명해줄 수 있나요?)](https://jeong-pro.tistory.com/148)
- [JVM 메모리구조](https://huelet.tistory.com/entry/JVM-메모리구조)
- [[JVM Internal\] JVM 메모리 구조](https://12bme.tistory.com/382)
- [JVM 구조 및 메모리](https://lazymankook.tistory.com/79)
- [JVM 구조](https://velog.io/@litien/JVM-구조)
- [JAVA 11로 전환해야 하는 이유](https://docs.microsoft.com/ko-kr/azure/developer/java/fundamentals/reasons-to-move-to-java-11)
- [JDK와 JRE 차이점](https://goodgid.github.io/Java-JDK-JRE/)
