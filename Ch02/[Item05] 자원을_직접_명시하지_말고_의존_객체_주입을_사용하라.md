# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

## ⛳️ 목표

의존성 주입 기법과 장점을 알아 보자

## 📄 핵심 요약

***의존 객체 주입 기법은***

- 클래스가 내부적으로 하나 이상의 자원에 의존할 때 사용할 수 있다.
- 클래스의 유연성, 재사용성, 테스트 용이성을 개선할 수 있다.

---

## 의존성 주입(DI, Dependency Injection)

### 전통적인 방식

클래스가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원을 사용해야 할 때 어떻게 해야 할까?

```java
public class SpellChecker {
	
	private final Lexicon dictionary;
	
	public SpellChecker(Lexicon dictionary) {
		this.dictionary = Objects.requireNonNull(dictionary);
	}
	
	public boolean isValid(String word) {...}
	public List<String> suggestions(String typo) {...}
}
```

의존성 객체를 필드를 통해 주입한다.

<br>

**[장점]**

- 불변을 보장하기 때문에 멀티스레드 환경에서 문제없이 사용할 수 있다.

<br>

**[단점]**

- 의존성이 많아지면 코드가 지저분해 질 수 있다.
- 이때는 의존 객체 주입 프레임워크를 사용하자. (e.g Dagger, Guice, Spring)

<br>

### Supplier<T> 인터페이스를 활용한 방식

**Supplier<T>**

```java
@FunctionalInterface
public interface Supplier<T> {
    T get();
}
```

- 자바 8에 도입된 함수형 인터페이스로, 파라미터 없이 객체를 반환하는 메소드를 정의한다.
- 호출될 때마다 객체를 제공하는 역할을 한다.

<br>

[예제]

***Service.class***

```java
// Service class
class Service {

	private final Supplier<Dependency> dependencySupplier;
	
	public Service(Supplier<Dependency> dependencySupplier) {
	    this.dependencySupplier = dependencySupplier;
	}
	
	public void doWork() {
	    Dependency dependency = dependencySupplier.get(); // 필요할 때마다 객체를 가져옴
	    dependency.performTask();
	}
}

```

<br>

***클라이언트 코드*** > Supplier를 사용해 객체 동적 생성 및 의존성 주입

```java

public class Main {
    public static void main(String[] args) {
        // Supplier를 사용하여 Dependency 객체를 필요할 때 생성
        Supplier<Dependency> dependencyFactory = () -> new Dependency();

        // Service에 Supplier를 주입
        Service service = new Service(dependencyFactory);
        
        service.doWork();
    }
}
```

<br>

**[장점]**

- `동적 객체 생성` : 같은 인터페이스로 서로 다른 구현체를 상황에 맞게 동적으로 반환할 수 있다.
    - `한정적 와일드 카드 타입(bounded wildcard type)`을 사용해 팩터리의 타입 매개변수를 제한하면 클라이언트는 자신이 명시한 타입의 하위 타입이라면 무엇이든 생성할 수 있는 팩터리를 넘길 수 있다.
- `지연 초기화` : 객체를 필요할 때 생성할 수 있어 성능 최적화나 메모리 절약에 도움이 된다.

---

### 번외. 그래서 DI는 왜 사용하는 걸까?

**1️⃣ 유연성 향상**

자원을 외부에서 관리하게 되므로 클래스는 객체 생성에 대한 책임을 지지 않아도 되고 외부 설정에 따라 다양한 객체를 주입할 수 있다.

<br>

2️⃣ **코드 재사용성 증가 및 유지보수성 향상**

의존성을 직접 명시하지 않으므로 클래스가 구체적인 구현에 종속되지 않고 추상화된 인터페이스를 통해 의존성을 해결할 수 있다.

- 클래스 간의 결합도(coupling)을 낮춘다.
- 다양한 상황에 맞춘 유연한 구현이 가능해진다.

<br>

**3️⃣ 테스트 용이성**

의존성 주입을 사용하면 테스트 시에 목(Mock) 객체를 주입할 수 있어 실제 자원에 의존하지 않고 테스트가 가능하다.

```java
Supplier<Dependency> mockDependencySupplier = () -> new MockDependency();
Service service = new Service(mockDependencySupplier);
```

- 비용이 큰 자원(네트워크 호출, 데이터베이스 연결 등)을 실제로 사용할 필요 없이 테스트 시에 빠르고 간편하게 시뮬레이션할 수 있다.

<br>

4️⃣ **단일 책임 원칙(SRP) 준수**

객체 생성을 외부에서 담당하기 때문에 클래스는 오직 비즈니스 로직에만 집중할 수 있다.