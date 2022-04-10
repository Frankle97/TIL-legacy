# String, StringBuffer, StringBuilder

문자열 연산이 적을 경우 문제가 발생할 확률은 적으나, **연산이 많다, 멀티쓰레드이다, Race Condition** 등의 상황을 고려해야 한다면 각 클래스에 대해 이해하고 상황에 맞는 클래스를 적절하게 사용할 줄 알아야 한다.

## String vs StringBuffer/StringBuilder

`String`과 `StringBuffer`/`StringBuilder`의 가장 큰 차이점은 String은 **불변**, 그 외는 **가변**이다.

```
String tom = "Hello";
tom += Tom";
```
위 예제를 보면 a에 있는 `"Hello"` 란 문자열 값에 `Tom`을 더하고 있다.

하지만 우린 보이는 것을 넘어 내부적인 구조를 이해해야 한다.

### String과 JVM
Java에서 String을 선언하면 JVM의 Heap 영역 내부의 `String Constant Pool`에 할당된다.

다만, `new String("abc")`으로 문자열을 생성하는 경우 `Heap` 영역에 바로 할당된다.

```
String a = "abc";
String b = "abc";
```
만약 동일한 문자열이 있다면 `String Constant Pool`에 동일한 값을 가진 주소를 참조한다.
a와 b는 같은 문자열 값인 `abc`를 갖고 있고 이는 곧 `String Constant Pool`에 동일한 주소를 참조하게 되면서 불필요한 메모리 생성을 방지한다.

`b` 값을 다른 문자열로 변경한다면 `String Constant Pool`에 새로운 메모리 영역이 할당된다.
그리고 `a`변수 또한 `b`와 겹치지 않는 문자열로 변경한다면 `String Constant Pool`의 `abc`는 GC 대상이 되어 메모리 해제된다.

### String이 가변이라면
만약 String이 가변일 경우 `b`에 할당된 문자를 "ABC"로 변경할 시 `c`에 할당된 값도 변경될 수 있기 때문에 String은 불변의 성질을 유지하고 있다.

### String은 불변
이처럼 String은 불변성을 갖고 있으므로 변하지 않은 문자열을 자주 읽어들이는 경우 String을 사용하면 좋은 성능을 기대할 수 있다.
하지만 문자열 연산이 빈번하게 발생하는 경우에 String을 사용하면 계속하여 메모리를 할당하고 해제하는 작업을 반복하면서 부하를 주게 된다.

이를 해결하기 위해 가변의  `StringBuffer` / `StringBuilder` 클래스가 탄생했다.

## StringBuffer VS StringBuilder
`StringBuffer`와 `StringBuilder` 서로 **가변**이라는 공통된 성질을 갖고 있다는 것은 알겠는데 둘의 차이는 무엇일까?

**StringBuffer**는 내부적으로 `synchronized` 키워드를 사용하여 동기화를 지원하므로  **Thread-Safe**하다.(=멀티쓰레드 환경에 안전하다.)
참고로 String 또한 불변성을 갖고 있기에 

반대로 **StringBuilder**는 동기화를 지원하지 않으므로 멀티스레드 환경에서 적합하지 않지만 성능 부분에선 동기화를 고려하지 않는 만큼 **StringBuffer**보다 뛰어나다.

## 정리
**String**, **StringBuffer**, **StringBuilder** 각 클래스가 갖고 있는 성질에 대해 올바르게 이해하고 있다면 보다 좋은 어플리케이션을 만들어나갈 수 있을 것이다.

- `String` : 문자열 연산이 적을 경우, 멀티쓰레드 환경에 안전, `String Constant Pool`에 할당
- `StringBuffer` : 문자열 연산이 많을 경우, 멀티쓰레드 환경에 안전, `Heap`에 할당
- `StringBuilder` : 문자열 연산이 많을 경우, 멀티쓰레드 환경에 안전하지 않음, `Heap`에 할당


## String -> StringBuilder
추가적으로`jdk 1.5` 이후부터 `String`의 + 연산은 컴파일 시에 성능 최적화를 위해 `StringBuilder` 사용하도록 자동 변환되어진다.
하지만 모든 경우에서 `String`이 `StringBuilder`로 변환되는 것은 아니다.

### 한 줄에 연산 할 경우
```java
String str = "Tom" + " is" + " idiot";

↓
        
String str = "Tom is idiot";
```
한 라인에서 연산을 이룬 경우에는 `StringBuilder`을 사용하여 변환하지 않습니다.


단지 하나의 `String`으로 변환됩니다.

### 여러 줄로 연산 할 경우
```
String str = "Tom";
str += " is";
str += " idiot";

↓

String str = "Tom";
str = (new StringBuilder().append(str).append(" is").toString());
str = (new StringBuilder().append(str).append(" idiot").toString());

```
`String` 연산을 여러 라인에 걸쳐서 선언할 경우에 자바 컴파일러가 연산하는 라인마다 새로운 `StringBuilder`를 생성하여 append하는 것을 확인할 수 있다.
`String`과 `StringBuilder` 서로 연산 시마다 새로운 객체를 생성하는 것은 같지만 새로운 `String` 객체를 새로 생성하는 것보다 `StringBuilder`를 이용하여 연산하는 것이 성능 부분에서 효율적이다.

---
https://ifuwanna.tistory.com/221
