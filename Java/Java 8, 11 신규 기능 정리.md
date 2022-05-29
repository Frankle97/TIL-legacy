# Java 8, Java 11 특징 정리

## Java란?

Java는 1991년 선 마이크로시스템즈의 제임스 고슬링에 의해 탄생한 객체 지향 프로그래밍 언어이다. Java 애플리케이션을 컴파일하면 JVM을 사용하는 모든 플랫폼에서 실행 가능한 바이트코드가 생성된다. 즉, JVM이 실행 가능한 모든 OS에서 애플리케이션을 사용할 수 있기에 Java를 WORA(Write Once, Run Anywhere) 언어라고도 부른다.

## Java의 장점

### 높은 인기와 급여

Java는 한국을 포함하여 세계에서도 가장 인기 있는 언어 중 하나이며, 시장에서의 수요가 높아 많은 개발자들이 높은 입금을 받으며 일하고 있다.

### 대규모 커뮤니티

많은 시간이 흐르면서 축적된 방대한 레퍼런스와 현재도 왕성하게 활동 중인 대규모 커뮤니티들이 존재한다. 국내에는 [Okky,](https://okky.kr/) 글로벌 커뮤니티로는 [StackOverFlow](https://stackoverflow.com/) 가 있다.

### 풍부한 API

Java에는 풍부한 API들이 만들어져 있으며 내부 구현을 알 필요 없이 애플리케이션을 개발하는 데 유용하고, Maven, Google Guava, Apache Commons 등 여러 오픈 소스 라이브러리가 존재한다.

### 뛰어난 문서 지원

Java용 문서 생성기인 Javadoc을 이용해 Java 소스 코드로 HTML 형식의 API 문서를 생성할 수 있다.

## Java 8

### Bye PermGem, Hi Metaspace

### Permanent Generation

PermGem은 Permanent Generation의 약자로 클래스의 메타 데이터(클래스 명, 필드 정보, 메서드 정보)들을 별도의 Heap 공간에 저장하는 영역이다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ce0676d3-b0ba-41ea-8c90-53c8726b387c/Untitled.png)

Java는 이 곳에서 클래스의 메타 데이터를 읽어 객체를 생성할 수 있었다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/963f5375-4f0b-4bfc-9ead-c623d55d0b90/Untitled.png)

PermGem은 OS, JVM에 따라 각기 다른 default 크기를 가지고 있었으며, 이는 매우 적은 크기였다.

클래스 로딩이 잦다보면 PermGem이 부족하여 **`java.lang.OutOfMemoryError: PermGen space`** 에러가 발생하곤 했었다. 이를 해결하기 위해 `-XX:PermSize`  `-XX:MaxPermSize` 를 설정하여 PermSize의 크기를 직접 설정했었다.

### Metaspace

자바 8 이후로는 JVM에서 PermGen 영역이 완전히 사라지고 Metaspace란 영역으로 대체되었다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/aabfdf1c-1147-4523-99b3-178c343d7373/Untitled.png)

Metaspace는 클래스 로더가 현재까지 로드한 클래스들의 메타 데이터들을 저장하는 공간이다.

Metaspace는 클래스의 메타 데이터들을 PermGen과 달리 Heap 영역이 아닌 Native 메모리에 저장하고, 크기가 제한되어 있지 않기 때문에 필요한만큼 크기가 자동으로 늘어난다.

Native 메모리 영역은 JVM에 관리되는 Heap 영역이 아니라 OS 레벨에서 관리되는 영역이다.

### 인터페이스의 기본 메서드

`default` 키워드를 사용하여 인터페이스에 비추상 메서드를 구현이 가능하다.

본래 인터페이스의 구현체 입장에서는 모든 인터페이스 메서드에 대해서 모두 오버라이딩을 해야 했지만, default 키워드가 붙은 메서드는 선택적으로 오버라이딩이 가능하다.

```java
public interface Math {
		int sum(int a, int b);

		default double sqrt(int a) {
				return Math.sqrt(int a);
		}
}
```

### 함수형 인터페이스

인터페이스에 추상 메서드가 단 하나만 포함된 임의의 인터페이스를 람다식으로 사용이 가능하다.

인터페이스가 요구 사항을 충족하는지 확인하기 위해 `@FunctionalInterface` 주석을 추가했다면, 컴파일러가 이 주석을 인식하고 인터페이스에 오직 하나만 추상 메서드를 갖도록 검사한다.

```java
@FunctionalInterface
interface Converter<F, T> {
    T convert(F from);
}
```

### 람다 표현식

익명 이너 클래스의 표현을 함수식으로 간단하게 표현할 수 있다.

**Java 7**

```java
new Thread(new Runnable() {
    @Override
		public void run() {
			System.out.println("Hello world");
		}
})
```

**Java 8**

```java
new Thread() -> {
		System.out.println("Hello World");
}
```

또한 람다 표현식에서는 **타입 추론**이 가능하며, 전달 파라미터의 타입을 명시하지 않아도 런타임에 추론이 가능해진다. 타입을 명시적으로 선언하지 않아도 되므로, 코드량이 감소한다.

**Java 7**

```java
List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");

Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return b.compareTo(a);
    }
});
```

**Java 8**

```java
Collections.sort(names, (String a, String b) -> {
    return b.compareTo(a);
});
```

