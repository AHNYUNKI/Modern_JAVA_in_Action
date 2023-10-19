# Chapter.3 - 람다 표현식

---

## 람다란 무엇인가? | 3.1

---

람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이라고 할 수 있다.

람다 표현식에는 이름은 없지만, 파라미터 리스트, 바디, 반환 형식, 발생할 수 있는 예외 리스트는 가질 수 있다.

**람다의 특징**

- 익명
    
    보통의 메서드와 달리 이름이 없으므로 익명이라 표현한다. 구현해야 할 코드에 대한 걱정거리가 줄어든다.
    
- 함수
    
    람다는 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 부른다. 하지만 메서드처럼 파라미터 리스트, 바디, 반환 형식, 가능한 예외 리스트를 포함한다.
    
- 전달
    
    람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있다,
    
- 간결성
    
    익명 클래스처럼 많은 자질구레한 코드를 구현할 필요가 없다.
    

람다를 이용하면 간결한 방식으로 코드를 전달할 수 있다. 람다가 자바 8 이전의 자바로 할 수 없었던 일을 제공하는 것은 아니다. 다만 동작 파라미터를 이용할 때 익명 클래스 등 판에 박힌 코드를 구현할 필요가 없다.

**결과적으로 코드가 간결하고 유연해진다.**

예를 들어 커스텀 Comparator 객체를 기존보다 간단하게 구현할 수 있다.

- 람다를 사용하지 않았을 경우

```java
Comparator<Apple> byWeight = new Comparator<Apple>() {
	public int compare(Apple a1, Apple a2) {
		return a1.getWeight().compareTo(a2.getWeight());
	}
};
```

- 람다를 사용 했을 경우

```java
Comparator<Apple> byWeight = 
		(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

코드가 훨씬 간결해졌다.

사과 두 개의 무게를 비교하는 데 필요한 코드를 전달할 수 있다. 람다 표현식을 이용하면 compare 메서드의 바디를 직접 전달하는 것처럼 코드를 전달할 수 있다. 

## 어디에, 어떻게 람다를 사용할까? | 3.2

---

2장에서 구현했던 필터 메서드에도 람다를 활용할 수 있다.

```java
List<Apple> greenApples =
		filter(inventory, (Apple a) -> GREEN.equals(a.getColor()));
```

함수형 인터페이스라는 문맥에서 람다 표현식을 사용할 수 있다. 위 예제에서 함수형 인터페이스 Predicate<T>를 기대하는 filter 메서드의 두 번째 인수로 람다 표현식을 전달했다.

### 함수형 인터페이스 | 3.2.1

2장에서 만든 Predicate<T>가 함수형 인터페이스이다. 

Predicate<T>는 오직 하나의 추상 메서드만 지정하기 때문이다.

```java
public interface Predicate<T> {
	boolean test (T t);
}
```

간단히 말해 함수형 인터페이스는 정확히 하나의 추상 메서드를 지정하는 인터페이스다.

지금까지 살펴본 자바 API의 함수형 인터페이스로 Comparator, Runnable 등이 있다.

```java
public interface Comparator<T> {
	int compare(T o1, T o2);
}

public interface Runnable {
	void run();
}

public interface ActionListener extends EventListener {
	void actionPerformed(ActionEvent e);
}

public interface Callable<V> {
	V call() throws Exception;
}

public interface PrivilegedAction<T> {
	T run();
}
```

- 퀴즈 3-2 - 함수형 인터페이스
    
    다음 인터페이스 중 함수형 인터페이스는 어느 것인가?
    
    ```java
    public interface Adder {
    	int add(int a, int b);
    }
    
    public interface SmartAdder extends Adder {
    	int add(double a, double b);
    }
    
    public interface Nothing {
    }
    ```
    
    정답 → Adder만 함수형 인터페이스이다.
    

람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으므로 전체 표현식을 함수형 인터페이스의 인스턴스로 취급할 수 있다.

함수형 인터페이스보다는 덜 깔끔하지만 익명 내부 클래스로도 같은 기능을 구현할 수 있다.

```java
Runable r1 = () -> System.out.println("Hello World 1"); // 람다 사용

Runable r2 = new Runnable() { // 익명 클래스 사용
	public void run() {
		System.out.println("Hello World 2");
	}
};

