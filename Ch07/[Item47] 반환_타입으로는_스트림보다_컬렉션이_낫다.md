# 반환 타입으로는 스트림보다 컬렉션이 낫다.

## ⛳️ 목표

- 원소 시퀀스를 반환할 때 스트림, Iterable, Collection 중 최적의 타입을 선택하는 방법을 알아보자.

<br>

## 📄 핵심 요약

### **원소 시퀀스 반환의 변천사**

- 자바 7 이전: Collection, Set, List, Iterable, 배열 사용
- 자바 8 이후: 스트림 도입으로 선택이 복잡해짐

<br>

---

### 1. 스트림과 반복

#### 스트림의 한계
- for-each 반복을 지원하지 않는다.
- Stream 인터페이스는 Iterable 메서드를 포함하지만 확장(extend)하지 않는다.

#### 해결 방법
- 어댑터 메서드 사용
```java
// Stream to Iterable 어댑터
public static <E> Iterable<E> iterableOf(Stream<E> stream) {
    return stream::iterator;
}

// Iterable to Stream 어댑터
public static <E> Stream<E> streamOf(Iterable<E> iterable) {
    return StreamSupport.stream(iterable.spliterator(), false);
}
```

<br>

### 2. 원소 시퀀스 반환 전략

#### 최선의 방법: Collection 또는 그 하위 타입
- Iterable의 하위 타입
- stream() 메서드 제공
- 반복과 스트림을 동시에 지원

#### 고려 사항
- 시퀀스의 크기가 작다면 ArrayList, HashSet 등 표준 컬렉션 사용
- 시퀀스가 큰 경우 메모리 사용에 주의

<br>

### 3. 특수한 상황의 해결 방법

#### 멱집합(Power Set) 예제
- AbstractList를 활용한 전용 컬렉션 구현
- 비트 벡터를 인덱스로 활용
- 크기 제한 필요 (30개 이하 원소)

#### 부분리스트 스트림 구현
- Stream.concat(), flatMap() 등 활용
- IntStream을 사용한 인덱스 매핑

<br>

### 4. 성능 고려사항

#### 어댑터의 단점
- 클라이언트 코드 복잡하다.
- 성능 오버헤드 발생한다. (약 2.3배 느림)

#### 권장 사항
- 가능하면 Collection 반환하라.
- 불가능한 경우 스트림 또는 Iterable 중 더 자연스러운 방식을 선택하자.

<br>

---

> #### 💡 핵심 정리
> 1. 원소 시퀀스를 반환할 때는 스트림과 반복을 모두 고려하라.
> 2. 가능하다면 Collection을 반환하자.
> 3. 메모리 제약이 있다면 전용 Collection 또는 스트림 구현을 고려하라.
> 4. 공개 API 설계 시 양쪽(스트림, 반복) 사용자를 모두 만족시키려 노력하라.