### 메서드 및 생성자 참조

`::` 키워드를 통해 메소드 또는 생성자의 참조를 전달할 수 있다.

```java
Converter<String, Integer> converter = Integer::valueOf;
Integer converted = converter.convert("123");
```

위는 Integer 객체의 정적 메소드인 valueOf를 참조하는 예시이다.

```java
Something something = new Something();
Converter<String, String> converter = something::startsWith;
```

Something 객체의 인스턴스 메소드를 참조하는 예시이다.

```java
Stream<User> stream = list.stream().map(User::new);
```

클래스명::new를 통해 생성자 참조를 사용할 수 있다.

### 스트림 API

스트림 API는 람다 표현식의 효과적인 활용법으로 Collection 인터페이스를 다루는 다양한 방법들을 지원한다. 파이프라인/지연/병렬 등이 인터페이스로 제공되며, 함수형 프로그래밍이 가능해진다.

```java
List<Guest> guests = repository.findAll();
return guests.stream()
	.filter(g -> company.equals(g.getCompany()))
	.sorted(Comparator.comparing(Guest::getGrade))
	.map(Guest::getName)
	.collect(Collectors.toList());
```

### 날짜 API

Java7까지의 JDK 날짜 관련 라이브러리들은 가변적이고 Thread-Safe 하지 못하다는 문제들로 말이 많았었다. Java 8부터는 LocalDate, LocalTime, LocalDateTime 등의 라이브러리를 통해 기존보다 손쉽게 날짜 로직을 작성이 가능하다.

```java
LocalDate now = LocalDate.now();
LocalDate today = IsoChronology.INSTANCE.date(2022, 11, 3);
LocalDate tomorrow = today.plusDays(1);
```

### Optional

Optional을 통해 null 또는 non-null일 수 있는 값을 손 쉽게 처리할 수 있어 이전과 달리 값에 대한 null 체크 코드를 줄일 수 있다.

```java
Optional<String> optional = Optional.of("bam");

optional.isPresent();           // true
optional.get();                 // "bam"
optional.orElse("fallback");    // "bam"

optional.ifPresent((s) -> System.out.println(s.charAt(0)));     // "b"
```

### GC

Java 8 GC는 Parallel GC 방식을 사용한다.

## Java 11

### String & Files 신규 메소드 추가

String에 아래와 같은 메소드들이 추가되었다.

- `strip()` - 문자열 앞, 뒤의 공백을 제거
- `stripLeading()` - 문자열 앞의 공백을 제거
- `stripTrailing()` - 문자열 뒤의 공백을 제거
- `isBlank()` - 문자열이 비었거나 공백일 경우 true를 반환
- `repeat(n)` - n 횟수만큼 문자열을 반복 append 하여 반환

Files에 아래와 같은 메소드들이 추가되었다.

- `Path writeString(Path, String, Charset, OpenOption)`: 파일에 문자열을 작성하고 경로를 반환한다. 파일 오픈 옵션에 따라 작동 방식을 달리하며, charset을 지정하지 않으면 UTF-8이 사용된다.
- `String readString(Path, Charset)`: 파일 전체 내용을 읽어 String으로 반환하고, 파일 내용을 모두 읽거나 예외가 발생하면 알아서 close를 한다. charset을 지정하지 않으면 UTF-8이 사용된다.
- `boolean isSameFile(Path, Path)`: 두 Path가 같은 파일을 가리키며, true, 아니면 false를 반환한다.

### Collection Interface 새로운 메소드 추가

`toArray()` 메소드를 오버로딩하는 메소드가 추가되었고, 원하는 타입의 배열로 반환을 얻을 수 있다.

```java
List list = Arrays.asList("Sam", "David"); 
String[] sampleArray = sampleList.toArray(String[]::new); 
```

### 람다에서 인수로 var 사용

람다에서 인수 선언 시 var 키워드 사용이 가능하다.

```java
List<String> sampleList = Arrays.asList("Sam", "David"); 
String resultString = sampleList.stream() 
.map((var x) -> x.toUpperCase()) 
.collect(Collectors.joining(", ")); 
```

### 자바 소스 파일 실행

Java 소스 파일을 컴파일 하지 않고 스크립트로 실행이 가능하다.

```java
// Java 8
$ javac HelloWorld.java
$ java Helloworld
Hello Java 8!

// Java 11
$ java HelloWorld.java
Hello Java 11!
```

### GC

Java 8 GC는 G1 GC 방식을 사용한다.

## Reference

[https://www.geeksforgeeks.org/top-10-reasons-to-learn-java/](https://www.geeksforgeeks.org/top-10-reasons-to-learn-java/)

[https://dzone.com/articles/permgen-and-metaspace#:~:text=PermGen is an abbreviation for,Metaspace - with some subtle differences](https://dzone.com/articles/permgen-and-metaspace#:~:text=PermGen%20is%20an%20abbreviation%20for,Metaspace%20%2D%20with%20some%20subtle%20differences).

[https://blog.voidmainvoid.net/315](https://blog.voidmainvoid.net/315)

[https://github.com/winterbe/java8-tutorial](https://github.com/winterbe/java8-tutorial)

[https://steady-coding.tistory.com/598](https://steady-coding.tistory.com/598)