public static void process(Runnable r) {
	r.run();
}
process(r1);
process(r2);
process(() -> System.out.println("Hello World 3")); // 직접 전달된 람다 표현식
```

### 함수 디스크립터 | 3.2.2

함수형 인터페이스의 추상 메서드 시그니처는 람다 표현식의 시그니처를 가리킨다.

람다 표현식의 시그니처를 서술하는 메서드를 **함수 디스크립터**라고 부른다.

예를 들어 Runnable 인터페이스의 유일한 추상 메서드 run은 인수와 반환값이 없으므로 Runnable 인터페이스는 인수와 반환값이 없는 시그니처로 생각할 수 있다.

- 퀴즈 3-3 - 어디에 람다를 사용할 수 있는가?
    
    다음 중 람다 표현식을 올바로 사용한 코드는?
    
    ```java
    1. execute(() -> {});
    	 public void execute(Runnable r) {
    		 r.run();
    }
    
    2. public Callable<String> fetch() {
    		 return () -> "Tricky example ;-)";
    }
    
    3. Predicate<Apple> p = (Apple a) -> a.getWeight();
    ```
    
    정답 → 1번과 2번
    

## 람다 활용 : 실행 어라운드 패턴 | 3.3

---

자원 처리(예를 들면 데이터베이스의 파일 처리)에 사용하는 순환 패턴은 자원을 열고, 처리한 다음에, 자원을 닫는 순서로 이루어진다.

설정과 정리 과정은 대부분 비슷하다.

즉, 실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태를 갖는다.

|                           초기화/준비 코드 |
| --- |
|                                 작업 A |
|                           정리/마무리 코드 |

|                           초기화/준비 코드 |
| --- |
|                                 작업 B |
|                           정리/마무리 코드 |

위 표와 같은 형식의 코드를 **실행 어라운드 패턴**이라고 부른다.

```java
public String processFile() throws IOException {
	try (BufferedReader br =
					new BufferedReader(new FileReader("data.txt"))) {
		return br.readLine(); // 살제 필요한 작업을 하는 행이다.
	}
}
```

### 1 단계 : 동작 파라미터화를 기억하라. | 3.3.1

현재 코드는 파일에서 한 번에 한 줄만 읽을 수 있다.

한 번에 두 줄을 읽거나 가장 자주 사용되는 단어를 반환하려면 기존의 설정, 정리 과정은 재사용하고 processFile 메서드만 다른 동작을 수행하도록 명령할 수 있다면 좋을 것이다.

processFile의 동작을 파라미터화 해보자.

processFile 메서드가 BufferedReader를 이용해서 다른 동작을 수행할 수 있도록 processFile 메서드로 동작을 전달해야 한다.

람다를 이용하면 동작을 전달할 수 있다.

processFile 메서드가 한 번에 두 행을 읽게 하려면 우선 BufferedReader를 인수로 받아서 String을 반환하는 람다가 필요하다.

```java
String result = processFile(BufferedReader br) ->
														br.readLine() + br.readLine());
```

### 2단계 : 함수형 인터페이스를 이용해서 동작 전달 | 3.3.2

함수형 인터페이스 자리에 람다를 사용할 수 있다.

따라서 BufferedReader → String과 IOException을 던질 수 있는 시그니처와 일치하는 함수형 인터페이스를 만들어야한다.

이 인터페이스를 BufferedReaderProcessor라고 정의하자.

```java
@FunctionalInterFace
public interface BufferedReaderProcessor {
	String process(BufferedReader b) throws IOException;
}
```

정의한 인터페이스를 processFile 메서드의 인수로 전달할 수 있다.

```java
public String processFile(BufferedReaderProcessor p) throws IOExceptiom {
	...
}
```

### 3단계 : 동작 실행 | 3.3.3

이제 BufferedReaderProcessor에 정의된 process 메서드의 시그니처와 일치하는 람다를 전달할 수 있다,

람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으며 전달된 코드는 함수형 인터페이스의 인스턴스로 전달된 코드와 같은 방식으로 처리한다.

따라서 processFile 바디 내에서 BufferedReaderProcessor 객체의 process를 호출할 수 있다.

```java
public String processFile(BufferedReaderProcessor p) throws IOException {
	try (BufferedReadr br =
								new BufferedReader(new FileReader("data.txt"))) {
		return p.preocess(br); // BufferedReader 객체 처리
	}
}
```

### 4단계 : 람다 전달 | 3.3.4

- 한 행을 처리하는 코드

```java
String oneLine =
		processFile((BufferedReader br) -> br.readLine());
