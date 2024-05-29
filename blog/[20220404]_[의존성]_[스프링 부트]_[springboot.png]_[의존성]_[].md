## 의존성
어떤 객체가 협력하기 위해 다른 객체를 필요로 할 때 두 객체 사이의 의존성이 존재하게 된다.

의존성은 실행 시점과 구현 시점에 서로 다른 의미를 가진다.

- 컴파일타임 의존성: 코드를 작성하는 시점에서 발생하는 의존성. 클래스 사이의 의존성.
- 런타임 의존성: 애플리케이션이 실행되는 시점의 의존성. 객체 사이의 의존성.

## 결합도
결합도란 "하나의 객체가 변경이 일어날 때에 관계를 맺고 있는 다른 객체에게 변화를 요구하는
정도"라고 볼 수 있다.

어떤 두 요소 사이에 존재하는 의존성이 바람직할 때 두 요소가 느슨한 결합도 또는 약한 결합도
를 가진다고 말한다.

반대로 두 요소의 의존성이 바람직하지 못할 때 단단한 결함도 또는 강한 결합도를 가진다고 말한다.

## 예제
다음 Order 클래스는 dicsountAmount 필드를 통해 totalAmount 메소드에서 직접 할인 로직을 정한다.

```Java
public class Order {

	private final UUID orderId;
	private final UUID customerId;
	private final List<OrderItem> orderItems;
	private long discountAmount;
	private OrderStatus orderStatus = OrderStatus.ACCEPTED;

	public Order(UUID orderid, UUID customerId, List<OrderItem> orderItems, long discountAmount) {
		this.orderId = orderid;
		this.customerId = customerId;
		this.orderItems = orderItems;
		this.discountAmount = discountAmount;
	}

	public long totalAmount() {
		var beforeDiscount = orderItems.stream()
			.map(v -> v.getProductPrice() * v.getQuantity())
			.reduce(0L, Long::sum);

		return beforeDiscount - discountAmount;
	}

	public void setOrderStatus(OrderStatus orderStatus) {
		this.orderStatus = orderStatus;
	}
}
```

이를 변경해 Voucher 클래스를 통해 할인 로직을 정한다.

아래와 같이 FixedAmountVoucher를 의존하는 Order로 바꿀 수 있다.

또한 totalAmount 메소드에서 할인을 구하는 역할을 FixedAmountVoucher로 위임한다.


```Java
public class FixedAmountVoucher {
private final long amount;

	public FixedAmountVoucher(long amount) {
		this.amount = amount;
	}

	public long discount(long beforeDiscount) {
		return beforeDiscount - amount;
	}
}

public class Order {
private final UUID orderId;
private final UUID customerId;
private final List<OrderItem> orderItems;
private FixedAmountVoucher fixedAmountVoucher;
private OrderStatus orderStatus = OrderStatus.ACCEPTED;

	public Order(UUID orderid, UUID customerId, List<OrderItem> orderItems, long discountAmount) {
		this.orderId = orderid;
		this.customerId = customerId;
		this.orderItems = orderItems;
		this.fixedAmountVoucher = new FixedAmountVoucher(discountAmount);
	}

	public long totalAmount() {
		var beforeDiscount = orderItems.stream()
				.map(v -> v.getProductPrice() * v.getQuantity())
				.reduce(0L, Long::sum);

		return fixedAmountVoucher.discount(beforeDiscount);
	}

	public void setOrderStatus(OrderStatus orderStatus) {
		this.orderStatus = orderStatus;
	}
}

public class OrderTester {
public static void main(String[] args) {

		var customerId = UUID.randomUUID();
		List<OrderItem> orderItems = new ArrayList<>() {{
				add(new OrderItem(UUID.randomUUID(), 100L, 1));
		}};
		var order = new Order(UUID.randomUUID(), customerId, orderItems, 10L);

		Assert.isTrue(order.totalAmount() == 90L, MessageFormat.format("totalAmount {0} is not 100L", order.totalAmount()));
	}
}
```

