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

---
https://velog.io/@conatuseus/Java-Immutable-Object%EB%B6%88%EB%B3%80%EA%B0%9D%EC%B2%B4

https://galid1.tistory.com/622