```

- 두 행을 처리하는 코드

```java
String towLines =
		processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

지금까지 함수형 인터페이스를 이용해서 람다를 전달하는 방법을 확인했다.

- 실행 어라운드 패넡을 적용하는 네 단계의 과정

```java
=================================================
public String processFile() throws IOExceptiom {
	try (BufferedReader br =
						new BufferedReader(new FileReader("data.txt"))) {
		return br.readLine();
	}
}
=================================================
public interface BufferedReaderProcessor {
	String process(BufferedReader b) throws IOException;
}

public String processFile(BufferedReaderProcessor p) throws IOException {
	...
}
=================================================
public String processFile(BufferedReaderProcessor p) throws IOExceptiom {
	try (BufferedReader br =
							new BufferedReader(new FileReader("data.txt"))) {
		return p.process(br);
	}
}
=================================================
String oneLine = processFile((BufferedReader br) -> br.readLine());
String oneLine = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

## 함수형 인터페이스 사용 | 3.4

---

함수형 인터페이스는 오직 하나의 추상 메서드를 저장한다.

함수형 인터페이스의 추상 메서드는 람다 표현식의 시그니처를 묘사하고, 그것을 **디스크립터** 라고 한다.

다양한 람다 표현식을 사용하려면 공통의 함수 디스크립터를 기술하는 함수형 인터페이스 집합이 필요하다.

### Predicate | 3.4.1

`java.util.function.Predicate<T>`인터페이스는 test라는 추상 메서드를 정의하여 test는 제네릭 형식 T의 객체를 인수로 받아 불리언을 반환한다.

전에 만들었던 인터페이스와 같은 형태인데 따로 정의할 필요 없이 바로 사용할 수 있다는 점이 특징이다.

T 형식의 객체를 사용하는 불리언 표현식이 필요한 상황에서 Predicate 인터페이스를 사용할 수 있다.

- Predicate 예제

```java
@FunctionalInterface
public interface Predicate<T> {
	boolean test(T t);
}

public <T> List<T> filter(List<T> list, Predicate<T> p) {
	List<T< results = new ArrayList<>();
	for(T t : list) {
		if(p.test(t)) {
			results.add(t);
		}
	}
	return results;
}

Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
```

### Consumer | 3.4.2

`java.util.function.Consumer<T>` 인터페이스는 제네릭 형태 T 객체를 받아서 void를 반환하는 accept라는 추상 메서드를 정의한다.

T 형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 Consumer 인터페이스를 사용할 수 있다.

forEach와 람다를 이용해서 리스트의 모든 항목을 출력하는 예제

- Consumer 예제

```java
@FunctionalInterface
public interface Consumer<T> {
	void accept(T t);
}

public <T> void forEach(List<T> list, Consumer<T> c) {
	for(T t : list) {
		c.accept(t);
	}
}

forEach(
	Arrays.asList(1, 2, 3, 4, 5),
	(Integer i) -> System.out.println(i) // Consumer의 accept 메서드를 구현하는 람다
);
```

### Function | 3.4.3

`java.util.function.Function<T, R>` 인터페이스는 제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 apply를 정의한다. 입력을 출력으로 매핑하는 람다를 정의할 때 Function 인테페이스를 활용할 수 있다

String 리스트를 인수로 받아 각 String의 길이를 포하하는 Integer 리스트로 변환하는 map 메서드 예제

- Function 예제

```java
@FunctionalInterface
public interface Function<T, R> {
	R apply(T t);
}

public <T, R> List<R> map(List<T> list, function<T, R> f) {
	List<R> result = new ArrayList<>();
	for(T t : list) {
		result.add(f.apply(t));
	}
	return result;
}

