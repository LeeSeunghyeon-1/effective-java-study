## 인터페이스는 타입을 정의하는 용도로만 사용하라

## ⛳️ 목표

인터페이스의 용도를 파악하고 상수 인터페이스 안티 패턴의 해결 방법을 알아보자

<br>

## 📄 핵심 요약

- 인터페이스는 타입을 정의하는 용도로만 사용해야 한다.
- 상수를 선언할 때는 상수를 사용하는 인터페이스 혹은 클래스 내부에 선언하거나 유틸리티 클래스를 따로 만들어야 한다.

---

## 인터페이스 - 용도

클래스가 어떤 인터페이스를 구현한다는 것은 자신의 인스턴스로 무엇을 할 수 있는지를 클라이언트에 얘기해주는 것이다.

<br>

## 인터페이스 - 안티 패턴

상수 인터페이스는 인터페이스의 본 용도와 맞지 않게 메서드 없이 상수를 뜻하는 static final 필드로 이루어진 인터페이스이다.

```java
// 상수 인터페이스 안티 패턴
public interface PhysicalConstants {
	// 아보가드로 수 
	static final double AVOGADROS_NUMBER = 6.022_140_857e23;
	// 볼츠만 상수
	static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
}
```

- 클래스 내부에서 사용하는 상수는 내부 구현임에도 불구하고 공개에 노출하게 된다.
- 상수는 유틸리티 클래스를 따로 만들거나 클래스 자체에 추가해야 한다.

<br>

## 상수용 유틸리티 클래스

```java
public class PhysicalConstants {

	private PhysicalConstants() {} // 인스턴스화 방지
	
	// 아보가드로 수 
	static final double AVOGADROS_NUMBER = 6.022_140_857e23;
	// 볼츠만 상수
	static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
	
}

public class Test {
	double atoms(double mols) {
		return PhysicalConstants.AVOGADROS_NUMBER * mols;
	}
}
```

- 상수 값의 사용이 빈번하다면 정적 임포트를 활용할 수 있다.

