# 가변인수(varargs)와 제네릭에 대해서

## ⛳️ 목표

- 가변인수와 제네릭의 조합에서 발생하는 문제를 이해하고 이를 안전하게 사용하는 방법을 익히자

<br>

## 📄 핵심 요약

***가변인수란?***

- **가변인수(Varargs)** 는 메서드의 매개변수로 임의 개수의 인수를 받을 수 있도록 지원하는 기능이다
- 메서드 호출 시 전달하는 인수의 개수를 고정하지 않고 다양하게 처리할 수 있다
- 자바에서는 `...` 문법을 사용하여 구현한다

```java
public static int sum(int... numbers) {
    int total = 0;
    for (int num : numbers) {
        total += num;
    }
    return total;
}

public static void main(String[] args) {
    System.out.println(sum(1, 2, 3));        // 출력: 6
    System.out.println(sum(4, 5, 6, 7, 8)); // 출력: 30
}
```

<br>

---

### **1. 가변인수와 제네릭의 문제점**

- **가변인수 메서드**는 호출 시 **인수들을 배열에 담아 처리**하지만 이 배열이 **외부에 노출**될 수 있다
- **제네릭의 타입 소거**와 배열의 **실체화 특성**이 충돌하게 된다
    - 제네릭 varargs는 컴파일러 경고를 발생시키며 **타입 안전성이 깨질 위험**이 있다
    - **예시 경고** : `Possible heap pollution from parameterized vararg type`

#### **2. 힙 오염 발생**

```java
static <T> T[] toArray(T... args) {
    return args; // varargs 배열을 그대로 반환
}
```

- 이 메서드가 반환하는 배열은 컴파일 시점에 `Object[]`로 생성되며 잘못된 타입으로 간주될 수 있다

**예시코드**
```java
public static void main(String[] args) {
    String[] attributes = toArray("좋은", "빠른", "저렴한"); // ClassCastException 발생
}
```

- **문제** : 컴파일러는 배열을 `Object[]`로 처리하지만 반환값을 `String[]`로 형변환하려다 실패하게 된다

<br>

---

### **3. 안전한 제네릭 가변인수 메서드 작성**

- **안전성 조건**
> 1. varargs 배열에 값을 저장하지 않는다
> 2. 배열 참조를 외부로 노출하지 않는다

#### **예제코드 : @SafeVarargs**

- 자바 7 이후 `@SafeVarargs` 애너테이션으로 경고 제거 가능하다
- 메서드가 안전하다는 확신이 있을 때만 사용할 수 있다

```java
@SafeVarargs
static <T> List<T> flatten(List<? extends T>... lists) {
    List<T> result = new ArrayList<>();
    for (List<? extends T> list : lists) {
        result.addAll(list); // 배열 내용을 읽기만 함
    }
    return result;
}
```

#### **사용 예제코드**

```java
public static void main(String[] args) {
    List<String> list1 = List.of("A", "B");
    List<String> list2 = List.of("C", "D");
    List<String> flattened = flatten(list1, list2);
    System.out.println(flattened); // 출력: [A, B, C, D]
}
```

<br>

---

### **3. 가변인수 배열을 사용하지 않는 대안**

- **varargs 배열 대신 `List` 사용**
    - 가변인수를 안전하게 처리하며 컴파일러가 타입 안전성을 검증할 수 있다

#### **대안 구현 : List 매개변수 사용**

```java
static <T> List<T> flatten(List<List<T>> lists) {
    List<T> result = new ArrayList<>();
    for (List<T> list : lists) {
        result.addAll(list);
    }
    return result;
}
```

#### **사용 예제코드**

```java
public static void main(String[] args) {
    List<String> list1 = List.of("A", "B");
    List<String> list2 = List.of("C", "D");
    List<String> flattened = flatten(List.of(list1, list2));
    System.out.println(flattened); // 출력: [A, B, C, D]
}
```

- **장점**
    - 타입 안전성을 강화할 수 있다
    - `@SafeVarargs` 애너테이션 불필요하다
- **단점**
    - 호출 시 `List.of`로 감싸는 작업이 필요하다

<br>

---

### **4. @SafeVarargs 사용 규칙**

- **@SafeVarargs 적용 가능 조건**
>- 메서드가 **재정의 불가능**(static, final, private)해야한다
>- 배열에 값을 저장하거나 참조를 외부로 노출하지 않아야한다

---

> 💡 **핵심 정리**
>
> 1. 가변인수와 제네릭은 함께 사용할 때 **타입 안전성이 깨질 위험**이 있다
> 2. **안전한 메서드 작성**
>     - 배열에 값을 저장하거나 외부로 노출하지 말 것
>     - `@SafeVarargs`를 붙여 사용자의 경고를 제거
> 3. 더 안전한 대안은 **varargs 대신 `List`를 사용하는 방법** 이다
> 4. 가변인수를 사용하더라도 **PECS(Producer-Extends, Consumer-Super)** 와 함께 타입 안전성을 보장하자