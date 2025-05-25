### 문제
##### 10MB파일 생성, 읽기
```java
FileOutputStream fos = new FileOutputStream(FILE_NAME);
for (int i = 0; i < FILE_SIZE; i++) {
	fos.write(1);
}
fos.close();
```

```java
FileOutputStream fos = new FileOutputStream(FILE_NAME);
for (int i = 0; i < FILE_SIZE; i++) {
	fos.write(1);
}
fos.close();
```
- 10MB 파일 하나를 쓰는데 14초, 읽는데 5초라는 매우 오랜 시간이 걸렸다.
- 오래 걸린 이유는 자바에서 1byte씩 디스크에 데이터를 전달하기 때문이다.
- 디스크는 1byte의 데이터를 받아서 1byte의 데이터를 쓴다. 이 과정을 무려 1000만 번 반복하는 것이다.
##### IO 속도가 느린 이유
- `write()` 나 `read()` 를 호출할 때마다 OS의 시스템 콜을 통해 파일을 읽거나 쓰는 명령어를 전달한다. 이러한 시스템 콜은 상대적으로 무거운 작업이다.
- HDD, SDD 같은 장치들도 하나의 데이터를 읽고 쓸 때마다 필요한 시간이 있다. HDD의 경우 더욱 느린데, 물리적으로 디스크의 회전이 필요하다.
> 즉, 한 번 데이터 전송시 많은 데이터를 담아서 보내면 된다.

##### 참고
이렇게 자바에서 운영 체제를 통해 디스크에 1byte씩 전달하면, 운영 체제나 하드웨어 레벨에서 여러가지 최적화가 발생한다. 따라서 실제로 디스크에 1byte씩 계속 쓰는 것은 아니다. 그렇다면 훨씬 더 느렸을 것이다. 하지만, 자바에서 1바이트씩 write()나 read()를 호출할 때마다 운영 체제로의 시스템 콜이 발생하고, 이 시스템 콜 자체가 상당한 오버헤드를 유발한다. 운영 체제와 하드웨어가 어느 정도 최적화를 제공하더라도, 자주 발생하는 시스템 콜로 인한 성능 저하는 피할 수 없다. 
결국 자바에서 read(), write() 호출 횟수를 줄여서 **시스템 콜 횟수도 줄여야 한다**.

---
### 개선
##### 버퍼활용
```java
FileOutputStream fos = new FileOutputStream(FILE_NAME);
byte[] buffer = new byte[BUFFER_SIZE];
int bufferIndex = 0;
for (int i = 0; i < FILE_SIZE; i++) {
	buffer[bufferIndex++] = 1;
	// 버퍼가 가득 차면 쓰고, 버퍼를 비운다.
	if (bufferIndex == BUFFER_SIZE) {
		fos.write(buffer);
		bufferIndex = 0;
	}
}

// 끝 부분에 오면 버퍼가 가득차지 않고 남아있을 수 있다. 버퍼에 남은 부분 쓰기
if (bufferIndex > 0) {
	fos.write(buffer, 0, bufferIndex);
}
fos.close();
```
-  직접 버퍼를 만들고 관리해야되는 번거로운 단점이 존재.
![[Pasted image 20250525215432.png]]
>  많은 데이터를 한 번에 전달하면 성능을 최적화 할 수 있다. 이렇게 되면 싯템 콜도 줄어들고 HDD,SDD 같은 장치들의 작동 횟수도 줄어든다. 하지만 버퍼가 커진다고 해서 속도가 계속 줄어들지 않는다.
>  왜냐하면 디스크나 파일 시스템에서 데이터를 읽고 쓰는 기본 단위가 보통 4kb 또는 8kb이기 때문이다.

---
##### BufferedOutputStream(OutputStream)
![[Pasted image 20250525220331.png]]

```java
BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(FILE_NAME), BUFFERE_SIZE);  
for (int i = 0; i < FILE_SIZE; i++) {  
	bos.write(1);  
}  
bos.close(); // 내부에 flush() 동작
```
##### 실행 과정
![[Pasted image 20250525221052.png]]

![[Pasted image 20250525221430.png]]

---

##### BufferedInputStream(InputStream)
![[Pasted image 20250525221922.png]]
##### 실행 과정
![[Pasted image 20250525222340.png]]
> BufferedInputStream은 버퍼의 크기만큼 데이터를 미리 읽어서 버퍼에 보관한다. 따라서 read()를 통해 1byte씩 데이터를 조회해도, 성능이 최적화 된다.

---
##### 버퍼를 직접 다루는 것 보다 BufferedXxx성능이 떨어지는 이유
- 예제1 쓰기: 14000ms (14초)
- 예제2 쓰기: 14ms (버퍼 직접 다룸)
- 예제3 쓰기: 102ms (BufferedXxx)
```java
// BufferedOutpuStream 내부 로직
@Override
public void write(int b) throws IOException {
	if (lock != null) {
		lock.lock();
		try {
			implWrite(b);
		} finally {
			lock.unlock();
		}
	} else {
		synchronized (this) {
			implWrite(b);
		}
	}
}
```
##### BufferedXxx클래스의 특징
- BufferedXxx는 자바 초창기 만들어진 클래스로, 처음부터 멀티 스레드를 고려해서 만든 클래스이다. 따라서 싱글 스레드 상황에서는 동기화 락이 필요하지 않기 때문에 직접 버퍼를 다룰 때와 비교해서 성능이 떨어진다.
- 일반적인 상황이라면 이 정도의 성능은 크게 문제가 되지 않음으로 BufferedXxx를 사용해도 충분하다.
- 만약에 성능이 중요한 상황이라면 직접 만든 코드를 사용하면 된다.