위에서 Order는 FixedAmountVoucher 클래스를 의존한다.

![](https://velog.velcdn.com/images/leehanju408/post/99863008-7b56-423d-88f7-62b29b856b83/image.png)

하지만 위 예제는 FixedAmountVoucher에 변경이 생기면 Order에도 영향이 간다.

따라서 느슨한 결합으로 바꿔야 한다.

느슨한 결합을 위해 컴파일 타임 의존성을 런타임 의존성으로 바꾼다.

이를 위해 Voucher 인터페이스를 만들고 Order 클래스에서 Voucher 인터페이스를 의존하도록 변경한다.

FixedAmountVoucher는 Voucher를 구현하도록 한다.

```Java
public interface Voucher {
UUID getVoucherId();
long discount(long beforeDiscount);
}

public class FixedAmountVoucher implements Voucher {

	private final UUID voucherId;
	private final long amount;

	public FixedAmountVoucher(UUID voucherId, long amount) {
		this.voucherId = voucherId;
		this.amount = amount;
	}

	@Override
	public UUID getVoucherId() {
		return voucherId;
	}

	@Override
	public long discount(long beforeDiscount) {
		return beforeDiscount - amount;
	}
}

public class PercentDiscountVoucher implements Voucher {

	private final UUID voucherId;
	private final long percent;

	public PercentDiscountVoucher(UUID voucherId, long percent) {
		this.voucherId = voucherId;
		this.percent = percent;
	}

	@Override
	public UUID getVoucherId() {
		return voucherId;
	}

	@Override
	public long discount(long beforeDiscount) {
		return beforeDiscount * (percent / 100);
	}
}
public class Order {
private final UUID orderId;
private final UUID customerId;
private final List<OrderItem> orderItems;
private Voucher voucher;
private OrderStatus orderStatus = OrderStatus.ACCEPTED;

	public Order(UUID orderid, UUID customerId, List<OrderItem> orderItems, Voucher voucher) {
		this.orderId = orderid;
		this.customerId = customerId;
		this.orderItems = orderItems;
		this.voucher = voucher;
	}

	public long totalAmount() {
		var beforeDiscount = orderItems.stream()
				.map(v -> v.getProductPrice() * v.getQuantity())
				.reduce(0L, Long::sum);

		return voucher.discount(beforeDiscount);
	}

	public void setOrderStatus(OrderStatus orderStatus) {
		this.orderStatus = orderStatus;
	}
}
```

Order 클래스에서 어떤 Voucher를 쓰는지 알 수 없다.

하지만 totalAmount 메소드는 voucher 인터페이스에 있는 discount 메소드를 사용해서 할인 로직을 사용할 수 있다.

또한 discount 메소드의 변경을 신경쓰지 않고 Order를 사용할 수 있다.

Voucher 지정은 런타임 시 다음과 같이 지정해줘야 한다.

```Java
public class OrderTester {

	public static void main(String[] args) {

		var customerId = UUID.randomUUID();
		List<OrderItem> orderItems = new ArrayList<>() {{
				add(new OrderItem(UUID.randomUUID(), 100L, 1));
		}};

		var fixedAmountVoucher = new FixedAmountVoucher(UUID.randomUUID(), 10L);
		var order = new Order(UUID.randomUUID(), customerId, orderItems, fixedAmountVoucher);

		Assert.isTrue(order.totalAmount() == 90L, MessageFormat.format("totalAmount {0} is not 100L", order.totalAmount()));
	}

}
```

다음과 같이 의존성을 정리할 수 있다.

![](https://velog.velcdn.com/images/leehanju408/post/4c7801e1-1bc1-4f0c-9359-3a20902f12e8/image.png)



## 코드

https://github.com/yanJuicy/kdt-spring-order/tree/2e5bbc52831e5a0a3c6b3358c8b236a94b086d54