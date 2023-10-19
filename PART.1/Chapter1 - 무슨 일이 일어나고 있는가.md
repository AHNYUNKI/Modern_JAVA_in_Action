# Chapter.1

# 자바에 무슨 일이 일어나고 있는가?

## 세가지 프로그래밍 개념

---

## 스트림 처리 | 1.1

<aside>
💡 스트림이란 한 번에 한 개씩 만들어지는 연속적인 데이터 항목들의 모임이다. 이론적으로 프로그램은 입력 스트림에서 데이터를 한개 씩 읽어 들이며 마찬가지로 출력 스트림으로 데이터를 한 개씩 기록한다.

</aside>

자바 8 [java.util.stream](http://java.util.stream) 패키지에 스트림 API가 추가 되었다. 스트림 패키지에 정의된 Stream<T>는 T 형식으로 구성된 일련의 항목을 의미한다. 

스트림 API의 핵심은 기존에는 한 번에 한 항목을 처리했지만 이제 자바8 에서는 우리가 하려는 작업을 (데이터베이스 질의처럼) 고수준으로 추상화해서 일련의 스트림으로 만들어 처리 할 수 있다는 것이다. 또한 스트림 파이프 라인을 이용해서 입력 부분을 여러 CPU 코어에 쉽게 할당할 수 있다는 부가적인 이득도 얻을 수 있다. 스레드라는 복잡한 작업을 사용하지 않으면서도 공짜로 병렬성을 얻을 수 있다.

## 동작 파라미터화 | 1.2

코드 일부를 API로 전달하는 기능이 추가 되었다. 이러한 기능을 이론적으로 동적 파라미터화(behavior parameterization)라고 부른다. 동작 파라미터화가 왜 중요할까? compareUsingCustomerId를 이용해 sort의 동작을 파라미터화 했던거 처럼 스트림 API는 연산의 동작을 파라미터화 할 수 있는 코드를 전달하는 사상에 기초하기 때문이다.

- 

```java
public int compareUsingCustomerId(String inv1, String inv2) {
...
}
```

## 병렬성과 공유 가변 데이터 | 1.3

자바 8 에서는 병렬성을 공짜로 얻을 수 있다. 하지만 병렬성을 공짜로 얻는 대신에 무엇을 포기해야 할까? 

스트림 메서드로 전달하는 코드의 동작 방식을 조금 바꿔야 한다. 

보통 다른코드와 동시에 실행하더라도 안전하게 실행 할 수 있는 코드를 만들려면 공유된 가변 데이터에 접근하지 않아야 한다. 이러한 함수를 순수 함수, 부작영 없는 함수, 상태 없는 함수라 부른다.

## 람다 :: 익명함수 | 1.4

---

자바 8 이전에는 클래스나 메서드를 그 자체로 값이 될 수 없었다. (이급시민) 하지만 자바 8에서 등장한 메서드 참조 즉 “ :: “ 를 이용 함으로써 그 자체로 값이 될 수 있게 만들었다. (이급 시민을 일급시민으로)

- 익명 클래스를 통한 파일 리스팅

```java
File[] hiddenFiles = new File(“ . “).listFiles(new FileFilter) {
	public boolean accept(File file) {
		return file.isHidden();	// 숨겨진 파일 필터링
}
```

- 메서드 참조를 이용한 파일 리스팅

```java
FIle[] hiddenFiles = new File(“ . “).listFiles(File::isHidden);
```

## 코드 넘겨주기 : 예제 | 1.5

---

Apple 클래스와 getColor 메서드가 있고, Apple 리스트를 포함하는 변수 inventory가 있다고 가정하자. 이때 모든 녹색 사과를 선택해서 리스트를 반환하는 프로그램을 구현하려고 한다. → “필터”

자바 8 이전에는 filterGreenApples라는 메서드를 구현 했을 것이다.

- Apple 클래스

```java
public static void main(String... args) {
    List<Apple> inventory = Arrays.asList(
        new Apple(80, "green"),
        new Apple(155, "green"),
        new Apple(120, "red")
    );
```

- Apple 클래스에서 녹색 사과만 가져오는 메서드

```java
public static List<Apple> filterGreenApples(List<Apple> inventory) {
  List<Apple> result = new ArrayList<>();
  for (Apple apple : inventory) {
    if (green.equals(apple.getColor())) {
      result.add(apple);
    }
  }
  return result;
}
```

하지만 누군가는 사과의 무게를 필터링 하고 싶을 수 있다.(복사 & 붙여넣기를 해서)

```java
public static List<Apple> filterHeavyApples(List<Apple> inventory) {
  List<Apple> result = new ArrayList<>();
  for (Apple apple : inventory) {
    if (apple.getWeight() > 150) {
      result.add(apple);
    }
  }
  return result;
}
```

이 예제에서 두 메서드는 굵게 표시된 한 줄의 코드만 다르다. (만약 버그가 있다면 복사 & 붙여넣기 한 코드는 다 수정해야 할 것이다.)

다행히 자바 8 에서는 코드를 인수로 넘겨 줄 수 있으므로 filter 메서드를 중복 구현 할 필요가 없다.

- (1) 녹색 사과를 가져오는 메서드

```java
public static boolean isGreenApple(Apple apple) {
  return "green".equals(apple.getColor());
}
```

- (2) 특정 무게를 가져오는 메서드

```java
public static boolean isHeavyApple(Apple apple) {
  return apple.getWeight() > 150;
}
```

- (3) 프레디케이트(predicate) (명확히 하기 위해 추가함 보통 java.util.function에서 임포트함)

```java
public interface Predicate<T>{
  boolean test(T t);
}
```

```java
public static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> p) {
  List<Apple> result = new ArrayList<>();
  for (Apple apple : inventory) {
    if (p.test(apple)) {
      result.add(apple);
    }
  }
  return result;
}
```

이 코드에서 녹색 사과만 가져오고 싶다면, (1)를 추가하면 될 것이고, 150 이상의 사과만 가져오고 싶다면 (2) 추가 하면 될 것이다. (물론 둘다 가져올 수도 있다.)

<aside>
💡 프레디케이트(predicate)란 무엇인가?

- 앞에서 다룬 예제에서는 Apple :: isGreenApple 메서드를 filterApples로 넘겨주었을 때 수학에서는 인수로 값을 받아 true 나 false를 반환하는 함수를 프레디케이트라고 한다.
</aside>

## 메서드 전달에서 람다로 | 1.6

---

메서드를 값으로 전달하는 것은 분명 유용한 기능이다. 

하지만 isHeavyApple, isGreenApple 처럼 한두 번만 사용할 메서드를 매번 정의하는 것은 귀찮은 일이다.

 자바 8에서는 이 문제를 람다로 간단히 해결할 수 있다.

```java
filterApples(inventory, (Apple a) -> GREEN.equals(a.getColor()) );
```

또는 다음과 같이 구현한다.

```java
filterApples(inventory, (Apple a) -> a.getWeight() > 150 );
```

심지어 다음과 같이 구현할 수도 있다.

```java
filterApples(inventory, (Apple a) -> a.getWeight() < 80 || 
																			RED.equals(a.getColor()) );
```

즉, 한번 만 사용할 메서드는 따로 정의를 구현할 필요가 없다. 

위 코드는 우리가 넘겨주려는 코드를 애써 찾을 필요가 없을 정도로 더 짧고 간결하다.

하지만 람다가 몇줄 이상 길어진다면 익명 람다보다는 코드가 수행하는 일을 잘 설명하는 이름을 가진 메서드를 정의하는 것이 더 좋다.

## 스트림 | 1.7

---

스트림 API를 이용하면 컬렉션 API와는 상당히 다른 방식으로 데이터를 처리할 수 있다.

컬렉션에서는 반복 과정을 직접 처리해야 했다. 즉, for-each 루프를 이용해서 각 요소를 반복하면서 작업을 수행 했다. 이런 방식의 반복을 외부 반복이라고 한다.

반면 스트림 API를 이용하면 루프를 신경 쓸 필요가 없다. 스트림 API에서는 라이브러리 내부에서 모든 데이터가 처리 된다. 이와 같은 반복을 내부 반복이라고 한다.

게다가 컬렉션을 이용했을 때 다른 문제가 생길 수 있다. 예를 들어 많은 요소를 가진 목록을 반복한다면 오랜 시간이 걸릴 수 도 있다.

## 멀티스레딩은 어렵다 | 1.8

---

이전 자바 버전에서 제공하는 스레드 API로 멀티스레딩 코드를 구현해서 병렬성을 이용하는 것은 쉽지않았다.

하지만 자바 8은 스트림 API로 ‘컬렉션을 처리하면서 발생하는 모호함과 반복적인 코드문제’ 그리고 ‘멀티코어 활용의 어려움’이라는 두 가지 문제를 모두 해결 했다.

**필터링**,  **추출, 그룹화,** 이러한 동작들을 쉽게 병렬화할 수 있다는 점도 변화의 동기가 되었다.

언뜻 보면 스트림 API가 컬렉션 API와 아주 비슷한 방식으로 동작한다고 생각할 수도 있다. 

둘의 차이점은 컬렉션은 어떻게 데이터를 저장하고 접근할지에 중점을 두는 반면 스트림은 데이터에 어떤 계산을 할 것인지 묘사하는 것에 중점을 둔다는 점을 기억하자.

스트림은 스트림내의 요소를 쉽게 병렬로 처리할 수 있는 환경을 제공한다는 것이 핵심이다.

컬렉션을 필터링할 수 있는 가장 빠른 방법은 컬렉션을 스트림으로 바꾸고, 병렬로 처리한 다음에. 리스트로 다시 복원 하는 것 이다.

스트림과 람다 표현식을 이용하면 ‘병렬성을 공짜로’ 얻을 수 있으며 리스트에서 무거운 사과를 순차적으로 또는 병렬로 필터링 할 수 있다.

- 순차 처리 방식

```java
import static java.util.stream.Collectors.toList;
List<Apple> heavyApples = 
						inventory.Strem().filter((Apple a) -> a.getWeight() > 150)
														.collect(toList());
```

- 병렬 처리 방식

```java
import static java.util.stream.Collectors.toList;
List<Apple> heavyApples = 
						inventory.parallelStream().filter((Apple a) -> a.getWeight() > 150)
														.collect(toList());
```

## 디폴트 메서드와 자바모듈 | 1.9

---

자바 8에는 인터페이스를 쉽게 바꿀 수 있도록 디폴트 메서드를 지원한다.

디폴트 메서드는 특정 프로그램을 구현하는 데 도움을 주는 기능이 아니라 미래에 프로그램이 쉽게 변화할 수 있는 환경을 제공하는 기능이다.

```java
List<Apple> heavyApples1 = 
						inventory.Strem().filter((Apple a) -> a.getWeight() > 150)
														.collect(toList());
List<Apple> heavyApples2 = inventory.parallelStream()
														.filter((Apple a) -> a.getWeight() > 150)
														.collect(toList());
```

자바 8 이전에는 List<T>가 stream이나 parallelStream 메서드를 지원하지 않는다는 것이 문제다. 따라서 이 예제는 컴파일할 수 없는 코드다. 가장 간단한 해결책은 직접 인터페이스를 만드는 방법인데, 이 방법은 사용자에게 큰 고통을 안겨준다.

이미 컬렉션 API의 인터페이스를 구현하는 많은 컬렉션 프레임워크가 존재한다. 인터페이스에 새로운 메서드를 추가한다면 인터페이스를 구현하는 모든 클래스는 새로 추가된 메서드를 구현해야 한다. 

상상만 해도 괴롭지 않는가?

다행히 자바 8은 구현 클래스에서 구현하지 않아도 되는 메서드를 인터페이스에 추가할 수 있는 기능을 제공한다.

이를 **디폴트 메서드** 라고 부른다.

디폴트 메서드를 이용하면 기존의 코드를 건드리지 않고도 원래의 인터페이스 설계를 자유롭게 확장할 수 있다.

예를 들어 자바 8에서는 List에 직접 sort 메서드를 호출할 수 있다.

```java
default void sort(Comparator<? super E> c) {
	Collections.sort(this, c);
}
```

따라서 자바 8 이전에는 List를 구현하는 모든 클래스가 sort를 구현해야 했지만 자바 8부터는 디폴트 sort를 구현하지 않아도 된다.

하나의 클래스에서 여러 인터페이스를 구현할 수 있지 않는가? 그러므로 여러 인터페이스에 다중 디폴트 메서드가 존재할 수 있다는 것은 결국 다중 상속을 허용된다는 의미일까? 엄밀히 다중 상속은 아니지만 어느 정도 ‘그렇다’라고 말할 수 있다.

## 함수형 프로그래밍에서 가져온 다른 유용한 아이디어 | 1.10

---

자바 8에서는 NullPointer 예외를 피할 수 있도록 도와주는 Optional<T> 클래스를 제공한다.

Optional<T>는 값을 갖거나 갖지 않을 수 있는 컨테이너 객체이다.

즉, 형식 시스템을 이용해서 어떤 변수에 값이 없을 때 어떻게 처리할지 명시할 수 있다.

또한 (구조적)패턴 매칭 기법도 있다. 패턴 매칭은 수학에서 다음 예제처럼 사용한다.

```java
f(0) = 1
f(n) = n*f(n-1) 그렇지 않으면
```

자바에서는 if-then-else나 switch문을 이용했을 것이다.

다른 언어에서는 if-then-else 보다 패턴 매칭으로 더 정확한 비교를 구현할 수 있다는 사실을 증명했다.

물론 자바에서도 다형성, 메서드 오버라이딩을 이용해서 if-then-else를 대신하는 비교문을 만들 수 있다. 하지만 지금 우리는 기능을 구현할 수 있는가가 아닌 언어 설계를 논하고 있음을 기억하자.

## 마치며 | 1.11

---

- 언어 생태계의 모든 언어는 변화해서 살아남거나 그대로 머물면서 사라지게 된다. 지금은 자바의 위치가 견고하지만 코볼과 같은 언어의 선례를 떠올리면 자바가 영원히 지배적인 위치를 유지할 수 있는 것은 아닐 수 있다.
- 자바 8은 프로그램을 더 효과적이고 간결하게 구현할 수 있는 새로운 개념과 기능을 제공한다.
- 기존의 자바 프로그래밍 기법으로는 멀티코어 프로세서를 온전히 활용하기 어렵다.
- 함수는 일급값이다. 메서드를 어떻게 함수형값으로 넘겨주는지, 익명 함수(람다)를 어떻게 구현하는지 기억하자.
- 자바 8의 스트림 개념 중 일부는 컬렉션에서 가져온 것이다. 스트림과 컬렉션을 적절하게 활용하면 스트림의 인수를 병렬로 처리할 수 있으며 더 가독성이 좋은 코드를 구현할 수 있다.
- 기존 자바 기능으로는 대규모 컴포넌트 기반 프로그래밍 그리고 진화하는 시스템의 인터페이스를 적절하게 대응하기 어려웠다. 자바 9에서는 모듈을 이용해 시스템의 구조를 만들 수 있고 디폴트 메서드를 이용해 기존 인터페이스를 구현하는 클래스를 바꾸지 않고도 인터페이스를 변경할 수 있다.
- 함수형 프로그래밍에서 null 처리 방법과 패턴 매칭 활용 등 흥미로운 기법을 발견했다.