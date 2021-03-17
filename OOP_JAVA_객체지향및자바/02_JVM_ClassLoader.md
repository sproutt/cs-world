# JVM ClassLoader

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/JVM-1-8c03a7155c5549278b36fd0fcad0c7af#b4ff21e316854b928cb2259ac0975d3a)
- [정회형](https://www.notion.so/hotheadfactory/Java-Classloader-e55638223450402dbd93e4db6a52ce93)
- [김종근](https://github.com/Bellroute/TIL/blob/master/Java/JVM-ClassLoader.md)

---

ClassLoader는 컴파일러에 의해서 컴파일된 *.class 파일의 자바 바이트 코드를 읽어서 JVM의 Excution Engine이 사용할 수 있도록 Runtime Data Area에 적재하는 역할을 하는 부분이다.

자바는 ClassLoader를 사용하고, 클래스를 언제 어떻게 JVM으로 로딩하고, 언로딩하는지에 대한 특정한 규칙을 갖는다. 이러한 규칙을 이해해야, ClassLoader를 좀 더 유용하게 사용할 수 있고, 개발자가 직접 커스텀 클래스로더를 작성하여 최적화를 시킬 수 있다.

</br>

### 클래스 로딩 방식

자바는 동적으로 클래스를 읽어온다. 모든 클래스는 그 클래스가 참조되는 순간에 동적으로 JVM에 링크되며, 메모리에 로딩된다. 클래스를 로딩하는 방식에는 로드타임 동적 로딩(load-time dynamic loading)과 런타임 동적 로딩(run-time dynamic loading)이 있다.

- 로드타임 동적 로딩(load-time dynamic loading)
  - 하나의 클래스를 로딩하는 과정에서 필요한 다른 클래스를 한 번에 로딩
- 런타임 동적 로딩(run-time dynamic loading)
  - Reflection과 같은 실제로 메소드가 실행될 때 로딩

</br>

### ClassLoader 종류

3가지 클래스로더에 의해 클래스가 로드 된다.

#### Bootstrap ClassLoader

- 3가지 기본 클래스로더 중 최상위 클래스로더에 담긴 JDK 클래스 파일을 로딩한다.

- ```
  {JAVA_HOME}/jre/lib/rt.jar
  ```

  - `Object.class, String.class ...`

- Native C로 구현(운영체제 별 구현이 다름)되어 있어, `String.class.getClassLoader()`는 null을 반환

#### Extention ClassLoader

- `{JAVA_HOME}/jre/lib/ext` 폴더나 `java.ext.dirs` 환경 변수로 지정된 폴더에 있는 클래스 파일을 로딩
- Java로 구현되어 있음
- `sun.misc.Launcher` 클래스 안에 static 클래스로 구현되어 있으며, `URLClassLoader`를 상속하고 있음

#### Application ClassLoader

- `-classpath(또는 -cp)`나 JAR 파일 안에 있는 Manifaest 파일의 `Class-Path` 속성 값으로 지정된 폴더에 있는 클래스를 로딩 => classpath에 속한 모든 클래스 해당
- Java로 구현되어 있음
- `sun.misc.Launcher` 클래스 안에 static 클래스로 구현되어 있으며, `URLClassLoader`를 상속하고 있음
- 개발자가 애플리케이션 구동을 위해 직접 작성한 대부분의 클래스는 애플리케이션 클래스로더에 의해 로딩됨

</br>

#### Java9에서의 클래스로더

클래스로더는 Java9에 모듈 시스템이 도입되면서 많은 변경이 있었다. 기본 클래스로더의 3계층 구조와 3원칙은 그대로 유효하지만, 모듈 시스템 도입에 맞춰 이름과 범위, 구현 내용 등이 바뀌었다.

| Java 8                  | Java 9               | 달라진 점                                                    |
| ----------------------- | -------------------- | ------------------------------------------------------------ |
| Bootstrap ClassLoader   | 이름 그대로          | - rt.jar 등이 없어짐에 따라 로딩할 수 있는 클래스의 범위가 전반적으로 축소 - 따라서 parent classloader 인자로 `null`을 넘겨주며 Bootstrap ClassLoader를 parent classloader로 사용했던 코드 수정 필요할 수 있음 |
| Extension ClassLoader   | Platform ClassLoader | - `jre/lib/ext`, `java.ext.dirs`를 지원하지 않음 - Java SE의 모든 클래스와 Java SE에는 없지만 JCP에 의해 표준화 된 모듈 내의 클래스를 볼 수 있으며, Java 8에 비해 볼 수 있는 범위가 확장됨 - `URLClassLoader`가 아닌 `BuiltinClassLoader`를 상속받아 `ClassLoaders` 클래스의 내부 static 클래스로 구현됨 |
| Application ClassLoader | System ClassLoader   | - 클래스패스, 모듈패스에 있는 클래스 로딩 - `URLClassLoader`가 아닌 `BuiltinClassLoader`를 상속받아 `ClassLoaders` 클래스의 내부 static 클래스로 구현됨 |

\* `JCP(Java Community Process)` - 자바 커뮤니티 프로세스의 약자로, 자바 플랫폼의 향후 버전 및 기능에 대한 정의에 관여하는 표준화 과정

</br>

### ClassLoader 동작 과정

[![Image](https://camo.githubusercontent.com/442d221fb5cf1b123cacd340d26a303fcf0dacbcc52d0febb0c73e527567770e/68747470733a2f2f6e65736f792e6769746875622e696f2f6173736574732f706f7374732f696d672f323032302d31312d30352d31392d34382d32332e706e67)](https://camo.githubusercontent.com/442d221fb5cf1b123cacd340d26a303fcf0dacbcc52d0febb0c73e527567770e/68747470733a2f2f6e65736f792e6769746875622e696f2f6173736574732f706f7374732f696d672f323032302d31312d30352d31392d34382d32332e706e67)

#### 1. 로딩(Loading)

- 클래스 로더가 .class 파일을 읽고, 그 내용에 따라 적절한 바이너리 데이터를 만들고 메소드 영역에 저장하는 행위
  - Loading하면서 JVM 스펙이 맞는지 확인
  - Java Version 확인

#### 2. 연결(Linking)

- 검증(Verify)
  - JVM에서 사용이 가능한 형태인지를 검증하는 작업
  - 생성된 자바 바이트코드가 적절한지 아닌지에 대해 검증
  - 검증이 실패할 경우 검증 오류를 내보냄
- 준비(Prepare)
  - Type이 필요로 하는 Memory를 할당해 주는 작업
  - 모든 정적변수의 메모리가 할당되며 기본 default값으로 할당
- 해석(Resolve)
  - 심볼릭(명확하게 정의되지 않은) 메모리 레퍼런스를 메소드 영역에 있는 실제 레퍼런스로 교체한다.
  - Constant Pool의 Symbolic Reference를 Direct Reference, 즉 실제 메모리 주소 값으로 변경해 주는 작업을 의미

#### 3. 초기화(Initialization)

- Static 변수의 값을 할당한다. (static 블럭이 있다면 이때 실행된다.)
- SuperClass 초기화를 진행한다.
- SuperClass를 초기화한 후 해당 Class의 초기화를 진행한다.

</br>

### ClassLoader 원칙

자바 클래스로더는 세 가지 원칙을 따른다.

- Delegate(위임)
  - 하위 클래스(로더)는 자신이 로딩하지 않고 상위 클래스에게 로딩을 위임한다
  - 최상위 클래스까지 위임한 후 로딩 여부를 체크하며 내려온다
  - 최종적으로 ClassNotFoundException까지 발생시킬 수 있다
- Visibility(가시성)
  - 하위 클래스로더는 상위 클래스로더가 로딩한 클래스를 볼 수 있지만, 상위 클래스로더는 하위 클래스로더가 로딩한 클래스를 볼 수 없다.
  - 만약에 개발자가 만든 클래스를 로딩하는 애플리케이션 클래스로더가 부트스트랩 클래스로더에 의해 로딩된 `String.class`를 볼 수 없다면 애플리케이션은 `String.class`를 사용할 수 없을 것이다.
  - 따라서 하위에서는 상위를 볼 수 있어야 애플리케이션이 제대로 동작할 수 있다.
- Uninqueness(유일성)
  - 하위 클래스로더는 상위 클래스로더가 로딩한 클래스를 다시 로딩하지 않게 해서 로딩된 클래스의 유일성
  - 계층 원칙에 의해 하위 클래스 로딩이 상위 클래스 로더에서 이루어질 수 있기때문에 중복 로딩을 방지하는 원칙이다

[![img](https://camo.githubusercontent.com/cc022323a47d23fb08e47ddce62e0740ef28f33906cbbd7c81974a364bf33db8/68747470733a2f2f6d656469612e766c70742e75732f696d616765732f616775677539352f706f73742f39623734353262392d663935342d343637352d623764622d3565346563323536643030352f696d6167652e706e67)](https://camo.githubusercontent.com/cc022323a47d23fb08e47ddce62e0740ef28f33906cbbd7c81974a364bf33db8/68747470733a2f2f6d656469612e766c70742e75732f696d616765732f616775677539352f706f73742f39623734353262392d663935342d343637352d623764622d3565346563323536643030352f696d6167652e706e67)

</br>

#### [참고]

> - [https://velog.io/@agugu95/%EC%9E%90%EB%B0%94-%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%A1%9C%EB%94%A9%EA%B3%BC-%EC%86%8D%EB%8F%84-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EA%B8%B0%EB%B2%95%EB%93%A4](https://velog.io/@agugu95/자바-클래스-로딩과-속도-그리고-기법들)
> - https://inor.tistory.com/4
> - [https://homoefficio.github.io/2018/10/13/Java-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%A1%9C%EB%8D%94-%ED%9B%91%EC%96%B4%EB%B3%B4%EA%B8%B0/](https://homoefficio.github.io/2018/10/13/Java-클래스로더-훑어보기/)
> - https://javacan.tistory.com/entry/1
> - https://sas-study.tistory.com/262
> - https://nesoy.github.io/articles/2020-11/ClassLoader
> - https://taes-k.github.io/2019/07/16/java-class-loading/