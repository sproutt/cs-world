# IoC / DI

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/IoC-DI-49c0d0eb86694593a48d745a143bc089) (작성자)
- [정회형](https://www.notion.so/IoC-DI-29372c0c486c41da9652ca1247b52d7f)
- [김종근](https://github.com/Bellroute/TIL/blob/master/Spring/IoC%EC%99%80_DI.md)

---

## IoC란?

**Inversion of Control**의 약자로 제어가 역전되었다는 의미다.

 그렇담 제어의 역전은 무엇인가? 여기서 제어란 바론 객체의 생명주기 관리하는 것을 말하며 이것이 역전되었다는 것은 객체의 생명주기의 주체가 달라졌다는 의미이다.

 그러면 기존의 객체의 생명주기에 대한 주체는 누구였으며 객체의 생명 주기를 어떻게 관리했는가? 바로 객체의 생명주기의 주체는 프로그래머였으며, 프로그래머는 생명주기 중 생성은 아래 코드와 같이 했어야 했다.

```java
public class Ex {
	private A a;
	
	Ex(A a){
	this.a = a;	
	}
}
```

 위와 같이 객체의 생명주기를 다룰때 필드 a를 직접 생성하여 넣어주는 것을 **의존성 주입**이라고 하는데, 이는 아래에서 자세히 살펴볼 예정이다.

 그렇다면 그전에 프로그래머가 직접 생명주기를 관리하면 어떠한 문제가 있었기에 이러한 제어권을 역전시키는 것인가?

 그에 대한 답은 바로 객체지향을 더욱 객체지향적으로 다룰 수 있기 위해서이다. 객체지향의 핵심은 **재사용성**, **확장성** 등이 될 수 있는데 객체가 이렇게 외부로 인한 영향을 줄이되 기능의 확장에는 열려 있게 하려면 바로 객체에 영향을 줄 수 있는 의존성을 줄여야 한다. 위의 코드를 예로 들면 현재 Ex객체는 A라는 객체에 의존성을 가지고 있다고 볼 수 있고, 이러한 의존성들이 많을 경우 의존성 들의 변화가 생길때 프로그래머는 직접 변경된 의존성들에  영향을 받을 수 있어 객체지향 프로그래밍의 특성일 발휘하기 어렵다. 따라서 이러한 의존성 주입을 프로그래머가 아닌 프레임워크 혹은 컨테이너에 맡기는 것이 바로 IoC이다.

### 일반적인 프로그램의 흐름은
- main() 메소드와 같이 프로그램이 시작되는 지점에서 다음에 사용할 오브젝트를 결정, 생성, 호출하는 식의 작업이 반복
- 각 오브젝트는 프로그램 흐름을 결정하거나 사용할 오브젝트를 구성하는 작업에 능동적으로 참여함
- 모든 오브젝트가 능동적으로 자신이 사용할 클래스를 결정하고, 언제 어떻게 만들지를 스스로 관장
- 모든 종류의 작업을 사용하는 쪽에서 제어하는 구조

### 제어의 역전에서는 이런 제어 흐름의 개념을 거꾸로 뒤집는다.
- 오브젝트가 자신이 사용할 오브젝트를 스스로 선택, 생성하지 않음.
- 자신도 어떻게 만들어지고 어디서 사용되는지를 알 수 없음
- 모든 제어 권한을 자신이 아닌 다른 대상에게 위임하기 때문
- 프로그램의 시작을 담당하는 엔트리 포인트(main() 메소드)를 제외하면 모든 오브젝트는 위임받은 제어 권한을 갖는 특별한 오브젝트에 의해 결정되고 만들어짐

### 왜 IoC를 사용할까?
객체의 라이프사이클을 관리하는 부분과 비즈니스 로직을 담당하는 부분으로 관심을 분리하여 각자의 역할은 충실히 행하면서 변경, 확장에는 열려있는 유연한 코드를 작성할 수 있는 구조를 설계하기 위함.

IoC는 Spring에서만 사용되는 개념이 아님. 작게는 객체 간에 디자인 패턴으로, 크게는 컨테이너, 프레임워크 역할에 적합한 구조이기 때문에 사용됨.
역할과 책임을 분리하여 변경에 유연한 코드 구조를 가져가기 위함(높은 응집도와 낮은 결합도) => OOP의 핵심(결국엔 재사용성을 위해)
컨테이너 차원에서는 엔터프라이즈 개발에 적합하게 수많은 객체 생명주기를 관리 하고 의존 관계를 설정해주고 그 외 많은 기능들을 제공하여 개발자는 비지니스 로직에 집중 할 수 있게 해주는 것이죠. (스프링이 가지는 강점)

### 적용 사례
- 서블릿 - 서블릿에 대한 제어 권한을 가진 컨테이너가 적절한 시점에서 서블릿 클래스의 오브젝트를 만들고 그 안에 메소드를 호출
- 템플릿 메소드 패턴 - 서브클래스가 구현을 담당하지만 언제 어떻게 사용될지는 슈퍼클래스에서 결정. 제어권을 상위 템플릿 메소드에 넘기고 자신은 필요할 때 호출되어 사용되도록 함.
- 프레임워크 - 프레임워크는 라이브러리의 다른 이름이 아님. 프레임워크는 분명한 제어의 역전 개념이 적용됨
서블릿(servlet) - 서버에서 웹페이지 등을 동적으로 생성하거나 데이터 처리를 수행하기 위해 자바로 작성된 프로그램
컨테이너(container) - 인스턴스의 라이프사이클을 관리, 생성된 인스턴스들에게 추가적인 기능을 제공.

### IoC의 종류

- DI(Dependency Injection)
- DL(Dependency Lookup)

## DI

DI란 의존성 주입이라는 의미로 객체간의 의존관계를 설정하는 것을 의미한다.


**[DI가 적용되지 않은 경우]**
```java
public class Toy {
  private Battery b;
  
  public A() {
    b = new BatteryA();
  }
}
```

**[DI가 적용된 경우]**
```java
// 생성자 주입
public class Toy {
  private Battery b;
  
  public A(Battery b) {
    this.b = b;
  }
}
```

### 의존성 주입의 종류

- 생성자 주입
```java
@Component
public class Toy {
  private Battery b;
  
  // spring 4.3부터 @Autowired 생략 가능
  public A(Battery b) {
    this.b = b;
  }
}
```
- 수정자 주입
```java
@Component
public class Toy {
  private Battery b;

  @Autowired
  public void setBattery(Battery b) {
    this.b = b;
	}
}
```
- 필드 주입 (@Autowired)
```java
@Component
public class Toy {
  @Autowired
  private Battery b;
}
```

가장 권장되는 것은 `생성자 주입` 이다.

- 필수적으로 사용해야 하는 레퍼런스 없이는 인스턴스를 만들지 못하도록 강제함
- 순환 참조 의존성을 알아 차릴 수 있음
- 생성자에 많은 의존성이 추가될 경우 리팩토링 시점을 감지할 수 있음
- 의존성 주입 대상 필드를 final로 불변 객체 선언할 수 있음

### 장점

1. 결합도 제거
    - 종속성이 감소해 변경에 민감하지 않음.
2. 재사용 코드
    - 일부 인터페이스의 다른 구현이 필요한 경우, 해당 구현을 사용해 components를 구성할 수 있음
3. 테스트 코드
    - 더 많은 테스트 코드 생성 가능함.
    - Mock 객체는 실제 구현의 테스트로 사용하는 객체로, 종속성을 components에 주입할 수 있을 경우, mock 구현 주입이 가능함.
4. 가독성 코드

### 스프링 프레임워크의 DI

스프링 프레임워크에서도 의존성 주입을 프레임워크에서 직접 관리해준다. 이러한 스프링프레임워크에서 의존성 관리를 담당하는 것이 IoC컨테이너고 이것의 종류로 BeanFactory 혹은 ApplicationContext가 있고 이를 상속 받거나 구현한 여러 구현체가 존재한다.

## DL

 모든 IoC 컨테이너는 각 컨테이너에서 관리해야 하는 객체들을 관리하기 위한 별도의 저장소를 가진 다. Bean에 접근하기 위하여 컨테이너에서 제공하는 API를 이용하여 사용하고자 하는 Bean을 Lookup 하는 것으로 컨테이너 API와 의존관계를 많이 가지면 가질수록 어플리케이션에 종속되는 단점이 있다.

## Quiz
```
IoC와 DI는 같은것인가?
```
## 참고

- [https://dog-developers.tistory.com/12](https://dog-developers.tistory.com/12)
- [https://biggwang.github.io/2019/08/31/Spring/IoC, DI란 무엇일까/](https://biggwang.github.io/2019/08/31/Spring/IoC,%20DI%EB%9E%80%20%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C/)
- [https://m.blog.naver.com/PostView.nhn?blogId=wwwkang8&logNo=220985550237&proxyReferer=https:%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.nhn?blogId=wwwkang8&logNo=220985550237&proxyReferer=https:%2F%2Fwww.google.com%2F)
- [https://madnix.tistory.com/entry/Spring-Framework와-IOC-DL-DI-정의](https://madnix.tistory.com/entry/Spring-Framework%EC%99%80-IOC-DL-DI-%EC%A0%95%EC%9D%98)
- https://pks424.tistory.com/entry/IoC-DI%EB%9E%80
- https://jobc.tistory.com/30
- https://biggwang.github.io/2019/08/31/Spring/IoC,%20DI%EB%9E%80%20%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C/
- https://velog.io/@gillog/Spring-DIDependency-Injection
- https://jobjava00.github.io/language/java/framework/spring/container/
- https://leejisoo860911.tistory.com/2
- https://baek.dev/post/21/
- https://velog.io/@gillog/Spring-DIDependency-Injection-%EC%84%B8-%EA%B0%80%EC%A7%80-%EB%B0%A9%EB%B2%95