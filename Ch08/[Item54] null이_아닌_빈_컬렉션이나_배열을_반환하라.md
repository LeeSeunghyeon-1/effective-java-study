# null이 아닌, 빈 컬렉션이나 배열을 반환하라

## ⛳️ 목표

null을 반환하지 않고 빈 컬렉션과 배열을 반환해야 하는 이유와 방법을 알아보자

<br>

## 📄 핵심 요약

- null 반환 시 클라이언트에서 방어 로직이 필요하며, 존재하지 않을 시 언제 어떤 오류가 발생할지 예측할 수 없다.
- 빈 컬렉션과 배열 생성이 성능 저하의 주범이 아닌 이상 크게 성능 차이가 나지 않는다.
- 빈 컬렉션과 배열을 매번 생성하지 않고 미리 선언하여 활용할 수 있다.

---

## null 반환

주변에서 흔히 볼 수 있는 null을 반환하는 코드이다.

```java
private final List<Cheese> cheesesInStock = ...;

public List<Cheese> getCheeses() {
	return cheesesInStock.isEmpty() ? null : new ArrayList<>(cheesesInStock);
}
```

- 이 경우 클라이언트에서 null 상황을 처리하는 코드를 추가로 작성해야 한다.
- 클라이언트에서 방어 코드를 작성하지 않을 경우, 당장은 문제가 발생하지 않더라도 수년 후에 문제가 발생할 수 있다.

<br>

## 빈 컨테이너 할당 시 비용 문제

빈 컨테이너를 할당하는 데도 비용이 드니 null을 반환하는 쪽이 낫다는 주장이 틀린 두 가지 이유를 알아보자.

- 성능 분석 결과 이 할당이 성능 저하의 주범이 확인되지 않는 한(아이템 67), 이 정도의 성능 차이는 신경 쓸 수준이 되지 않는다.
- 빈 컬렉션과 배열은 굳이 새로 할당하지 않고도 반환할 수 있다.

<br>

### [1] 빈 컬렉션 반환

```java
public List<Cheese> getCheeses() {
	return new ArrayList<>(cheesesInStock);	
}
```

- 만약 빈 컬렉션 할당이 성능을 눈에 띄게 떨어뜨릴 경우에는 매번 똑같은 빈 ‘불변’ 컬렉션을 반환할 수 있다. (e.g. `return Collections.emptyList();`, `return Collections.emptyMap();`)
- 하지만, 위의 경우 최적화에 해당하니 수정 전과 후의 성능을 측정하여 실제로 성능이 개선되는지 확인해야 한다.

<br>

### [2] 빈 배열 반환

길이가 0인 배열을 반환하라.

```java
public Cheese[] getCheeses() {
	return cheesesInStock.toArray(new Cheese[0]);
}
```

- 이 방식이 성능을 떨어뜨릴 것 같다면 미리 길이 0짜리 배열을 선언해두고 반환하면 된다. (길이 0인 배열은 모두 불변이다.)

```java
// 좋은 예
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];
...
return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);

// 나쁜 예 (전달하는 배열을 미리 할당하면 오히려 성능이 떨어질 수 있다.)
return cheeseInStock.toArray(new Cheese[cheesesInStock.size()]);
```