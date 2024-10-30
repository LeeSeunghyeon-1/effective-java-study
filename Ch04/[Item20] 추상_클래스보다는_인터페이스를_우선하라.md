### 추상 클래스보다는 인터페이스를 우선하라

## ⛳️ 목표
- 인터페이스 사용을 우선시 해야하는 이유에 대해 알아보자.

<br>

## 📄 핵심 요약

***추상 클래스란?***
- 하나 이상의 메서드를 구현하지 않은 클래스이다.
- 자식 클래스에서 반드시 구현해야 하며, 단일 상속만 지원한다.

***인터페이스란?***
- 객체의 행동을 정의하는 규약으로, 클래스에 메서드를 구현하도록 요구한다.
- 자바 8부터는 디폴트 메서드를 지원하여 인스턴스 메서드 구현 가능하다.

> 아래의 코드에서는 `Cat` 클래스가 `Animal` 인터페이스를 구현하지만, 
> `makeSound()` 메서드를 오버라이드하지 않아 기본 구현인 "Some generic animal sound"를 사용한다.

```java
// 인터페이스 정의
interface Animal {
    // 디폴트 메서드
    default void makeSound() {
        System.out.println("Some generic animal sound");
    }

    // 추상 메서드
    void eat();
}

// 인터페이스를 구현하는 클래스
class Dog implements Animal {
    @Override
    public void eat() {
        System.out.println("Dog is eating");
    }

    // 디폴트 메서드를 오버라이드하여 구현할 수 있음
    @Override
    public void makeSound() {
        System.out.println("Bark");
    }
}

// 또 다른 인터페이스를 구현하는 클래스
class Cat implements Animal {
    @Override
    public void eat() {
        System.out.println("Cat is eating");
    }

    // 디폴트 메서드를 오버라이드하지 않으면 기본 구현 사용
}

public class Main {
    public static void main(String[] args) {
        Animal dog = new Dog();
        dog.makeSound(); // "Bark" 출력
        dog.eat();       // "Dog is eating" 출력

        Animal cat = new Cat();
        cat.makeSound(); // "Some generic animal sound" 출력 (디폴트 메서드 사용)
        cat.eat();       // "Cat is eating" 출력
    }
}
```

***추상 클래스와 인터페이스 선택***
- **추상 클래스**: 상속 관계에서 공통된 기능을 제공하고자 할 때 사용한다.
- **인터페이스**: 다중 상속을 지원하고자 할 때 사용한다.

<br>

> 💡 **핵심 포인트**
> - 인터페이스는 다른 클래스를 상속하면서 동시에 구현할 수 있어 유연한 설계를 제공
> - 기존 클래스에 새로운 인터페이스를 쉽게 추가할 수 있음
> - 추상 클래스는 새로운 클래스를 추가하기 어려워 복잡한 클래스 계층 구조를 초래할 수 있음

<br>

### 1. 믹스인 사용 가능
- 인터페이스는 믹스인(mixin) 정의에 최적화되어 있어, 클래스에 선택적 행동을 추가할 수 있다.
- ex) `Comparable` 인터페이스를 통해 순서를 정할 수 있다.
- 추상 클래스로는 믹스인을 정의할 수 없으며, 두 부모 클래스를 가질 수 없기 때문에 유연성이 떨어진다.

```java
public interface Comparable<T> {
    int compareTo(T o);
}

public class Person implements Comparable<Person> {
    private String name;

    public Person(String name) {
        this.name = name;
    }

    @Override
    public int compareTo(Person other) {
        return this.name.compareTo(other.name);
    }
}
```

### 2. 계층구조가 없는 타 프레임워크 사용
- 인터페이스를 사용하면 서로 다른 타입을 계층적으로 정의하지 않고도 구현할 수 있다.
- ex) `Singer`와 `Songwriter` 인터페이스를 정의하면, 하나의 클래스에서 두 인터페이스를 모두 구현할 수 있다.

```java
public interface Singer {
    AudioClip sing(Song s);
}

public interface Songwriter {
    Song compose(int chartPosition);
}

// 가수와 작곡가를 동시에 구현
public class SingerSongwriter implements Singer, Songwriter {
    @Override
    public AudioClip sing(Song s) {
        // 구현
    }

    @Override
    public Song compose(int chartPosition) {
        // 구현
    }
}
```

### 3. 안전한 기능 향상 가능
- 인터페이스의 디폴트 메서드를 활용하여 기존 클래스에 새로운 기능을 안전하게 추가할 수 있다.
- ex) `Iterable` 인터페이스가 새로 추가되었을 때, 기존 클래스에 이 인터페이스를 구현하는 것이 가능하다.
- 인터페이스는 코드의 유지보수성을 높이고, 기능을 추가할 때 클래스 계층구조의 혼란을 줄일 수 있다.

<br>

### 추가 설명
> - 인터페이스와 추상 클래스는 각각의 장점이 있으며, 자바는 두 가지 모두를 지원한다.
> - 인터페이스는 다중 구현이 가능하므로 유연한 설계를 제공하고, 복잡한 클래스 계층 구조를 피할 수 있다.
> - 반면, 추상 클래스는 단일 상속만 지원하지만 공통 기능을 정의하는 데 유리하다.
> - 최종적으로, 인터페이스는 새로운 기능 추가 시 안전하고 강력한 수단이 될 수 있다.

### 결론
> 😄 이처럼 인터페이스를 활용하는 것이 추상 클래스보다 유리한 점이 많으므로, 클래스 설계 시 인터페이스를 우선하는 것이 좋습니다.