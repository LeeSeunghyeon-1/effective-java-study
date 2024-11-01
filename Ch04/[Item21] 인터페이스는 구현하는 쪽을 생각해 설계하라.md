## 인터페이스는 구현하는 쪽을 생각해 설계하라

## ⛳️ 목표

디폴트 메서드를 추가함으로써 발생할 수 있는 문제점에 대해 인지하고 방지할 수 있는 방법을 알아보자

<br>

## 📄 핵심 요약

***디폴트 메서드는***

- 기존 구현체에서 재정의하지 않으면 의도와 다르게 오작동을 할 수 있다.
- 꼭 필요한 경우가 아니라면 추가하지 않는 것이 좋다.

---

## 디폴트 메서드

자바 8부터 인터페이스에서 디폴트 메서드를 사용할 수 있게 되었다.

- **장점** : 기존 인터페이스에 디폴트 메서드를 추가할 수 있게 되었다.
- **단점** :  기존 구현체들 중 오작동을 일으킬 수 있는 위험이 생겼다.

<br>

## 디폴트 메서드 - 오작동을 일으키는 경우

[예제 코드]

```java
// 자바 8의 Collection 인터페이스에 추가된 디폴트 메서드
default boolean removeIf(Predicate<? super E> filter) {
	Objects.requireNonNull(filter);
	boolean result = false;
	for (Iterator<E> it = iterator(); it.hasNext();) {
		if (filter.test(it.next)) {
			it.remove();
			result = true;
		}
	}
	return result;
}
```

- 반복자를 이용해 순회하면서 각 원소를 인수로 넣어 `Predicate`를 호출한다.
- `Predicate`가 true를 반환하면 반복자의 remove 메서드를 호출해 원소를 제거한다.
- Collection 인터페이스를 구현하는 Thread-safe 한 클래스에서 위의 디폴트 메서드를 재정의하지 않는다면 해당 메서드를 호출하게 됐을 때 동시성 문제가 발생할 수 있다.

> **Predicate (Java 8 도입)**
함수형 인터페이스로 입력된 값에 대해 조건을 검사하여 true 또는 false를 반환한다. `boolean test(T t)` 메서드가 있어 특정 조건에 따라 주어진 인수 t에 대해 true 또는 false를 반환한다.
>

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
numbers.removeIf(n -> n % 2 == 0);  // 짝수인 요소를 삭제
System.out.println(numbers);  // 결과: [1, 3, 5]
```

<br>

## 디폴트 메서드 - 오작동을 방지하는 방법

- 꼭 필요한 경우가 아니라면 기존 인터페이스에 디폴트 메서드로 새 메서드를 추가하지 않도록 한다.
- 디폴트 메서드가 인터페이스로부터 메서드를 제거하거나 기존 메서드의 시그니처를 수정하는 용도가 아님을 명심해야 한다.