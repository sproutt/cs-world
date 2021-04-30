# AOP

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/AOP-924ee4666a6b4b20a19190a4e82cbd8b) (작성자)
- [정회형](https://www.notion.so/hotheadfactory/AOP-28c919b3a98b44009937f3b71596f160)
- [김종근](https://github.com/Bellroute/TIL/blob/master/Spring/AOP.md)

---

## AOP란?

 AOP란 관점 지향 프로그래밍의 약자이다. 관점 지향이라는 것은 프로그래밍을 다른 시각에서 바라 보자는 의미인데, 객체 지향 프로그래밍으로 프로그래밍이 객체에게 적절한 책임과 역할을 주어 잘 협력하도록 하는 것인 즉 객체의 관점에서 바라보는 프로그래밍 방식이라면, 관점 지향은 이러한 관점을 객체가 아닌 전체적인 시각에서 바라 본본다는 의미이다.

이러한 관점 지향 프로그래밍에 대해서 좀 더 이해하기 쉽게 설명하자면 우리가 예를 들어 특정 비즈니스 로직을 수행하기 위한 핵심기능을  A→ B→ C 로 개발을 했다면 이러한 A, B, C 단계를 거칠때마다 처리가 잘 되었는지 로그를 확인하고 싶을 것이고 또한 처리시간에는 각 단계에서 얼마나 걸릴지 확인하고 싶을 것이다.

|A|B|C |
|-|-|-|
|부가기능 1|부가기능 1|부가기능 1|
|핵심기능 A|핵심기능 B|핵심기능 C|
|부가기능 2|부가기능 2|부가기능 2|

그렇다면 위와같은 구성을 가지게 될텐데 앞서말한 전체적인 시각에서 바라본다면 각각의 핵심 기능에 부가기능이 반복된다는 것을 알 수 있다. 따라서 관점지향프로그래밍이란 핵심기능과 부가기능을 나누어서 부가기능을 모듈로 만들어 핵심기능에 적용하는 프로그래밍 방식이라고 말할 수고 있고 즉 **애플리케이션 전체에 걸쳐 사용되는 기능을 재사용을** 지원하는 기술입니다.

### 장점

- 어플리케이션 전체에 흩어진 공통 기능이 하나의 장소에서 관리된다는 점
- 다른 서비스 모듈들이 본인의 목적에만 충실하고 그외 사항들은 신경쓰지 않아도 된다는 점

### 용어

- 타겟 : 핵심 기능을 담고 있는 모듈로 타겟은 부가기능을 부여할 대상
- 어드바이스 : 어드바이스는 타겟에 제공할 부가기능을 담고 있는 모듈
- 조인포인트
    - 어드바이스가 적용될 수 있는 위치
    - 타겟 객체가 구현한 인터페이스의 모든 메서드는 조인 포인트가 됨
- 포인트컷
    - 어드바이스를 적용할 타겟의 메서드를 선별하는 정규표현식
- 애스펙트
    - 애스펙트는 AOP의 기본 모듈
    - 애스펙트 = 어드바이스 + 포인트컷
    - 애스펙트는 싱글톤 형태의 객체로 존재
- 어드바이저
    - 어드바이저 = 어드바이스 + 포인트컷
    - 어드바이저는 Spring AOP에서만 사용되는 특별한 용어
- 위빙 : 포인트컷에 의해서 결정된 타겟의 조인 포인트에 부가기능(어드바이스)를 삽입하는 과정

### 스프링 AOP의 특징

1. 프록시 기반 AOP를 지원
2. 프록시(Proxy)가 호출을 가로챔
3. Spring AOP는 메서드 조인 포인트만 지원

프록시 패턴 기반의 AOP 구현체. 프록시 객체를 쓰는 이유는 접근 제어 및 부가 기능을 추가하기 위해서임

프록시 패턴(Proxy Pattern) : 실제 기능을 수행하는 객체Real Object 대신 가상의 객체Proxy Object를 사용해 로직의 흐름을 제어하는 디자인 패턴. 어떤 기능을 추가하려 할 때 기존 코드를 변경하지 않고 기능을 추가할 수 있음.
```

            |---------------|      사용    |------|
            |Inteface       |<────────────|Client|
            |---------------|             |------|
            |+operation()   |
            |---------------|
                    △
                    │구현
        ┌────────────────────────┐
        │                        │
|---------------|         |---------------|
|Real Object    |   위임   |Proxy Object   |
|---------------|<────────|---------------|
|+operation()   |         |+operation()   |
|---------------|         |---------------|
```
스프링 빈에만 AOP 적용 가능

모든 AOP 기능을 제공하는 것이 아닌 스프링 IoC와 연동하여 엔터프라이즈 애플리케이션에서 가장 흔한 문제(중복 코드, 프록시 클래스 작성의 번거로움, 객체들 간 관계 복잡도 증가 ...)에 대한 해결책을 지원하는 것이 목적

### Spring AOP 사용법
**의존성 추가**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```
Aspect 클래스
```java
@Component
@Aspect
public class PerfAspect {

	@Around("execution(* com.saelobi..*.EventService.*(..))")
	public Object logPerf(ProceedingJoinPoint pjp) throws Throwable{
		long begin = System.currentTimeMillis();
		Object retVal = pjp.proceed(); // 메서드 호출 자체를 감쌈
		System.out.println(System.currentTimeMillis() - begin);
		return retVal;
	}
}
```
**@Aspect**

Aspect 로직을 담당할 클래스에 @Aspect를 붙여 이 클래스가 Aspect를 나타내는 클래스라는 것을 명시하고 @Component를 붙여 스프링 빈으로 등록한다.

**Advice**

어드바이스는 Aspect가 "무엇을", "언제"할지를 의미하는데, "무엇"은 해당 메소드에서 이루어지는 로직을 의미하고, "언제"는 총 5개의 타입이 존재한다.

- @Before (이전)
어드바이스 타겟 메소드가 호출되기 전에 어드바이스 기능을 수행
- @After (이후)
타겟 메소드의 결과에 관계없이(즉 성공, 예외 관계없이) 타겟 메소드가 완료 되면 어드바이스 기능을 수행
- @AfterReturning (정상적 반환 이후)
타겟 메소드가 성공적으로 결과값을 반환 후에 어드바이스 기능을 수행
- @AfterThrowing (예외 발생 이후)
타겟 메소드가 수행 중 예외를 던지게 되면 어드바이스 기능을 수행
- @Around (메소드 실행 전후)
어드바이스가 타겟 메소드를 감싸서 타겟 메소드 호출전과 후에 어드바이스 기능을 수행

## Quiz
```
Q) AOP와 OOP의 차이는?

A)
- OOP : 비지니스 로직의 모듈화
      모듈화의 핵심 단위는 비지니스 로직
- AOP : 인프라 혹은 부가기능의 모듈화
      대표적 예 : 로깅, 트랜잭션, 보안 등
      각각의 모듈들의 주 목적 외에 필요한 부가적인 기능들
```
```
Q) 프록시란?
```
### 참고

- [https://lion-king.tistory.com/entry/Spring-AOP-AOP란-AOP-핵심-간단-정리](https://lion-king.tistory.com/entry/Spring-AOP-AOP%EB%9E%80-AOP-%ED%95%B5%EC%8B%AC-%EA%B0%84%EB%8B%A8-%EC%A0%95%EB%A6%AC)
- [https://shlee0882.tistory.com/206](https://shlee0882.tistory.com/206)
- [https://jojoldu.tistory.com/69](https://jojoldu.tistory.com/69)
- https://jdm.kr/blog/235
- https://dailyheumsi.tistory.com/202
- https://jojoldu.tistory.com/71
- https://engkimbs.tistory.com/746
- https://logical-code.tistory.com/118