# 불필요한 객체 생성을 피하라

## ⛳️ 목표

불필요하게 객체 생성을 피하고 재사용 가능한 객체를 활용하는 방법에 대해 이해하자

<br>

## 📄 핵심 요약

***메모리와 성능을 최적화하기 위해서***

- 불필요한 객체는 생성하지 말자
- 정적 팩토리 메서드를 활용하여 불변 객체를 재사용하자
- 오토박싱을 주의하자

<br>

## 불필요한 객체 생성 Case

### 1️⃣ new String(”필요없는 객체”)

String을 아래처럼 새로운 객체로 할당하면 어떻게 될까?

```java
String string = new String("woojoo");
```

- Heap 메모리 영역에 객체가 생성되며 가비지 컬렉션의 대상이 된다.
- Constant pool에 저장되는 문자열 리터럴은 이미 존재하는 리터럴의 주소 값을 참조하기 때문에 인스턴스를 재사용할 수 있다.

<br>

### 2️⃣ 계속 생성되는 객체

`String.matches()`는 정규표현식으로 문자열 형태를 확인할 때 사용된다.

```java
static boolean isRonamNumeral(String s) {
	return s.matches("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
```

하지만 결국 호출되는 메소드를 확인해 보면 아래와 같이 new 생성자를 통해 Pattern 인스턴스를 생성해내는 것을 알 수 있다.

```java
public static Pattern compile(String regex) {    
	return new Pattern(regex, 0);
}
```

- String을 객체로 생성했을 때와 마찬가지로 Pattern 객체도 가비지 컬렉션 대상이 된다.
- 정규 표현식을 컴파일하는 과정 자체가 복잡하고 최적화된 상태로 변환해야 하기 때문에 계산 비용이 높다.

<br>

***개선 코드***

반복적으로 같은 패턴을 사용할 때 객체를 재사용하면 성능을 최적화할 수 있다.

```java
public class RomanNumber {

    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

    static boolean isRomanNumeral(String s) {
        return ROMAN.matcher(s).matches();
    }
}
```

<br>

## 효율적이고 안전한 객체 생성 Case

### 1️⃣ 정적 팩터리 메서드

- 불변 객체만이 아니라 가변 객체일지라도 중간에 재사용하게 변경할 수 있다.
- [[아이템 1]](https://github.com/LeeSeunghyeon-1/effective-java-study/blob/main/Ch02/%5BItem01%5D%20%EC%83%9D%EC%84%B1%EC%9E%90_%EB%8C%80%EC%8B%A0_%EC%A0%95%EC%A0%81_%ED%8C%A9%ED%84%B0%EB%A6%AC_%EB%A9%94%EC%84%9C%EB%93%9C%EB%A5%BC_%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC.md)

<br>

### 2️⃣ 어댑터

실제 작업은 뒷단 객체에 위임하고 자신은 제2의 인터페이스 역할을 해주는 객체이다.

```java
// 출처 : gihub.com/keesun/study

public class UsingKeySet {

    public static void main(String[] args) {
        Map<String, Integer> menu = new HashMap<>();
        menu.put("Burger", 8);
        menu.put("Pizza", 9);

        Set<String> names1 = menu.keySet();
        Set<String> names2 = menu.keySet();

        names1.remove("Burger");
        System.out.println(names2.size()); // 1
        System.out.println(menu.size()); // 1
    }
}
```

- keySet을 호출할 때마다 매번 같은 Set 인스턴스를 반환한다.
- 그러므로 names1에서 “Burger”를 제거했을 때 모든 곳에서 다 제거된다.

<br>

### 3️⃣ 오토박싱 (주의)

기본 타입과 박싱된 기본 타입을 섞어 쓸 때 자동으로 상호 변환해주는 기술이다.

```java
public class AutoBoxingExample {

    public static void main(String[] args) {
        long start = System.currentTimeMillis();
        Long sum = 0l;
        for (long i = 0 ; i <= Integer.MAX_VALUE ; i++) {
            sum += i;
        }
        System.out.println(sum);
        System.out.println(System.currentTimeMillis() - start);
    }
}
```

- sum 변수를 long이 아닌 Long으로 선언해서 i가 sum에 더해질 때마다 Long 인스턴스가 만들어진다.
- sum 타입을 long으로 바꿔주면 속도가 약 10배 이상 빨라진다.
- 박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.

<br>

### 4️⃣ 방어적 복사(Defensive Copying)

객체의 불변성을 유지하거나 외부에서 객체의 내부 상태가 변경되는 것을 방지하기 위해 사용되는 기법이다.

**[목적]**

- 클라이언트 코드가 객체의 상태를 직접 수정하는 것을 막는다.
- 불변성을 보장하여 의도치않은 버그를 방지한다.

<br>

**[예제 코드]**

***Constructor***

원본 객체를 복사한 후 저장하거나 반환한다.

```java
public class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        this.start = new Date(start.getTime()); // 방어적 복사
        this.end = new Date(end.getTime());     // 방어적 복사
    }

    public Date getStart() {
        return new Date(start.getTime()); // 방어적 복사
    }

    public Date getEnd() {
        return new Date(end.getTime());   // 방어적 복사
    }
}

```

***Getter***

getter 메서드로 내부 mutable 객체를 외부에 그대로 반환하면 외부에서 해당 객체의 상태를 변경할 수 있으므로, 반환하기 전에 해당 객체를 복사하여 반환한다.

```java
public List<String> getValues() {
    return new ArrayList<>(values); // 방어적 복사
}

```

<br>

**[장점]**

- 객체의 불변성을 유지할 수 있다.
- 외부 코드가 내부 객체의 상태를 직접 변경하는 것을 방지하여 안전성을 높인다.
- 예기치 않은 버그 발생 가능성을 줄인다.

<br>

**[단점]**

- 객체를 복사하는 성능 비용이 발생한다. (복사해야 할 객체가 많거나 크다면 부정적)
- 코드가 복잡해질 수 있다.