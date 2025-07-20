```java
  
static Map<Long, Order> orderRep = new HashMap<>();  
  
static {  
	orderRep.put(1L, new Order(1L, new Delivery("배송완료", false)));  
	orderRep.put(2L, new Order(2L, new Delivery("배송중", false)));  
	orderRep.put(3L, new Order(3L, new Delivery("배송중", true)));  
	orderRep.put(4L, new Order(4L, null));  
}  
  
public static void main(String[] args) {  
	System.out.println("1 = " + getDeliverysatus(1L));  
	System.out.println("2 = " + getDeliverysatus(2L));  
	System.out.println("3 = " + getDeliverysatus(3L));  
	System.out.println("4 = " + getDeliverysatus(4L));  
}  
private static String getDeliverysatus(Long orderId) {  
	return findOrder(orderId).map(Order::getDelivery)  
		.filter(d -> d.isCanceled() == false)  
		.map(Delivery::getStatus)  
		.orElse("배송X");  
}  
static Optional<Order> findOrder(Long orderId) {  
	return Optional.ofNullable(orderRep.get(orderId));  
}
```

```console
1 = 배송완료
2 = 배송중
3 = 배송X
4 = 배송X
```