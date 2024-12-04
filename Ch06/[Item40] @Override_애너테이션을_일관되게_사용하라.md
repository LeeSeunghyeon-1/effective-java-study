# @Override 애너테이션 사용의 중요성

## ⛳️ 목표

- @Override 애너테이션의 사용의 필요성을 이해하자.

<br>

## 📄 핵심 요약

### 구체 클래스란?
- 구체 클래스(Concrete Class)**는 인스턴스를 생성할 수 있는 클래스를 의미하며, 추상 클래스나 인터페이스와 달리 모든 메서드가 구현되어 있다.
- 구체 클래스는 객체 지향 프로그래밍에서 기능을 직접 구현하고 실제 객체를 생성하는 데 사용된다.

<br>

---

### 1. 구체 클래스에서의 @Override

- **@Override** 애너테이션은 상위 클래스 또는 인터페이스의 메서드를 재정의했음을 컴파일러에게 명확히 알린다.
- 이를 통해 **오타** 또는 **잘못된 메서드 시그니처**로 인해 발생하는 버그를 예방할 수 있다.

#### 잘못된 재정의 예시코드

- 다음은 equals 메서드를 재정의하려다 실수로 다중정의(Overloading)가 발생한 코드다.

```java
public class Bigram {
    private final char first;
    private final char second;

    public Bigram(char first, char second) {
        this.first = first;
        this.second = second;
    }

    public boolean equals(Bigram b) { // Object.equals를 재정의하지 않음
        return b.first == first && b.second == second;
    }

    @Override
    public int hashCode() {
        return 31 * first + second;
    }
}
```

#### 문제
- **Object.equals(Object)**를 재정의하지 않고, 잘못된 매개변수 타입(`Bigram`)을 사용했다.
- 이처럼 `@Override`가 없으면 컴파일러가 이 오류를 잡아내지 못한다.

#### 해결방법
`@Override`를 추가하여 문제를 사전에 방지
```java
@Override
public boolean equals(Object o) {
    if (!(o instanceof Bigram)) return false;
    Bigram b = (Bigram) o;
    return b.first == first && b.second == second;
}
```

#### 장점
- 컴파일 타임에 상위 클래스의 메서드와 정확히 일치하는지 확인 가능하다.
- 의도하지 않은 메서드 추가를 방지한다.

<br>

---

### 2. 인터페이스 메서드에서의 @Override

- 디폴트 메서드가 도입된 이후, 인터페이스의 메서드를 재정의할 때도 @Override를 사용하여 재정의 의도를 명확히 해야 한다.

#### 예시
```java
interface Animal {
    default void sound() {
        System.out.println("Animal sound");
    }
}

public class Dog implements Animal {
    @Override
    public void sound() { // 올바른 재정의
        System.out.println("Dog barks");
    }
}
```

#### 장점
- 인터페이스에 동일한 이름의 새로운 디폴트 메서드가 추가되더라도, 의도한 메서드 재정의 여부를 검증 가능하다.

---

> 💡 **핵심 정리**
>
> - 구체 클래스 및 인터페이스에서 메서드를 재정의할 때 항상 `@Override`를 사용하자.
> - 이를 통해 의도치 않은 다중정의, 오타, 잘못된 메서드 시그니처로 인한 오류를 방지할 수 있다.
> - `@Override`는 코드의 가독성과 유지보수성을 높이고, 컴파일 타임에 잠재적 버그를 사전에 방지한다.