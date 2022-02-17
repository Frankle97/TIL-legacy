# Immutable Object (불변 객체)

먼저 아래 예제 코드를 통하여 `불변 객체`와 `가변 객체`란 무엇인지 확인해보자.

```java
// 객체 상태가 public 이므로 외부에서 값 수정 가능
class Student {
    public int id;
    public String name;
    
    public Student(int id, String name) {
        this.id = id;
        this.name = name;
    }
}

// Setter 로 객체 데이터 값 수정 가능
class Student {
    private int id;
    private String name;

    public void setId(int id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```
위 클래스는 외부에서 객체의 상태 변경이 가능한 **가변 객체**이다.
- 접근 제어자가 public 이므로 어디에서나 객체의 인스턴스 변수 값에 접근할 수 있고 수정 또한 가능
- Setter 가 있다면 해당 데이터 값 또한 수정이 가능

```java
class Student {
    private final int id;
    private final String name;

    public Student(int id, String name) {
        this.id = id;
        this.name = name;
    }
}
```
위 클래스는 객체의 상태 변경이 불가한 **불변 객체**이다.<br/>
**불변객체**는 객체 생성 이후에 객체의 상태 변경이 불가하다.

모든 인스턴스 변수에 `final`를 사용함으로 수정이 불가하고, `Setter` 메서드 또한 작성할 수 없다.

## 불변 객체의 장단점
### 장점
- 객체의 신뢰도가 높아진다.
    - 생성된 객체의 상태가 변할 일이 없다면 트랜잭션 내에서 해당 객체가 변하지 않는다는 확신을 얻으므로 신뢰를 얻을 수 있다.
- 생성자, 접근 메서드에 대한 방어적 복사를 하지 않아도 된다.
- 멀티 스레드 환경에서 동기화 처리 없이 객체 공유가 가능하다. 

### 단점
- 객체마다 값을 다르게 가져야 한다면 새로운 객체를 생성해야한다.
    - 이는 곧 새로운 객체를 계속 생성해야하기 때문에 메모리 누수와 성능 저하로 이어질 수 있다.
    
불변 객체는 복제나 비교를 위한 조작을 단순화 시켜주고 성능 개선에 도움을 주지만, 객체의 상태가 많은 경우에는 오히려 불변이 독이 될 수 있다.

## 참조 타입의 불변 객체
**불변 객체**를 만드는 기본적인 방법은 필드에 `final` 를 사용하고 `Setter`를 구현하지 않는 것이다.<br/>
다만, 위 방법은 필드가 기본형 타입일 경우에만 가능하고, 참조 타입일 경우에는 추가적인 작업이 필요하다.

```java
public class Animal {

    private final Age age;

    public Animal(final Age age) {
        this.age = age;
    }

    public Age getAge() {
        return age;
    }
}

class Age {

    private int value;

    public Age(final int value) {
        this.value = value;
    }

    public void setValue(final int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}
```
앞서 설명했듯이 Animal 클래스는 불변 객체가 될 수 없다.

```
    Age age = new Age(1);
    Animal animal = new Animal(age);

    System.out.println(animal.getAge().getValue());
    // Output: 1

    animal.getAge().setValue(10);
    System.out.println(animal.getAge().getValue());
    // Output: 10
```

이처럼 Animal 클래스의 참조 변수인 Age 또한 불변 객체가 아니라면 접근 방법이 열려있기 때문이다.<br/>
즉, 불변 객체의 참조 클래스 또한 또한 불변해야 한다는 것을 확인할 수 있다.

불변 객체를 만드려는 클래스 필드에 참조 타입이 있는 경우는 여러가지지만, 대표적으로 (1)객체, (2)Array, (3)List를 참조하는 상황에 대해 불변 객체를 만들어보자.

## (1) 일반 객체의 경우

```java
public class Animal {

    private final Age age;

    public Animal(final Age age) {
        this.age = age;
    }

    public Age getAge() {
        return age;
    }
}

class Age {

    private final int value;

    public Age(final int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}
```
참조 변수인 Age 또한 불변 객체로 만들면 Animal 는 불변 객체가 된다.

