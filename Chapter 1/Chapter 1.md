# Chapter 1. 도메인 모델 시작하기

## 💭 도메인이란?

- **구현해야 할** 소프트웨어 대상

  ex) 온라인 서점 -> 책을 판매하는 데 필요한 상품 조회, 구매, 결제 등의 기능을 제공

- 한 도메인은 하위 도메인으로 나눌 수 있음

  ex) 온라인 서점 도메인
  ![도메인은 여러 하위 도메인으로 구성](image.png)

  - 한 하위 도메인은 다른 하위 도메인과 연동하여 완전한 기능 제공

    ex) 고객이 물건을 구매하게 되면 주문, 결제, 배송, 혜택 하위 도메인의 기능이 엮이게 됨

  > 도메인마다 꼭 하위 도메인이 존재하는 것은 아니고, 하위 도메인 구성 여부는 상황에 따라 달라짐

## 💭 도메인 모델

- 특정 도메인을 개념적으로 표현한 것

- 도메인 모델을 사용하면 여러 이해관계자들이 동일한 모습으로 도메인을 이해 가능

- 도메인 모델을 객체로만 모델링 할 수 있는 것은 아님 -> 다양한 다이어그램 (ex: 상태 다이어그램 등..) 을 이용해서 모델링 가능

![객체를 이용한 도메인 모델](image-2.png)

- 객체 모델 - 도메인이 제공하는 기능과 도메인의 주요 데이터 구성을 파악하기 좋음

![상태 다이어그램 모델](image-3.png)

- 상태 다이어그램 모델 - 도메인의 상태 변경을 파악하기 좋음

- UML 표기법 (ex: 클래스 다이어그램, 상태 다이어그램 등.. )뿐만 아니라 그래프와 같은 방식을 이용해서 모델링할 수 있음

> 도메인에 따라 용어 의미가 결정되기 때문에 여러 하위 도메인을 하나의 다이어그램에 모델링해서는 안됨

## 💭 도메인 모델 패턴

- 일반적인 어플리케이션의 네 개의 영역
  ![아키텍처 구성](image-4.png)

> - **사용자 인터페이스 (표현)** : 사용자의 요청을 처리하고 사용자에게 정보를 보여줌. 이때, 사용자는 사람뿐만 아니라 외부 시스템도 가능
> - **응용** : 사용자가 요청한 기능 실행. 업무 로직 구현 없이 도메인 계층 조합해서 기능 실행
> - **도메인**: 시스템이 제공할 도메인 규칙 구현
> - **인프라스트럭처**: 데이터베이스, 메시징 시스템과 같은 외부 시스템과의 연동 처리

- 도메인 계층은 도메인의 핵심 규칙을 구현
- 도메인 규칙을 객체 지향 기법으로 구현하는 패턴이 **'도메인 모델 패턴'**

- 핵심 규칙을 구현한 코드는 도메인 모델에만 위치하기 때문에 규칙이 변경되거나 확장될 시, 다른 코드에 영향을 덜 줄게됨
  > **➕ 개념 모델**
  > : 순수하게 문제를 분석한 결과물로, DB나 트랜젝션 처리, 성능 등을 고려하고 있지 않음.
  > 개념 모델을 만들 때 완벽하게 도메인 모델을 만드는 것은 사실상 불가능 -> 처음부터 완벽한 개념 모델을 만들기 보다는, 전반적인 개요를 파악할 수 있는 정도로 하고 점진적으로 구현모델로 발전시켜야함

## 💭 도메인 모델 도축

주문 도메인과 관련된 몇 가지 요구사항

1. 최소한 종류 이상의 상품을 주문해야 한다.
2. 한 상품을 한 개 이상 주문할 수 있다.
3. 총 주문 금액은 각 상품의 구매 가격 합을 모두 더한 금액이다.
4. 각 상품의 구매 가격 합은 상품 가격에 구매 개수를 곱한 값이다.
5. 주문할 때 배송지 정보를 반드시 지정해야 한다.
6. 배송지 정보는 받는 사람 이름, 전화번호, 주소로 구성된다.
7. 출고를 하면 배송지를 변경할 수 없다.
8. 출고 전에 주문을 취소할 수 있다.
9. 고객이 결제를 완료하기 전에는 상품을 준비하지 않는다.