// [7, 2, 6]
List<Integer> 1 = map(
	Array.asList("lam
);
```

### 기본형 특화

자바의 모든 형식은  참조형 아니면 기본형에 해당한다.

하지만 제네릭 파라미터에는 참조형만 사용할 수 있다. 제네릭의 내부 구현 때문에 어쩔 수 없는 일이다.

자바에서는 기본형을 참조형으로 변환하는 기능을 제공한다.

이 기능을 **박싱**이라 하고, 반대에 경우에는 **언박싱**이라고 한다.

그리고 프로그래머가 편리하게 코드를 구현할 수 있도록 박싱과 언박싱이 자동으로 이루어지는 **오토박싱**이라는 기능도 제공한다.

- int → Integer

```java
List<Integer> list = new ArrayList<>();
for (int i = 300; i < 400; i++) {
	list.add(i);
}
```

하지만 이런 변환 과정은 비용이 소모된다.

박싱한 값은 기본형을 감싸는 래퍼며 힙에 저장된다. 따라서 박싱한 값은 메모리를 더 소비하며 기본형을 가져올 떄도 메모리를 탐색하는 과정이 필요하다.

자바 8에서는 기본형을 입출력으로 사용하는 상황에서 오토방식 동작을 피할 수 있도록 특별한 버전의 함수형 인터페이스를 제공한다.

예를 들어 아래 예제에서 IntPredicate는 1000이라는 값을 박싱하지 않지만, Predicate<Integer>는 1000이라는 값을 Integer 객체로 박싱한다.

```java
public interface IntPredicate {
	boolean test(int t);
}

IntPredicate evenNumbers = (int i) -> i % 2 == 0;
evenNumbers.test(1000); //참(박싱 없음)

Predicate<Integer> oddNumbers = (Integer i) -> i % 2 != 0;
oddNumbers.test(1000); // 거짓(박싱)
```

일반적으로 특정 형식을 입력으로 받는 함수형 인터페이스의 이름 앞에는 DoublePredicate, IntConsumer, LongBinaryOperator, IntFunction처럼 형식명이 붙는다

Function 인터페이스는 ToIntFunction<T>, IntToDoubleFunction 등의 다양한 출력 형식 파라미터를 제공한다.

## 형식 검사, 형식 추론, 제약 | 3.5

---

람다 표현식 자체에는 람다가 어떤 함수형 인터페이스를 구현하는지의 정보가 포함되어 있지 않다. 따라서 람다 표현식을 더 제대로 이해하려면 람다의 실제 형식을 파악해야 한다.

### 형식 검사 | 3.5.1

람다가 사용되는 콘텍스트를 이용해서 람다의 형식을 추론할 수 있다.

어떤 콘텍스트에서 기대되는 람다 표현식의 형식을 **대상 형식**이라고 부른다.

- 람다 표현식을 사용할 때 실제 어떤 일이 일어나는지 보여주는 예제

```java
List<Apple> heavierThan150g = 
						filter(inventory, (Apple apple) -> apple.getWeight() > 150);
```

다음과 같은 순서로 형식 확인 과정이 진행된다.

1. filter 메서드의 선언을 확인한다.
2. filter 메서드는 두 번째 파라미터로 Predicate<Apple> 형식(대상 형식)을 기대한다.
3. Predicate<Apple>은 test라는 한 개의 추상 메서드를 정의하는 함수형 인터페이스이다.
4. test 메서드는 Apple을 받아 boolean을 반환하는 함수 디스크립터를 묘사한다.
5. filter 메서드로 전달된 인수는 이와 같은 요구사항을 만족해야 한다.

### 같은 람다, 다른 함수형 인터페이스 | 3.5.2

대상 형식이라는 특징 때문에 같은 람다 표현식이더라도 호환되는 추상 메서드를 가진 다른 함수형 인터페이스로 사용될 수 있다.

예를 들어 이전에 살펴본 Callable과 PrivilegedAction 인터페이스는 인수를 받지 않고 제네릭 형식 T를 반환하는 함수를 정의한다.

```java
Callable<Integer> c = () -> 42;
PrivilegedAction<Integer> p = () -> 42;
```

위 코드에서 첫 번째 할당문의 대상 형식은 Callable<Integer>고, 두 번째 할당문의 대상 형식은 PrivilegedAction<Integer>이다.

### 형식 추론 | 3.5.3

자바 컴파일러는 람다 표현식이 사용된 콘텍스트(대상 형식)를 이용해서 람다 표현식과 함수형 인터페이스를 추론한다.

즉, 대상 형식을 이용해서 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그니처도 추론할 수 있다.

결과적으로 컴파일러는 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 이를 생략할 수 있다.

```java
List<Apple> greenApples =
					filter(inventory, apple -> GREEN.equals(apple.getColor()));
```

여러 파라미터를 포함하는 람다 표현식에서는 코드 가독성 향상이 더 두드러진다.

### 지역 변수 사용 | 3.5.4

지금까지 살펴본 모든 람다 표현식은 인수를 자신의 바디 안에서만 사용했다.

하지만 람다 표현식에서는 익명 함수가 하는 것처럼 **자유 변수**를 활용할 수 있다. 이와 같은 동작을 **람다 캡처링**이라고 부른다.

- portNumber 변수를 캡처하는 람다 예제

```java
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
```

하지만 자유 변수에도 약간의 제약이 있다.

람다는 인스턴스 변수와 정적 변수를 자유롭게 캡처할 수 있다. 하지만 그러려면 지역 변수는 명시적으로 final로 선언되어 있어야 하거나 실질적으로 final로 선언된 변수와 똑같이 사용되어야 한다.

즉, 람다 표현식은 한 번만 할당할 수 있는 지역 변수를 캡처할 수 있다.

### 지역 변수의 제약

인스턴스 변수는 힙에 저장되는 반면 지역 변수는 스택에 위치한다.

람다에서 지역 변수에 바로 접근할 수 있다는 가정하에 람다가 스레드에서 실행된다면 변수를 할당한 스레드가 사라져서 변수 할당이 해제되었는데도 람다를 실행하는 스레드에서는 해당 변수에 접근하려 할 수 있다.

따라서 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공한다.

복사본의 값은 바뀌지 않아야 하므로 지역 변수에는 한 번만 값을 할당해야 한다는 제약이 생긴 것이다.

또한 지역 변수의 제약 때문에 외부 변수를 변화시키는 일반적인 명령형 프로그래밍 패턴에 제동을 걸 수 있다.

## 메서드 참조 | 3.6

---

메서드 참조를 이용하면 기존의 메서드 정의를 재활용해서 람다처럼 전달할 수 있다. 때로는 람다 표현식보다 메서드 참조를 사용하는 것이 더 가독성이 좋으며 자연스러울 수 있다.

```java
inventory.sort((Apple a1, Apple a2) ->
												a1.getWeight().compareTo(a2.getWeight()));
```

다음은 메서드 참조와 java.util.Comparator.comparing을 활용한 코드이다.

```java
inventory.sort(comparing(Apple::getWeight)); // 첫 번째 메서드 참조
```

### 요약 | 3.6.1

메서드 참조는 특정 메서드만을 호출하는 람다의 축약형이라고 생각할 수 있다.

예를 들어 람다가 ‘이 메서드를 직접 호출해’라고 명령한다면 메서드를 어떻게 호출해야 하는지 설명을 참조하기보다는 메서드명을 직접 참조하는 것이 편리하다.

실제로 메서드 참조를 이용하면 기존 메서드 구현으로 람다 표현식을 만들 수 있다.

이때 명시적으로 메서드명을 참조함으로써 **가독성을 높일 수 있다.**

메서드 참조는 메서드명 앞에 구분자 (::)를 붙이는 방식으로 메서드 참조를 활용할 수 있다.

메서드 참조를 새로운 기능이 아니라 하나의 메서드를 참조하는 람다를 편리하게 표현할 수 있는 문법으로 간주할 수 있다.

### 메서드 참조를 만드는 방법

1. 정적 메서드 참조
    
    예를 들어 Integer의 perseInt 메서드는 Integer::parseInt로 표현할 수 있다.
    
2. 다양한 형식의 인스턴스 메서드 참조
    
    예를 들어 String의 length 메서드는 String::length로 표현할 수 있다.
    
3. 기존 객체의 신스턴스 메서드 참조
    
    예를 들어 Transaction 객체를 할당받은 expensiveTransaction 지역 변수가 있고, Transaction 객체에는 getValue 메서드가 있다면, 이를 expensiveTransaction::getValue라고 표현할 수 있다.
    

String::length 같은 두 번째 유형의 메서드 참조를 이용해서 람다 표현식의 파라미터로 전달할 수 있다.

예를 들어 (String s) → s.toUpperCase()라는 람다 표현식을 String::toUpperCase로 줄여서 표현할 수 있다.

반면 세 번째 유형의 메서드 참조는 람다 표현식에서 현존하는 외부 객체의 메서드를 호출할 때 사용된다.

예를 들어 () → expensiveTransaction.getValue() 라는 람다 표현식을 expensiveTransaction::getValue로 줄여서 표현할 수 있다.

세 번째 유형의 메서드 참조는 비공개 헬퍼 메서드를 정의한 상황에서 유용하게 활용할 수 있다.

isValidName이라는 헬퍼 메서드를 정의했다고 가정하자.

```java
private boolean isValidName(String string) {
	return Character.isUpperCase(string.charAt(0));
}
```

이제 Predicate<String>를 필요로 하는 적당한 상황에서 메서드 참조를 사용할 수 있다.

`filter(words, this::isValidName)`

### 생성자 참조 | 3.6.2

ClassName::new처럼 클래스명과 new 키워드를 이용해서 기존 생성자의 참조를 만들 수 있다.

이것은 정적 메서드의 참조를 만드는 방법과 비슷하다.

예를 들어 인수가 없는 생성자, 즉 Supplier의 () → Apple과 같은 시그니처를 갖는 생성자가 있다고 가정하자.

```java
Supplier<Apple> c1 = Apple::new;
Apple a1 = c1.get(); // Supplier의 get메서드를 호출해서 새로운 Apple 객체를 만들 수 있다.

// 위 코드와 아래 코드는 같다.

Supplier<Apple> c1 = () -> new Apple(); // 람다 표현식은 디폴트 생성자를 가진 Apple을 만든다.
Apple a1 = c1.get(); // Supplier의 get 메서드를 호출해서 새로운 Apple 객체를 만들 수 있다.
```

Apple(Integer weight)라는 시그니처를 갖는 생성자는 Function 인터페이스의 시그니처와 같다.

```java
Function<Integer, Apple> c2 = Apple::new; // Apple(Integer weight)의 생성자 참조
Apple a2 = c2.apply(110); // Function의 apply 메서드에 무게를 인수로 
													// 호출해서 새로운 Apple 객체를 만들 수 있다.

// 위 코드와 아래 코드는 같다.

Function<Integer, Apple> c2 = (weight) -> new Apple(weight);
Apple a2 = c2.apply(110);
```

다음 코드에서 Integer를 포함하는 리스트의 각 요소를 우리가 정의했던 map 같은 메서드를 이용해서 Apple 생성자로 전달한다.

```java
List<Integer> weights = Arrays.asList(7, 3, 4, 10);
List<Apple> apples = map(weights, Apple::new);

public List<Apple> map(List<Integer> list, Function<Integer, Apple> f) {
	List<Apple> result = new ArrayList<>();
	for(Integer i : list) {
		result.add(f.apply(i));
	}
	return result;
}
```

`Apple(String color, Integer weight)` 처럼 두 인수를 갖는 생성자는 BiFunction 인터페이스와 같은 시그니처를 가지므로 다음처럼 할 수 있다.

```java
BiFunction<Color, Integer, Apple> c3 = Apple :: new; //Apple(String color,
																										 // weight)의 생성자 참조
Apple a3 = c3.apply(GREEN, 110); // BiFunction의 apply 메서드에 색과 무게를
																 // 인수로 제공해서 새로운 Apple 객체를 만들 수 있다.
```

인스턴스화를 하지 않고도 생성자에 접근할 수 있는 기능을 다양한 상황에 응용할 수 있다.

예를 들어 Map으로 생성자와 문자열값을 관련시킬 수 있다. 그리고 String과 Integer가 주어졌을 때 다양한 무게를 갖는 여러 종류의 과일을 만드는 giveMeFruit라는 메서드를 만들 수 있다.

```java
static Map<String, Function<Integer, Fruit>> map = new HashMap<>();
static {
	map.put("apple", Apple::new);
	map.put("orange", Orange::new);
	// 등등
}
```

## 람다, 메서드 참조 활용하기 | 3.7

---

사과 리스트 정렬 문제를 해결하면서 지금까지 동작 파라미터화, 익명 클래스, 람다 표현식, 메서드 참조 등을 총 동원해서 해결해본다.

### 1단계 : 코드 전달 | 3.7.1

다행히 자바 8의 List API에서 sort 메서드를 제공하므로 정렬 메서드를 직접 구현할 필요는 없다.

sort 메서드는 다음과 같은 시그니처를 갖는다.

```java
void sort(Comparator<? super E> c)
```

이 코드는 Comparator 객체를 인수로 받아 두 사과를 비교한다.

객체 안에 동작을 포함시키는 방식으로 다양한 전략을 전달할 수 있다.

1단계의 코드를 다음과 같이 완성할 수 있다.

```java
public class AppleComparator implements Comparator<Apple> {
	public int compare(Apple a1, Apple a2) {
		return a1.getWeight().compareTo(a2,getWeight());
	}
}
inventory.sort(new AppleComparator());
```

### 2단계 : 익명 클래스 사용 | 3.7.2

한 번만 사용할 Comparator를 위 코드처럼 구현하는 것보다는 익명 클래스를 이용하는 것이 좋다.

```java
inventory.sort(new Comparator<Apple>() {
	public int compare(Apple a1, Apple a2) {
		return a1.getWeight().compareTo(a2.getWeight());
	}
});
```

### 3단계 : 람다 표현식 사용 | 3.7.3

자바 8에는 람다 표현식이라는 경량화된 문법을 이용해서 코드를 전달할 수 있다.

함수형 인터페이스를 기대하는 곳 어디에서나 람다 표현식을 사용할 수 있음을 배웠다.

함수형 인터페이스란 오직 하나의 추상 메서드를 정의하는 인터페이스다.

추상 메서드의 시그니처(함수 디스크립터라 불림)는 람다 표현식의 시그니처를 정의한다.

Comparatort의 함수 디스크립터는 `(T, T) → int` 이다.

우리는 사과를 사용할 것이므로 더 정확히는 `(Apple, Apple) → int` 로 표현할 수 있다.

```java
inventory.sort((Apple a1, Apple a2) ->
											a1.getWeigh().compareTo(a2.getWeight())
);
```

자바 컴파일러는 람다 표현식이 사용된 콘텍스트를 활용해서 람다의 파라미터 형식을 추론한다고 설명했다.

```java
inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

이 코드의 가독성을 더 향상시킬 수는 없을까?

Comaprator는 Comparable 키를 추출해서 Comparator 객체로 만드는 Function 함수를 인수로 받는 정적 메서드 comparing을 포함한다.

```java
Comparator<Apple> c = Comparator.comparing((Apple a) -> a.getWeight());
```

이 코드를 다음처럼 간소화할 수 있다.

```java
import static java.util.Comparator.comparing;
inventory.sort(comparing(apple -> apple.getWeight()));
```

### 4단계 : 메서드 참조 사용 | 3.7.4

메서드 참조를 이용해서 코드를 조금 더 간소화할 수 있다.

```java
inventory.sort(comparing(Apple::newgetWeight));
```

자바 8 이전의 코드에 비해 코드만 짧아진 것이 아니라 코드의 의미도 명확해졌다.

즉, 코드 자체로 ‘Apple을 weight별로 비교해서 inventory를 sort하라’는 의미를 전달할 수 있다.

## 람다 표현식을 조합할 수 있는 유용한 메서드 | 3.8

---

자바 8 API의 몇몇 함수형 인터페이스는 다양한 유틸리티 메서드를 포함한다.

예를 들어 Comparator, Function, Predicate 같은 함수형 인터페이스는 람다 표현식을 조합할 수 있도록 유틸리티 메서드를 제공한다.

### Comparator 조합 | 3.8.1

정적 메서드 Comparator.comparing을 이용해서 비교에 사용할 키를 추출하는 Function 기반의 Comparator를 반환할 수 있다.

```java
Comparator<Apple> c = Comparator.comparing(Apple::getWeight);
```

### 역정렬

사과의 무게를 내림차순으로 정렬하고 싶다면 굳이 다른 Comparator 인스턴스를 만들 필요가 없다.

인터페이스 자체에서 주어진 비교자의 순서를 뒤바꾸는 reverse라는 디폴트 메서드를 제공하기 때문에 처음 비교자 구현을 그대로 재사용해서 사과의 무게를 기준으로 역정렬 할 수 있다.

```java
inventory.sort(comparing(Apple::getWeight).reversed()); // 무게를 내림차순으로 정렬
```

### Comperator 연결

무게가 같은 두 사과가 존재한다면 어떻게 해야할까? 정렬된 리스트에서 어떤 사과를 먼저 나열해야 할까?

이럴 땐 비교 결과를 더 다듬을 수 있는 두 번째 Comparator를 만들 수 있다.

예를 들어 무게로 두 사과를 비교한 다음에 무게가 같다면 원산지 국가별로 사과를 정렬할 수 있다.

thenComparing 메서드로 두 번째 비교자를 만들 수 있다.

thenComparing은 함수를 인수로 받아 첫 번째 비교자를 이용해서 두 객체가 같다고 판단되면 두 번째 비교자에 객체를 전달한다.

```java
inventory.sort(comparing(Apple::getWeight)
							.reversed() // 무게를 내림차순으로 정렬
							.thenComparing(Apple::getCountry)); // 두 사과의 무게가 같으면 국가별로 정렬
```

## Predicate 조합 | 3.8.2

Predicate 인터페이스는 복잡한 프레디케이트를 만들 수 있도록 negate, and, or 세 가지 메서드를 제공한다.

예를 들어 ‘빨간색이 아닌 사과’처럼 특정 프레디케이트를 반전시킬 때 nagate 메서드를 사용할 수 있다.

```java
Predicate<Apple> notRedApple = redApple.negate(); // 기존 프레디케이트 객체
																									// redApple의 결과를
																									// 반전시킨 객체를 만든다.
```

또한 and 메서드를 이용해서 빨간색이면서 무거운 사과를 선택하도록 두 람다를 조합할 수 있다.

```java
Predicate<Apple> redAndHeavyApple =
		redApple.and(apple -> apple.getWeight() > 150); // 두 프레디케이트를 연결해서 새로운
																										// 프레디케이트 객체를 만든다.
```

그뿐만 아니라 or을 이용해서 ‘빨간색이면서 무거운(150그램 이상) 사과 또는 그냥 녹색 사과’ 등 다양한 조건을 만들 수 있다.

```java
Predicate<Apple> redAndHeavyAppleOrGreen =
	redApple.and(apple -> apple.getWeight() > 150)
			.or(apple -> GREEN.equals(a.getColor())); // 프레디케이트 메서드를 연결해서
																								// 더 복잡한 프레디케이트 객체를 만든다.
```

### Function 조합 | 3.8.3

마지막으로 Function 인터페이스에서 제공하는 람다 표현식도 조합할 수 있다.

Function 인터페이스는 인스턴스를 반환하는 andThen, compose 두 가지 디폴트 메서드를 제공한다.

andThen 메서드는 주어진 함수를 먼저 적용한 결과를 다른 함수의 입력으로 전달하는 함수를 반환한다.

예를 들어 숫자를 증가 (x → x + 1) 시키는 f 라는 함수가 있고, 숫자에 2를 곱하는 g 라는 함수가 있다고 가정하자.

이제 다음처럼 f와 g를 조립해서 숫자를 증가시킨 뒤 결과에 2를 곱하는 h 라는 함수를 만들 수 있다.

```java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.andThen(g); // 수학으로는 write g(f(x))라 표현
int result = h.apply(1); // 4를 반환
```

compose 메서드는 인수로 주어진 함수를 먼저 실행한 다음에 그 결과를 외부 함수의 인수로 제공한다.

즉, `f.andThen(g)`에서 andThen 대신에 compose를 사용하면 $g(f(x))$가 아니라 $f(g(x))$라는 수식이 된다.

```java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.compose(g); // 수학으로는 write f(g(x))라 표현
int result = h.apply(1); // 3를 반환
```

## 마치며 | 3.9

- 람다 표현식은 익명 함수의 일종이다. 이름은 없지만, 파라미터 리스트, 바디, 반환 형식을 가지며 예외를 던질 수 있다.
- 람다 표현식으로 간결한 코드를 구현할 수 있다.
- 함수형 인터페이스는 하나의 추상 메서드만을 정의하는 인터페이스다.
- 함수형 인터페이스를 기대하는 곳에서만 람다 표현식을 사용할 수 있다.
- 람다 표현식을 이용해서 함수형 인터페이스의 추상 메서드를 즉석으로 제공할 수 있으며 람다 표현식 전체가 함수형 인터페이스의 인스턴스로 취급된다.
- java.util.function 패키지는 Predicate<T>, Function<T, R>, Supplier<T>, Consumer<T>, BinaryOperator<T> 등을 포함해서 자주 사용하는 다양한 함수형 인터페이스를 제공한다.
- 자바 8은 Predicate<T>와 Function<T, R> 같은 제네릭 함수형 인터페이스와 관련한 박싱 동작을 피할 수 있는 IntPredicate, IntToLongFunction 등과 같은 기본형 특화 인터페이스도 제공한다.
- 실행 어라운드 패넡(예를 들면 자원 할당, 자원 정리 등 코드 중간에 실행해야 하는 메서드에 꼭 필요한 코드)을 람다와 활용하면 유연성과 재사용성을 추가로 얻을 수 있다.
- 람다 표현식의 기대 형식을 대상 형식 이라고 한다.
- Comparator, Predicate, Function 같은 함수형 인터페이스는 람다 표현식을 조합할 수 있는 다양한 디폴트 메서드를 제공한다.