## (2) Array
```java
public class ArrayObject {

    private final int[] array;

    public ArrayObject(final int[] array) {
        this.array = Arrays.copyOf(array,array.length);
    }


    public int[] getArray() {
        return (array == null) ? null : array.clone();
    }
}
```
```java
int[] arr1 = {1,2,3};
        ArrayObject arr=new ArrayObject(arr1);
        arr.getArray()[1]=2;
    
        for (Integer a:arr.getArray()) {
        System.out.println(a);
        }
// Output : 123 변경되지 않은 초기 Array 값을 갖고 있다.
```
생성자 인자로 받는 Array를 copy하고 getter에서 clone을 반환하도록 한다.<br/>
Array를 그대로 참조하거나 곧장 반환할 경우 외부에서 Array 값을 변경 가능이 가능하지만, clone을 반환하게 되면 변경이 불가하다. 

만약 Array 타입이 기본형이 아닌 참조 타입일 경우 해당 타입의 클래스는 불변 객체어야한다.

## (3) List의 경우
Array와 마찬가지로 생성자 인자를 그대로 할당하지 않고 새로운 List로 할당해야 한다.<br/>
> 이처럼 객체 내부에서 `새로운 객체`를 만들어 반환하는 과정을 **방어적 복사**라고 한다.

또한, Getter을 통해 제공되는 값이 수정되는 것을 방지하기 위해 Collection의 unmodifiableList 메서드를 사용한다.
```java
public class AnimalList{
    private final List<Animal> animals;
    
    public ListObj(final List<Animal> animals){
        this.animals = new ArrayList<>(animals);
    }
    
    public List<Animal> getAnimals(){
        return Collections.unmodifiableLIst(animals);
    }
}
```
## String으로 보는 불변 객체

String 은 Java의 대표적인 불변 객체이다.

String str = "a";
str = str + "b";
// str - "ab"
위의 String 연산은 기존의 str에 "b"가 붙어서 "ab"가 된 것처럼 보이지만 사실은 JVM에 새로운 메모리 영역을 할당한 것이다. <br/>
이는 곧 String을 통한 문자열 연산이 많을 수록 GC 대상이 늘어나게 되는 것이다.

String str = "a";
str = "b";
위의 상황에서 처음에는 한 주소에 "a" 라는 것을 생성하고 str 변수에 담는다.
이후 "b" 때에도 또한 다른 주소에 "b" 를 생성하고 참조를 해당 주소로 바꾼다는 것이다.

### String을 불변 객체로 한 이유

String이 불변 객체인 주 목적은 메모리 절약을 위한 캐싱 기능 때문이다.
Google 이라는 웹 서버가 Java 기반일 때에 "Google" 이라는 문자열은 정말 셀 수 없이 많이 호출될 것이다. 이것을 요청이 들어올 때마다 한 개씩 생성한다면 "Google" 이라는 값이 요청 처리 수 만큼 생성되어 있을 것이다. 그러나 불변 객체로 생성했기 때문에 단 하나의 문자열 객체만 만들어지게 될 것이고 참조 변수만 그 호출 수 만큼 생성되었다가 사라지게 될 것이다.

for(int i = 0; i < 500; i++){
    String str = "Google";
    System.out.println(str);
}
str 만 500번 생성 소멸될 것이다.

그리고 캐싱의 진정한 목적은 메모리 절약도 있겠지만 속도 향상에 있다. 자주 쓰이는 값은 최대한 CPU와 가까운 데이터일수록 처리가 빠르다. 보조기억장치보다는 메모리에 메모리보다는 캐시메모리나 레지스터에 있을수록 곧바로 연산을 하기가 쉬운것이죠.

Java에서 이 String 객체들은 Heap의 String Pool 이라는 특별한 공간이 있어 이곳에 저장되고, 그리고 앞선 코드의 for문에서처럼 참조하려는 문자열이 String Pool에 존재하는 경우 새로 생성하지 않고 Pool에 있는 객체를 사용하도록 한다. 따라서 객체를 새로 생성되는 오버헤드가 발생하지 않기때문에 특정 문자열값을 재사용하는 빈도가 높을수록 상당한 성능 향상을 기대할 수 있다는 것이다.

이외에 물론 불변하니깐 보안적으로도 좋고 스레드 안정성도 가지고 갈 수가 있다.

---
https://velog.io/@conatuseus/Java-Immutable-Object%EB%B6%88%EB%B3%80%EA%B0%9D%EC%B2%B4

https://galid1.tistory.com/622
