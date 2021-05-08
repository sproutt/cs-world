# 상태패턴

스터디원들의 개별 정리자료 링크

- [김민섭](https://www.notion.so/0e50de69b8fb4181aeb6bfb57c78f0cc#0a96a46633d448c3ae0914e764a38cf6) (작성자)
- [정회형](https://www.notion.so/hotheadfactory/518641f26b0e41f78dd9541c971c916e)
- [김종근](https://github.com/Bellroute/TIL/blob/master/DesignPattern/%EC%83%81%ED%83%9C%ED%8C%A8%ED%84%B4(StatePattern).md)

---

## 적용상황

 예를 들어 우리 각자(객체)는 매일과 같이 공부할때 항상 같은 노력을 들여서 공부를 하지 않는다. 공부에도 평상시에 진행하는 공부가 있고, 시험이나 테스트를 앞둔 시점에서 하는 공부가 있고, 또한 반대로 여유가 생겨 공부를 평소보다 조금하는 경우도 생긴다. 이처럼 같은 행위임에도 불구하고 상황에 따라 행위가 달라질때 적용할 수 있는 것이 상태 패턴이다.

![Untitled](https://user-images.githubusercontent.com/40922963/117544618-4621cf00-b05d-11eb-91a1-5ffc09675cb9.png)


위의 이미지와 같이 하나의 동작이 상태에 따라서 달라지도록 상태에 대한 인터페이스를 만들고 구현체에서 각각 상태에 맞게 행위를 다르게 구현하는 방식이다.

## 상태 패턴 적용전

```java
public class Before {
    public static void main(String[] args) {
        Seob seob = new Seob();
        seob.state = "NORMAL";
        seob.study();
        seob.state = "BUSY";
        seob.study();
        seob.state = "FREE";
        seob.study();
    }
}

class Seob {
    public String state;

    public void study() {
        switch (state) {
            case "NORMAL":
                System.out.println("평소대로 공부한다.");
                break;
            case "BUSY":
                System.out.println("열심히 공부한다.");
                break;
            case "FREE":
                System.out.println("여유있게 공부한다.");
                break;
            /*
            case "GIVEUP":
                System.out.println("떄려친다..");
             */
        }
    }
}
```
![_2021-05-06__4 21 46](https://user-images.githubusercontent.com/40922963/117544625-49b55600-b05d-11eb-885d-c59880248e45.png)
## 상태 패턴 적용 후

### 인터페이스

```java
public interface State {
    public abstract void study(SeobSeob seobseob);
}

class NormalState implements State {

    @Override
    public void study(SeobSeob seobseob) {
        System.out.println("천천히 공부한다.");
        //평상시로 공부하다가 바쁜시즌이 돌아옴
        seobseob.changeState(new BusyState());
    }
}

class BusyState implements State {

    @Override
    public void study(SeobSeob seobseob) {
        System.out.println("열심히 공부한다.");
        //열심히 공부했으니 상태가 여유롭게 됨
        seobseob.changeState(new FreeState());
    }
}

class FreeState implements State {

    @Override
    public void study(SeobSeob seobseob) {
        System.out.println("여유있게 공부한다.");
        //여유있게 공부하다보니 다시 조금씩 할일이 생겨 평상시로 돌아옴
        seobseob.changeState(new NormalState());
    }
}
```

### 컨텍스트

```java
class SeobSeob {
    State state;

    SeobSeob(State state) {
        this.state = state;
    }

    public void study() {
        this.state.study(this);
    }

    public void eating() {
        System.out.println("어떠한 상황에도 밥은 맛있게 먹는다.");
    }

    public void changeState(State newState) {
        this.state = newState;
    }
}
```

### 실행

```java
public class After {
    public static void main(String[] args) {
        SeobSeob seobseob = new SeobSeob(new NormalState());
        System.out.println("=====공부 시작=======");
        seobseob.study();
        seobseob.eating();
        System.out.println("=====몇 주 뒤=======");
        seobseob.study();
        seobseob.eating();
        System.out.println("=====몇 주 뒤=======");
        seobseob.study();
        seobseob.eating();
        System.out.println("=====몇 주 뒤=======");
        seobseob.study();
        seobseob.eating();
        System.out.println("=====몇 주 뒤=======");
        seobseob.study();
        seobseob.eating();
        System.out.println("=====몇 주 뒤=======");
        seobseob.study();
        seobseob.eating();
        System.out.println("=====몇 주 뒤=======");
        seobseob.study();
        seobseob.eating();
    }
}
```

### 실행 결과
![_2021-05-06__5 11 12](https://user-images.githubusercontent.com/40922963/117544691-85502000-b05d-11eb-9708-dd9c5a103645.png)

### 장점

- 상태가 추가 되더라도 콘텍스트 코드가 받는 영향이 최소화됨
- 기존에는 상태가 많이질수록 조건문이 길어져 복잡도가 높아졌는데, 상태 패턴을 적용하면 클래스위 갯수가 증가할뿐 코드에 복잡도에는 영향을 주지 않음
- 유지보수에 장점이 있음
- 상태별로 관리 되기 때문에 수정에 이점이있음

## 상태패턴의 상태 변경 주체

### 콘텍스트 객체 (SeobSeob)

**장점**

- 상태의 객체의 갯수가 적을때 유리
- 상태 객체는 자신의 할 작업에만 집중 가능

**단점**

- 콘텍스트 코드가 복잡해질 수 있음

### 상태 객체 (NormalState, BusyState, FreeState)

**장점**

- 콘텍스트에 영향을 주지 않으면서 상태를 추가하거나 상태 변경 규칙을 바꿀 수 있음

**단점**

- 상태 객체의 수가 증가할 수록 상태 변경 규칙을 파악하기에 어려울 수 있음
- 한 상태 클래스에서 다른 상태 클래스에 대한 의존도가 발생

> 따라서 상태 객체의 갯수와 상황에 맞는 상태 변경 주체를 선택할 필요가 있다.