위 요구사항에서 주문은 '출고 상태로 변경하기', '배송지 변경하기', '주문 취소하기', '결제 완료하기' 기능을 제공한다는 것을 알 수 있음

```java
public class Order {
    public void changeShipped() {...}
    public void changeShippingInfo(ShippingInfo newShipping) {...}
    public void cancel() {...}
    public void completePayment() {...}
}
```

다음 요구사항은 '주문 항목'이 어떤 데이터로 구성되는지 알려줌

- 한 상품을 한 개 이상 주문할 수 있다.
- 각 상품의 구매 가격 합은 상품 가격에 구매 개수를 곱한 값이다.

-> '주문 항목'을 표현하는 **_OrderLine_**은 최소한 주문할 상품, 상품의 가격, 구매 개수, 각 구매 항목의 구매 가격을 포함해야함

- OrderLine은 한 상품(product)을 얼마에(price) 몇 개 살지(quantity)를 담고 있고 calculateAmounts() 메서드로 구매가격을 구하는 로직을 구현하고 있음

```java
public class OrderLine {
	private Product product;
	private int price;
	private int quantity;
	private int amounts;

	public OrderLine(Product product, int price, int quantity) {
		this.product = product;
		this.price = price;
		this.quantity = quantity;
		this.amounts = calculateAmounts();
	}

	private int calculateAmounts() {
		return price * quantity;
	}
	...
}
```

다음 요구사항은 Order와 OrderLine의 관계를 알려줌

- 최소한 종류 이상의 상품을 주문해야 한다.
- 총 주문 금액은 각 상품의 구매 가격 합을 모두 더한 금액이다.

```java
public class Order {
	private List<OrderLine> orderLines;
	private Money totalAmounts;

	public Order(List<OrderLine > orderLines) {
		setOrderLines(orderLines);
	}

	private void setOrderLines(List<OrderLine > orderLines) {
		verifyAtLeastOne0rMore0rderLines(orderLines);
		this.orderLines = orderLines;
		calculateTotalAmounts();
	}

	private void verifyAtLeastOneOrMoreOrderLines(List<OrderLine> orderLines) {
		if (orderLines == null || orderLines.isEmpty()) {
		throw new IllegalArgumentException("no OrderLine");
		}
	}

	private void calculateTotalAmounts() {
	int sum = orderLines.stream()
            .mapToInt (x -> x.getAmounts())
            .sum();
	this.totalAmounts = new Money(sum);
	}
}
```

- Order은 한 개 이상의 OrderLine을 가질 수 있으므로 Order를 생성할 때 OrderLine 목록을 List로 전달
- 생성자에서 호출하는 `setOrderLines()` 메서드는 요구사항에 정의한 제약 조건을 검사함
- 요구사항에 따르면 최소 한 종류 이상의 상품을 주문해야함 -> `verifyAtLeastOne0rMore0rderLines()` 메서드를 이용해서 OrderLine이 한 개 이상 존재하는지 검사
- `calculateTotalAmounts()` 메서드를 이용해서 총 주문 금액 계산

배송지 정보는 이름, 전화번호, 주소 데이터를 가지므로 ShippingInfo 클래스를 다음과 같이 정의 가능

```java
public class ShippingInfo {
	private String receiverName;
	private String receiverPhoneNumber;
	private String shippingAddress1;
	private String shippingAddress2;
	private String shippingZipcode;

	... 생성자,getter
}
```

'5. 주문할 때 배송지 정보를 반드시 지정해야 한다.' 라는 요구사항을 토대로 Order를 생성할 때 OrderLine의 목록뿐만 아니라 ShippingInfo도 함께 전달해야함 -> 생성자에 반영

```java
public class Order {
	private List<OrderLine> orderLines;
	private ShippingInfo shippingInfo;
	...

	public Order(List<OrderLine > orderLines, ShippingInfo shippingInfo) {
		setOrderLines(orderLines);
		setShippingInfo(shippingInfo);
	}

	private void setShippingInfo(ShippingInfo shippingInfo) {
		if (shippingInfo == null)
			throw new IllegalArgumentException("no ShippingInfo");
		this.shippingInfo = shippingInfo;
	}
	...
}
```

