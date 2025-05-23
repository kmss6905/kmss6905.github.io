---
layout: post
category: example
---

# 2024.01.07 - [java] Java 의 `final` 

> 예제에서 사용한 모든 코드는 [Github Repository](https://github.com/kmss6905/blog/tree/main/_20240107) 에 있습니다.

나는 변하지 않는 전역변수를 만들 때나 생성자의 파라미터를 받을 때 final 키워드를 사용했었다. 문득 final 에 대해 이것 말고 더 쓰이는 곳이 없을까? final에 대해 더 찾아보고 공부한 결과를 기록했다.

```java
class A {
	// 변하지 않는 전역변수
	private static final INIT_VALUE = 0.1;
}
```

email 과 password 를 생성자 파라미터로 받는 Member 클래스의 모습이다.
```java
// 생성자의 파라미터
public Member(final String email, final String password) {
        validateNonNull(email, password);
        this.email = email;
        this.password = password;
 }
 ```

우선 final 은 클래스, 메서드, 변수에 사용할 수 있다.

## 클래스(class)
final 이 있는 클래스는 상속하여 사용할 수 없다.
```java
public final class Cat {

    private int weight;

    // standard getter and setter
}

public class BlackCat extends Cat {

}
```
확장을 원하지 않는 경우에 해당 클래스에 final 키워드를 통해 다른 클래스가 상속받아 사용하는 것을 막을 수 있다.

<img src="https://velog.velcdn.com/images/kmss6905/post/f64a3029-421e-4add-b1b8-1427671ce17e/image.png" width="70%" heightv="70%"/>

단, 클래스에 final이 있다고 해서 final 클래스로 만든 객체가 불변하다는 것을 뜻하는 것은 아니다.
즉, 내부의 맴버변수는 얼마든지 바꿀 수 있다.
```java
class ClassFinalMainTest {

    @Test
    @DisplayName("final 클래스의 맴버변수는 바꿀 수 있다.")
     void mainTest() {
        Moeny moeny = new Moeny();
        moeny.setValue(100);

        assertEquals(100, moeny.getValue());
        assertDoesNotThrow(() -> moeny.setValue(200)); // 예외발생하지 않음.
        assertEquals(200, moeny.getValue());
    }

    final class Moeny {
        private int value;

        public void setValue(int value) {
            this.value = value;
        }

        public int getValue() {
            return value;
        }
    }
}
```
<h align="center">
  <img src="https://velog.velcdn.com/images/kmss6905/post/8e6d87d9-fa5f-4015-9a64-751f5f184dc7/image.png" width="40%">
<h/>

![](https://velog.velcdn.com/images/kmss6905/post/83e18ef2-ee70-4c65-af5a-07c36acbf096/image.png)

참고로, 인텔리제이에서는 final 클래스의 경우 "압정표시"로 상속할 수 없다는 것을 표시해준다.

## 메서드(method)
final 이 붙은 메서드는 오버라이딩 할 수 없다.
부모 클래스에 해당하는 Cat 클래스는 `public`, `private`, `final` 메서드로 각기 다른 "야옹~"을 출력하도록 했다.

자식 클래스에 해당하는 WhiteCat 가 오버라이딩 할 수 있는 메서드는 `public` 메서드 뿐이다.
이렇게 `public` 메서드의 경우 Cat 을 상속하여 오버라이딩을 통해 해당 `meow()` 메서드를 사용할 수 있다.

```java

// 부모 클래스
public class Cat {
    private int weight;

    public void meow() {
        System.out.println("누구나 야옹~");
    }

    final public void finalMeow() {
        System.out.println("나만 야옹~");    
    }

    private void privateMeow() {
        System.out.println("내부 야옹~");   
    }
}

// 자식 클래스
public class WhiteCat extends Cat{

    @Override
    public void meow() {
        System.out.println("흰 고양이 야옹");
    }
}
```

만약 자식클래스에서 부모의 final 메서드를 재정의 하려고 시도하면 어떻게 될까?
`finalMeow()' cannot override 'finalMeow()' in 'Cat'; overridden method is final` ![](https://velog.velcdn.com/images/kmss6905/post/b3686362-1048-4d20-a745-d13bd812a7a7/image.png)
오버라이드한 메서드가 final 이기 때문에 오버라이드를 할 수 없다고 에러 메시지를 띄우고 있다.

## 변수(varibles)

### 1. 원시변수(Primitive Varibles)
final 로 선언한 원시변수에 값을 할당한 후에는, 다른 값을 할당할 수 없다.
```java 
final int i = 1;
int i = 2;

```
i 변수에 1을 할당한 후, 2를 재할당하려고 하면 아래와 같은 에러를 뿜어낸다.
이미 위에서 정의되었기 때문에 다시 재할당할 수 없다.!

<h align="center">
    <img src="https://velog.velcdn.com/images/kmss6905/post/2317ff3d-d4a3-4ade-8361-fdd4b990c068/image.png" width="50%" heigh="50%"/>
</h>


---
### 2. 참조변수(Reference Varibles)
```java
final User user = new User("jimin");
user = new User("junguk");
```
<h align="center"><img src="https://velog.velcdn.com/images/kmss6905/post/19979a37-0fa3-470d-87b9-57d5d877ebfd/image.png" width="50%" heigh="50%"></h>

원시변수의 경우와 마찬가지로 final 로 선언한 참조변수의 경우 역시 다른 참조변수의 할당이 불가능하다.
실수로 다른 변수 값으로 바꿔치기 되는 대참사를 막을 수 있다.

참고로 user 객체는 불변은 아니다. 즉, 재할당이 불가능한 거지 객체의 내부 변수 값은 바꿀 수 있다.

만약 final class 를 이용하여 객체를 생성할 때 해당 변수에 final을 선언하면 어떻게 될까?
위의 내용을 다시 정리하자면,
1. class 의 final 은 상속이 불가하다라는 것을 의미한다.
2. 참조변수의 final은 초기화 후 재할당 할 수 없는 것을 의미한다.
   다시 한번 말하지만 해당 클래스 자체를 완전한 불변(immutable)으로 만드는 것은 아니다.

따라서 아래의 생성된 객체 내부의 변수(`a`)를 수정하는 건 가능하다.
```
final class XXXclass{
	private int a = 5;
}

final XXXClass xxxClass = new XXXClass();
xxxClass.a = 10; // 수정이 가능하다!!
```


---

### 3. 필드(Field)

constant 상수 필드에 사용하거나, 생성자 맴버 변수에 final 을 사용할 수 있다.
이 경우 생성자가 완료되기 전에 모든 final 필드를 초기화해야 한다.(즉, 값을 할당해야한다)

```java
class Point{
	private static final GLOBAL_POINT = "10.0;
    
    public changePointToTenDotOne(){
    	this.GLOBAL_POINT = 10.1; // 이미 위에서 할당했기 떄문에 에러 발생!!
    }
}
```
위의 `GLOBAL_POINT` constant 변수는 final 키워드로 인해 재할당이 불가능한 변수가 되었다.
`Point.GLOBAL_POINT = "10.1"` 로 값을 바꾸려고 시도한다면 컴파일 에러가 난다.

![](https://velog.velcdn.com/images/kmss6905/post/776c5300-0757-485a-81b6-0ab61176fdc8/image.png)


아래의 코드는 OrderService 가 ProductRepository 를 의존하고 있으며, 생성 시점에 ProductRepository 를 생성자 파라미터로 받아 초기화한다.

그리고 `void order(int id)` 의 경우 제품의 번호를 받아 productRepository 로 부터 상품을 조회하도록 한다.

```java
public class OrderService {
    private ProductRepository productRepository;

    public OrderService(ProductRepository productRepository) {
        this.productRepository = productRepository;
    }

    public void order(int id) {
        Product product = productRepository.findId(id);
        // etc
    }
}
```
따라서 productRepository 는 null 이 되어선 안되고 반드시 초기화
`this.productRepostiroy = productRepostirot` 가 되어야한다.

만약 실수로 OrderService 생성 시점에 맴버변수 ProductRepository 를 초기화하는 코드를 깜빡 잊었다고 가정해보자. 아래와 같이 작성한다고 해서 컴파일 에러는 나지 않는다.

```java
public class OrderService {
    private ProductRepository productRepository;

    public OrderService() {
    	
    }

    public void order(int id) {
        Product product = productRepository.findId(id);
        // etc
    }
}

```

하지만 아래의 코드를 실행하면
```java
OrderService orderService = new OrderService();
orderService().order(1);
```

초기화 되지 않은 ProductRepository 를 호출하려고 했기 때문에 `NullPointException` 예외가 터진다.
```
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "variablesfinal.field.ProductRepository.findId(int)" because "this.productRepository" is null
	at variablesfinal.field.OrderService.order(OrderService.java:10)
	at variablesfinal.field.OrderMain.main(OrderMain.java:7)
```


이때 final 키워드를 맴버 변수에 사용한다면 클래스 생성 시점에 강제로 final 키워드가 붙은 맴버변수를 초기화 할 수 있도록 강제할 수 있다. 따라서 최소한 맴버 변수 초기화 하는 것을 깜빡해 `NullPointException`  에러가 나는 것은 막을 수 있다.

![](https://velog.velcdn.com/images/kmss6905/post/d9b47b0f-1888-4624-869b-4b3c51f67244/image.png)

변수 'productRepository'가 초기화되지 않았을 수 있다고 경고를 보낸다. 따라서 강제로 초기화하여 사용할 수 밖에 없도록 만든다.
```java
private final ProductRepostiroy productRepository;

public OrderService(ProductRepository productRepository){
	this.productRepository = productRepository;
}
```



---
### 4. 메서드 인자(Argument Varibles)
메서드의 인자에 final 키워드를 사용할 수 있다.
이 경우 받은 인자를 메서드 내부에서 재할당 하여 사용할 수 없다.

```java
public int plus(final int a, final int b){
	int a += b;
    return a;
}
```
메서드 인자 중 `a` 는 이미 final로 선언되었기 때문에 `a`에 다른 값을 재할당할 수 없다.
final 메서드 인자에 새로운 값을 할당할 수 없기 때문에 새로운 변수를 선언하여 사용해야한다.
```java
public int plus(final int a, final int b){
	int c = a + b;
    return c;
}
```


## 요약

java 의 final 키워드는 클래스, 메서드, 메서드의 아규먼트, 필드(consant, member varibles) 에 사용가능하다. final 은 말그대로 최종이라는 뜻으로서 재할당을 막는 데 목표를 두고있다.그렇기 때문에 적절한 final 키워드를 사용해서 재할당을 하지 말도록 하는 의미를 들어내도록 사용할 수 있다.

특히, 생성자 맴버변수에 final을 사용하게 될 경우, 재할당 뿐만 아니라 생성자 초기화 시 무조건 할당해야 하도록 강제하는 역할을 하기도 한다.



### 참조

---
https://www.baeldung.com/java-final
