# equals 를 재정의한 클래스에서는 hashCode 도 재정의해라

## ⛳️ 목표

- `equals`와 `hashCode`의 재정의를 함께 진행해야하는 이유와 그렇지 않았을 상황의 문제점에 대해서 이해하자

<br>

## 📄 핵심 요약

***hashCode 란?***

- 객체를 식별하는데 사용되는 정수 값이다
- Object 클래스에 정의된 메소드이다 (책에서 계속 _Object 명세_ 언급한 이유)
- 객체의 메모리 주소를 기반으로 해시 코드를 생성한다
- 해시 기반 자료구조인 `HashMap`이나 `HashSet`에서 객체를 빠르게 검색하거나 비교하는데 사용된다

```java
String a = "Hello";
System.out.println(a.hashCode()); // 69609650
System.out.println(a.hashCode()); // 69609650 (변경 없음)
```
<br>

### 1. `equals`와 `hashCode`의 관계

- `equals`를 재정의할 때는 반드시 `hashCode`도 재정의해야 한다 
- 그렇지 않으면 해시 기반 컬렉션에서 논리적으로 동등한 객체를 제대로 처리할 수 없다

> - **⓵ 논리적 동등성**: `equals` 메서드는 두 객체가 논리적으로 동일한지 비교한다 `hashCode`는 이 논리적 동등성을 해시 기반 컬렉션에서 효율적으로 처리하기 위해 사용된다
> - **⓶ 해시 기반 컬렉션**: `HashMap`, `HashSet`과 같은 컬렉션에서 객체를 사용할 때, 동등한 객체는 동일한 해시코드를 가져야 한다
> - **⓷ 불일치 문제**: `equals`를 재정의했지만 `hashCode`를 재정의하지 않으면, 논리적으로 동일한 객체가 다른 해시코드를 가져 해시 기반 컬렉션에서 올바르게 동작하지 않는다

<br>

### 2. `hashCode` 재정의 방법

- 좋은 `hashCode` 구현은 각 필드의 값을 기반으로 적절한 해시코드를 생성해야 하며 충돌을 최소화해야 한다

> - **⓵ 핵심 필드 사용**: 객체의 중요한 필드를 사용해 해시코드를 계산해야 한다 해시코드가 각 객체에 대해 고유할수록 성능이 향상된다
> - **⓶ 해시 충돌 방지**: 다른 객체는 가능한 서로 다른 해시코드를 반환해야 한다 그렇지 않으면 해시 기반 컬렉션의 성능이 저하될 수 있다

<br>

### 3. 좋은 `hashCode` 예시

```java
import java.util.Objects;

public class PhoneNumber {
    private final int areaCode;
    private final int prefix;
    private final int lineNumber;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        PhoneNumber that = (PhoneNumber) o;
        return areaCode == that.areaCode && prefix == that.prefix && lineNumber == that.lineNumber;
    }

    @Override
    public int hashCode() {
        return Objects.hash(areaCode, prefix, lineNumber); // 핵심 필드를 이용해 해시코드 생성
    }
}
```

- 이 구현은 `equals`와 `hashCode`를 모두 재정의하여 논리적으로 동등한 객체가 동일한 해시코드를 가질 수 있도록 보장한다