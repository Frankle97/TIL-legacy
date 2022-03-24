# Generics
- **Generics를 사용하는 이유**
  - 컴파일 시점에 컴파일러가 보다 정확하게 타입을 체크할 수 있음.
    - 런타임 에러가 발생할 확률이 높아짐.
  - 값을 사용하기 위해 불필요한 캐스팅 과정이 필요로 하게 될 수 있음.
  - 다형성..
    - 타입별로 메서드를 정의해야 할 경우 제네릭을 잘 활용하면 하나로 통일할 수 있겠다.
```java
static <T extends Comparable<T>> long countGreatherThan(T[] arr, T element) {
    return Arrays.stream(arr).filter(s -> s.compareTo(element) > 0).count();    
}

해당 메서드는 arr 배열에서 element 인자보다 큰 문자 또는 숫자의 수를 구하는 메서드이다.  
이렇게 제네릭을 잘 활용하면, 문자 배열과 숫자 배열을 받는 메서드를 각각 정의할 필요 없이 하나로 통일이 가능하다.
``` 
- `?`는 와일드카드라고 불리우며, 타입을 모르면서 알 필요도 없고, 어떤 것이든 받는다.
- `T`는 타입이 정해지면 타입이 무엇인지 알고, 그것을 사용하겠다는 의미이다.
