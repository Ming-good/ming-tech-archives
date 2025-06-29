> jdk11에 추가된 메서드이다. 
> 같은 문자를 count 수 만큼 붙여서 반환한다.

```java
String.repeat(int count)
```

- 내부적으로 `Arrays.fill(byte[], byte)`와 `System.arraycopy(src, srcPos, dest, destPos, length)`를 사용함으로써 메모리 효율적이고 for문보다 빠르다.