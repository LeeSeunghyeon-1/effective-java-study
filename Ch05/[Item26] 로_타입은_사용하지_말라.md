# 로 타입은 사용하지 말라

## ⛳️ 목표

- 원시 타입의 문제점을 이해하고 제네릭 타입을 활용하는 방법을 알아보자

<br>

## 📄 핵심 요약
- 원시 타입을 사용하면 타입 안정성이 떨어지며, 런타임 오류가 발생할 수 있다.
- 제네릭을 사용하면 타입 안정성이 보장되며, 컴파일 시점에 오류를 잡을 수 있다.

<br>

## Generic 도입 이전

- `Raw Type(원시 타입)`으로 타입을 명시하지 않고 컬렉션을 생성하고 형변환하여 조회했다.
- 타입을 명시하지 않기 때문에 어떤 타입의 데이터를 넣어도 오류없이 컴파일되고 실행된다.
- 하지만, 해당 데이터를 조회하려고 할 때 `ClassCastException`이 발생한다.

```java
public static void main(String[] args) {
	List rawList = new ArrayList(); // 타입이 명시되지 않음
	rawList.add("Hello"); // 문자열 입력
	rawList.add(10);  // 숫자 입력
	
	String hello = (String) rawList.get(0);
}
```

<br>

## Generic 도입 이후 (Java 5)

- 제네릭은 클래스, 인터페이스, 메서드에서 타입을 매개변수화한다.
- 객체 생성 시 타입을 명시적으로 지정할 수 있어 런타임에서 발생할 수 있는 예외 발생을 방지할 수 있다.

```java
public static void main(String[] args) {
	List<String> stringList = new ArrayList(); // String 타입 명시
	stringList.add("Hello"); // 문자열 입력
	//stringList.add(10);  // 숫자 입력 시 컴파일 오류 발생
}
```

<br>

## 원시 타입(Raw Type)을 만든 이유

- 기존에 제네릭 없이 사용된 코드와의 호환성을 위해 로 타입을 지원하기로 했다.
- Generic 구현에는 `소거(Erasure) 방식`을 사용해서 이전 버전과의 호환성을 유지한다.

```
📢 소거(Erasure) 방식
타입 정보를 컴파일 시에만 사용하고, 실행 시에는 원시 타입으로 변환하는 것을 말한다.
```

<br>

### 활용 예

- class 리터럴

```java
List.class         // O
List<String>.class // X
```

- instanceof 연산자

```java
if (o instanceof Set) {      
	Set<?> s = (Set<?>) o; 
	...
}
```

<br>

## 비한정적 와일드카드 타입(Unbounded WildCard Type)

- `비한정적 와일드카드 타입`은 제네릭을 쓰고 싶지만 실제 타입 매개변수는 제한없이 사용하고 싶을 때 사용할 수 있다.
- 컬렉션에 타입을 지정할 때 `물음표 마크(?)`를 사용한다.
- 타입 안정성을 유지하면서도 다양한 타입을 다뤄야 할 때 활용할 수 있다.

```java
static int numElementsInCommon(Set<?> s1, Set<?> s2) { ... }
```

<br>

### 원시타입과의 차이점

- 원시 타입 컬렉션에는 아무 원소나 넣을 수 있기에 타입 불변식을 훼손한다.
- 비한정적 와일드카드 타입 컬렉션에는 null 외에는 어떤 원소도 넣을 수 없다. 타입 정보를 알 수 없기 때문에 원소를 넣으면 컴파일 오류가 발생한다.

```java
Set set = new HashSet();       // raw type
Set<?> set = new HashSet<>();  // generic unbounded wildcard type
```