생성자에서 호출하는 `setShippingInfo()` 메서드는 ShippingInfo가 Null이면 익셉션이 발생 -> '배송지 정보 필수'라는 도메인 규칙 구현

도메인을 구현하다 보면 특정 조건이나 상태에 따라 제약이나 규칙이 달리 적용되는 경우가 많음. 주문 요구사항에서는 다음과 같은 내용이 제약과 규칙에 해당

- 출고를 하면 배송지 정보를 변경할 수 없다.
- 출고 전에 주문을 취소할 수 있다.
- 고객이 결제를 완료하기 전에는 상품을 준비하지 않는다.

위 요구사항은 상태에 따라 다른 제약 사항들을 알려줌. 다음과 같은 열거 타입을 활용해서 상태 정보 표현 가능

```java
public enum OrderState {
	PAYMENT_WAITING, PREPARING, SHIPPED, DELIVERING, DELIVERY_COMPLETED,
	CANCELED;
}
```

배송지 변경이나 주문 취소 기능은 출고 전에만 가능하다는 제약 규칙 적용을 위해 `changeShippingInfo()`와 `cancel()`은 `verifyNotYetShipped()` 메서드를 먼저 실행

```java
public class Order {
	private OrderState state;

	public void changeShippingInfo(ShippingInfo newShippingInfo) {
		verifyNotYetShipped();
		setShippingInfo(newShippingInfo);
	}

	public void cancel() {
		verifyNotYetShipped();
		this.state = OrderState.CANCELED;
	}

	private void verifyNotYetShipped() {
		if (state != OrderState.PAYMENT WAITING && state != OrderState.PREPARING)
			throw new IllegalStateException("aleady shipped");
	}
	...
}
```

> 메서드 이름 또한 도메인을 더 잘 파악하게 되면서 구체적인 의미를 파악할 수 있도록 변경

## 💭 엔티티와 벨류

도출한 모델은 엔티티와 벨류로 구분할 수 있음
![엔티티와 벨류 존재](image-5.png)

- 엔티티와 벨류를 제대로 구분해야 도메인을 올바르게 설계하고 구현할 수 있음

### 👀 엔티티

- 가장 큰 특징은 식별자를 지님
- 식별자는 엔티티 객체마다 고유하기 때문에 각 엔티티는 서로 다른 식별자를 가짐
  ex) 주문 도메인에서 각 주문은 주문 번호를 가지고 있음. 이 주문 번호는 각 주문마다 서로 다름 -> 주문번호가 주문의 식별자가 됨

  ![alt text](image-6.png)
  : Order가 엔티티가 되며 주문번호를 속성으로 가지게 됨

- 엔티티를 생성하고 속성을 바꾸고 삭제할 때까지 식별자는 바뀌지 않고 유지됨

  - 엔티티를 구현한 클래스는 다음과 같이 식별자를 이용해서 equals(), hashCode() 메서드 구현 가능

  ```java
  public class Order {
  private String orderNumber;

  @Override
  public boolean equals(Object obj) {
  	if (this == obj) return true;
  	if (obj== null) return false;
  	if (obj.getClass0 != Order.class) return false;
  	Order other = (Order)obj;
  	if (this.orderNumber = = null) return false;
  	return this.orderNumber.equals(other.orderNumber);
    }

  @Override
  public int hashCode() {
  	final int prime = 31;
  	int result = 1;
  	result = prime * result + ((orderNumber == null) ? 0 : orderNumber .hashCode);
  	return result;
      }
  }
  ```

### 👀 엔티티의 식별자 생성

