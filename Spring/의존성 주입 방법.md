# 스프링 의존성 주입 방법

## 필드 주입

```java
@Service
public class AServiceImpl implements AService {

	**@Autowired
	private BService bService;**

}
```

## 수정자 주입

```java
@Service
public class AServiceImpl implements AService {

	private BService bService;

	**@Autowired
	public setBService(BService bService) {
		this.bService=bService;
	}**
}
```

## 생성자 주입

```java
@Service
public class AServiceImpl implements AService {

	private final BService bService;
private final CService cService;

	**@Autowired
	public AServiceImpl(BService bService, CService cService) {
		this.bService=bService;
	}**
}
/**
- 단일 생성자의 경우 @Autowired 생략이 가능하다.
- 생성자 주입의 경우 final 사용이 가능하다.
**/
```


과거에는 **수정자 주입** 또는 **필드 주입** 방법을 주로 사용했었지만, 현재 스프링에서는 **생성자 주입** 방법을 권장하고 있다. 

*IntelliJ*에서 필드 주입을 사용하면 경고를 나타낸다


## 스프링에서 **생성자 주입 방법을 권장하는 이유**

### 1. 객체의 불변성

*대부분의* *어플리케이션은 런타임 시에 의존 관계에 대해 변경해야 할 일이 거의 없다.*

필드 주입과 수정자 주입은 필드를 final로 선언 할 수 없지만, 생성자 주입은 final 선언이 가능하다. ⇒ **불변성**

또한, 객체를 생성하기 위해 생성자 인자로 의존관계를 다 설정해주어야만 객체 생성이 가능하다. ⇒ **NPE 방지**

### 2. 순환 참조를 방지

순환 참조 오류를 사전에 파악할 수 있다.

### 3. 테스트 코드 작성에 용이

필드 주입의 경우에 테스트를 진행하려면 스프링 컨테이너를 올려야하거나 @Mock 등 도구를 사용하여 의존성을 주입해주어야하는 부분에서 상대적으로 불편함을 확인할 수 있다. 

반면에, 생성자 주입의 객체라면 스프링 컨테이너의 도움 없이 원하는 객체에 필요한 종속성을 전달하여 손쉽게 인스턴스를 생성 할 수 있다는 장점이 있다.

### 4. 단일 책임 원칙 위반

필드 주입은 새로운 의존성을 추가하는 방법이 굉장히 쉽다. 여러 개의 종속성을 추가한다고 해도 어떠한 제한도 없다.

한 클래스에 종속성이 너무 많아질 가능성이 있고, 이는 곧 단일책임원칙(SRP) 및 관심사 분리에 대한 위반이다.

반면에, 생성자 주입의 경우 생성자의 인자가 많아짐에 따른 리팩토링의 지표가 될 수가 있다. ⇒ *롬복을 사용하면 별 차이는 없는 것 같다.*


## 필드 주입의 장점?

다른 주입 방법에 비해 상대적으로 편하다는 점 이외엔 없다.  또한, 롬복을 사용하면 편리함에 있어 차이도 없다. 

특별한 이유가 없다면 생성자 주입을 사용하자.
