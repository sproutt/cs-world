# JVM Execution Engine
----------------

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/JVM-3-Execution-Engine-eb9b7745ba264add8f1311647d19d478)
- [정회형](https://www.notion.so/hotheadfactory/Java-Execution-Engine-7f547a1b3a914950aa893b7983fdecf3)
- [김종근](https://github.com/Bellroute/TIL/blob/master/Java/JVM-Execution%20Engine.md)

## Execution Engine이란?

Class Load 작업이 끝난 Class 파일은 Runtime Data Area의 Method Area에 배치된다. JVM은 Method Area의 Byte Code를 Execution Engine에 제공하여 Class에 정의된 내용대로 실행하게 된다.

이때, 로드된 Class의 ByteCode를 실행하는 Runtime Module이 바로 Execution Engine이다.

- ByteCode는 JVM이 실행하는 명령어(Insturction) 집합이다. 자바 바이트코드는 `.class`가 담고 있다.
- Execution Engine이 수행하는 가장 최소 단위의 명령어를 Instruction이라고 하고, 1바이트의 Opcode(operation code의 약자)와 Operand로 구성되어 있다.

### **Execution Engine 구조**

- Interpreter : 바이트코드를 한줄씩 실행하여 해석
- JIT(Just-In-Time) : 효율을 증가시키는데 사용하고, 전체 바이트 코드를 해석하여 네이티브 코드로 변경하여 메소드의 반복 호출을 확인할때마다 재차해석하지 않고 네이티브 코드를 제공
- Garbage Collector : 참조되지 않는 객체를 소멸*([JVM구조(4) - Garbage Collector](https://www.notion.so/JVM-4-Garbage-Collector-f105cdb6c08848088f437839e77e1403)에서 자세히 다룰 예정)*

![스크린샷 2021-03-10 오후 6 33 14](https://user-images.githubusercontent.com/40922963/110607938-170eee80-81cf-11eb-80c4-0e0e43449a93.png)

## Interpreter 방식

 자바 컴파일러로 부터 변환된 바이트 코드들이 ClassLoader에 의해서 Runtime Data Area에 적절히 저장이 되고 이러한 내용들을 바탕으로 코드를 한 줄씩 읽어들여 기계어로 변환시켜가면서 실행시키는 방법이다.

 한줄마다 처리하다보니 다소 속도가 느린단점이있고, 같은 코드를 여러번 반복해야할 때에도 번역과정을 거치기 때문에 다소 느리다.

## JIT(Just-In-Time) 컴파일러 방식

인터프리터 방식의 효율성을 높이기 위해 사용되며, 전체 바이트 코드를 컴파일하여 네이티브 코드로 변경하고 캐싱한다 캐싱한 내용은 코드 캐시라는 곳에 저장한다. 따라서 메서드를 호출할 때매다 JIT는 해당 부분의 네이티브 코드를 제공하여 재해석 하지 않아도 되어 효율성이 향상된다.

 네이티브 코드로 변환하면 속도가 빨라지지만 네이티브 코드로 변환하는데 비용이 발생한다. 이 때문에 반복 수행이 발생하지 않으면 interpreter 방식이 빠르다

 JIT의 성능에 영향을 주는것은 바로 자주 사용되는 메소드를 파악하고 그것을 캐싱하는 것이다. 따라서 자주 사용되는 메소드를 정하는 기준인 '컴파일 임계치'를 적절히 설정하는 것과, 네이티브 코드가 캐싱되는 코드 캐시의 크기를 적절하게 정하는 것이 '**JIT 컴파일러 튜닝'**이다.

### JIT 동작과정

1. 인라인 : 메서드들간의 호출 트리를 구성
2. 지역 최적화 : 한 번에 코드의 작은 부분을 분석하고 성능을 향상
3. 제어 흐름 최적화 : 메서드의 내부 흐름에 대한 분석과 경로 재정렬을 통한 최적호
4. 전역 최적화 : 전체 메서드 관점에서의 최적화
5. 네이티브 코드 생성(OS에 따라 다르게 생성됨)

따라서 JVM은 모든 코드를 JIT Compiler  방식으로 실행하지 않고 Interpreter 방식을 사용하다 일정한 기준이 넘어가면  JIT Compiler 방식으로 실행한다.

## AOT(Ahead of Time) 방식

- 프로그램 최초 실행시가 아닌, 그 이전에(주로 설치시에) 한번에 전체를 변환해 두고 저장한 뒤, 프로그램 실행시 마다 변환된 코드를 읽음
- 안드로이드에서 주로 사용되는데, JIT 컴파일러가 돌아가는데 실행시마다 하드웨어 전체적으로 상당한 부하가 생겨 배터리 성능 문제를 해결하기 위해 등장
- 속도 측면에서 성능 개선을 기대할 수 있지만, 메모리 공간을 많이 차지
- 번외. 안드로이드 개발자들의 성능 고민

## Native Method Interface(or JNI)

다른 언어로 작성된 코드를 자바에서 호출하도록 만들어진 규약으로 Native Method Library와 상호작용하고 C와 C++의 Native Library를 제공하는 인터페이스이며 Native Library에 있는 Native Method 들이 제공된다.

이를 통해서 JVM은 필요할때 C/C++ 라이브러리를 호출하거나 혹은 하드웨어에서 특정한 C/C++ 라이브러리에 의해 호출될 수 있다.

Native Method란? 
method에 native란 키워드가 붙어있고 그 구현을 자바가 아닌, C나 C++로 구현한 것을 말한다.

## Native Method Library

Native Method Interface를 통해 제공될 라이브러리들이 있는곳이다.

### Quiz
```
Q. 플랫폼에 독립적인게 자바의 특징인데 JNI를 사용하면 플랫폼에 종속적인게 되지 않는가? 그럼에도 불구하고 JNI가 필요한 이유는?
A. 예전에는 처리 속도 문제 때문에 JNI를 필요로 했었지만 현재는 속도 문제는 많이 해결되었다. 하지만 단지 속도 문제만이 아니라 특수한 목적으로 제작된 하드웨어에서 자바로 제어를 하고자 하는 경우에서 JNI가 필요로 하다. 왜냐하면 자바 클래스만으로 특수한 플랫폼의 기능을 다 포함할 수 없기 때문이다.
JNI관련 참고 URL : https://jetzt.tistory.com/365
```
```
C언어와 Java는 같은 동작을 하는 코드를 실행시켜도 속도에서 차이가 나는 이유를 설명해보시오
```
```
Q. 기계어와 네이티브 코드의 차이점은?
A. 기계어로 만들어지는 코드를 네이티브 코드라고 한다.
```
```
Q. JVM관점에서 자바의 성능을 어떻게 향상시킬 수 있는지?
```

### 참고 URL

- [https://www.javacodegeeks.com/2018/04/jvm-architecture-execution-engine-in-jvm.html](https://www.javacodegeeks.com/2018/04/jvm-architecture-execution-engine-in-jvm.html)
- [https://www.slipp.net/wiki/pages/viewpage.action?pageId=8880270](https://www.slipp.net/wiki/pages/viewpage.action?pageId=8880270)
- [https://velog.io/@prayme/.java%EB%A5%BC-JVM%EC%9C%BC%EB%A1%9C-%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94-%EA%B3%BC%EC%A0%95-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0](https://velog.io/@prayme/.java%EB%A5%BC-JVM%EC%9C%BC%EB%A1%9C-%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94-%EA%B3%BC%EC%A0%95-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
- [https://junshock5.tistory.com/111](https://junshock5.tistory.com/111)
- [https://namu.wiki/w/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C%20%EB%9F%B0%ED%83%80%EC%9E%84](https://namu.wiki/w/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C%20%EB%9F%B0%ED%83%80%EC%9E%84)