- 엔티티의 식별자를 생성하는 시점은 도메인 특징과 사용하는 기술에 따라 달라짐.

  - 특정 규칙에 따라 생성

    - 주문번호, 운송장번호, 카드번호와 같은 식별자는 특정 규칙에 따라 생성
    - 도메인에 따라 다르고 같은 주문번호라도 회사마다 다른
      ex) 두 온라인 서점에서 구매한 책의 주문번호는 각각 '2021112831728OOOO' 와 '001-A88277OOOO'
    - 흔히 사용하는 규칙은 현재 시간과 다른 값을 함께 조합
      - 주의해야할 점은 같은 시간에 동시에 식별자를 생성해도 같은 식별자가 만들어져서는 안됨

  - UUID(universally unique identifier)나 Nano ID와 같은 고유 식별자 생성기 사용

    - 다수의 개발 언어가 UUID 생성기 제공
      ex) 자바는 java.util.UUID 클래스 사용

      ```java
      UUID uuid = UUID.randomUUID();

      //613f2sd-c3212-4sfds9sd-2313-ds1233과 같은 형식 문자열
      String strUuid = uuid.toString0;
      ```

    - 값을 직접 입력

      - 회원의 아이디나 이메일과 같은 식별자는 값을 직접 입력
      - 사용자가 직접 입력하는 값은 식별자를 중복해서 입력하지 않도록 사전에 방지하는 것이 중요

    - 일련번호 사용 (시퀀스나 DB의 자동 증가 칼럼 사용)

      - 주로 데이터베이스가 제공하는 자동 증가 기능 사용
        ex) 오라클 - 시퀀스 / MySQL - 자동 증가 칼럼
      - 자동 증가 칼럼을 제외한 다른 방식은 식별자를 먼저 만들고 엔티티 객체를 생성할 때 식별자 전달
        -> 자동 증가 칼럼의 경우, DB테이블에 데이터를 삽입해야 값을 알 수 있기 때문에 그 전까지는 모름. 따라서 **엔티티 객체를 생성할 때 식별자를 전달할 수 없음을 의미**

            ```java
            Article article = new Article(author, title, ...);
            articleRepository.save(article); // DB에 저장한 뒤 구한 식별자를 엔티티에 반영
            Long savedArticleId = article.getId(); //DB에 저장한 후 식별자 참조 가능
            ```

### 👀 벨류 타입

![alt text](image-7.png)

ShippingInfo 클래스는 받는 사람과 주소에 대한 데이터를 가지고 있음. receiverName필드와 receiverPhoneNumber 필드는 개념적으로 받는 사람 의미

- 벨류 타입은 **개념적으로 완전한 하나를 표현**할 때 사용
  ex) 사람을 위한 벨류 타입인 Receiver를 다음과 같이 작성 가능

```java
public class Receiver {
	private String name;
	private String phoneNumber;

	public Receiver(String name, String phoneNumber) {
		this.name = name;
		this.phoneNumber= phoneNumber;
	}

	public String getName() {
		return name;
	}

	public String getPhoneNumber() {
		return phoneNumber;
	}
}
```

- Receiver는 받는 사람이라는 도메인 개념 표현
- 벨류 타입을 사용함으로써 개념적으로 완전한 하나를 잘 표현할 수 있음

- 벨류 타입을 이용해서 ShippingInfo 클래스를 다시 구현해보면 배송정보가 받는 사람과 주소로 구성된다는 것을 알 수 있음

- 벨류 타입이 꼭 두 개 이상의 데이터를 가져야하는 것은 아님

  ```java
  public class OrderLine {
    private Product product;
    private int price;
    private int quantity;
    private int amounts;
    }
    ...
  ```

  - OrderLine의 priced와 amount는 int 타입의 숫자를 사용하고 있지만 '돈'을 의미함
  - '돈'을 의미하는 Money 타입을 만들어 사용

  ```java
  public class Money {
  private int value;

  public Money(int value) {
  	this.value = value;
  }

  public int getValue() {
  	return this.value;
  }
  }
  ```

  ```java
    public class OrderLine {
  private Product product;
  private Money price;
  private int quantity;
  private Money amounts;
    ...
    }
  ```

  -> Moeny 타입 덕에 price나 amounts가 금액을 의미한다는 것을 쉽게 알 수 있음

- 또 다른 장점은 밸류 타입을 위한 기능을 추가할 수 있음

ex) Money 타입은 돈 계산을 위한 기능을 추가할 수 있음

```java
public class Money {
	private int value;

	... 생성자, getValue()

	public Money add (Money money) {
		return new Money(this.value + money.value);
	}

	public Money multiply(int multiplier) {
	return new Money(value * multiplier);
	}
  }
```

- 밸류 객체의 데이터를 변경할 때는 기존 데이터를 변경하는 방식보다는 변경한 데이터를 갖는 새로운 밸류 객체를 생성하는 방식을 선호

ex) Money 클래스의 add() 메서드는 Moeny를 새로 생성하고 있음

- 데이터 변경 기능을 제공하지 않는 타입을 '불변'이라고 함

  - 불변으로 구현하면 안전한 코드를 작성 가능함

  ex) OrderLine을 생성하려면 Money 객체를 전달해야함

  ```java
  Money price = ...;
  OrderLine line = new OrederLine(product, price, quantity);
  // 만약 price.setValue(0)로 값을 변경할 수 있다면?
  ```

  만약 이 경우 Money가 setValue()와 같은 메서드를 제공해서 값을 변경한다면 OrderLine의 price 값이 잘못 반영되는 상황이 발생

  -> 따라서 OrderLine 생성자는 아래와 같이 새로운 Money 객체를 생성하도록 코드를 작성해야함

  ```java
  public class OrderLine {
  ...
  private Money price;

  public OrderLine(Product product, Moeny price, int quantity){
      this.product = product;
      // Money가 불변 객체가 아니라면 price 파라미터가 변경될 때 발생하는 문제를 방지하기 위해 데이터를 복사한 새로운 객체를 생성해야함
      this.price = new Moeny(price.getValue());
      this.quantity = quantity;
      this.amounts = calculateAmounts();
  }
  }
  ```

### 👀 엔티티 식별자와 밸류 타입

- 엔티티 식별자의 실제 데이터는 String과 같은 문자열로 구성된 경우가 많음
- Moeny가 단순 숫자가 아닌 도메인의 '돈'을 의미하는 것처럼 식별자를 위한 밸류 타입을 사용해서 의미가 잘 드러나게 할 수 있음

ex) 주문번호 표현을 위해 Order의 식별자 타입을 String 대신 OrderNo 밸류 타입을 사용 -> 해당 필드가 주문번호라는 것을 알 수 있음

```java
public class Order {
	//OrderNo 타입 자체로 id가 주문번호임을 알 수 있다.
	private OrderNo id;
	...
	public OrderNo getId() {
		return id;
    }
}
```

### 👀 도메인 모델에 set 메서드 넣지 않기

- set 메서드는 도메인의 핵심 개념이나 의도를 코드에서 사라지게함
  - set 메서드는 필드값만 변경하고 상태 변경과 관련된 도메인 지식이 코드에서 사라지게 됨
- set 메서드를 사용하면 도메인 객체를 생성할 때 온전하지 않은 상태가 될 수 있음

- 도메인 객체가 불완전한 상태로 사용되는 것을 막으려면 생성 시점에 필요한 것을 전달해 주어야함. 즉, **생성자를 통해 필요한 데이터를 모두 받아야함**

```java
Order order = new Order(orderer, lines, shippingInfo, OrderState.PREPARING);
```

- 생성자로 필요한 것을 모두 받으면 생성자를 호출하는 시점에 필요한 데이터가 올바른지 검사 가능

- set 메서드가 private이라면 외부에서 데이터를 변경할 목적으로 set 메서드를 사용할 수 없음
- set 메서드를 구현해야할 특별한 이유가 없다면 불변 타입의 장점을 살릴 수 있도록 밸류 타입은 **불변**으로 구현

## 💭 도메인 용어와 유비쿼터스 용어

- 코드를 작성할 때 도메인에서 사용하는 용어는 매우 중요함

- 도메인에서 사용하는 용어를 코드에 반영하지 않으면 해당 코드는 개발자에게 코드의 의미를 해석해야 하는 부담을 줌

```java
public OrderState {
	STEP1, STEP2, STEP3, STEP4, STEP5, STEP6
}
```

해당 코드의 의미를 이해하려먼 STEP1, STEP2 등이 각각 어떤 상태인지 해석이 필요함

```java
public enum OrderState {
	PAYMENT_WAITING, PREPARING, SHIPPED, DELIVERING, DELIVERY_COMPLETED;
}
```

위와 같이 도메인 용어를 사용해서 OrderState를 구현하면 해석 과정이 줄어듬

> 알맞은 영단어를 찾는 것이 쉽지는 않지만 시간을 들여 찾는 노력을 해야함. **도메인 용어에 알맞은 단어를 찾는 시간을 아까워하지 말자**
