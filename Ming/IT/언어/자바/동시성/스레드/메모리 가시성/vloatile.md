> vloatile 키워드는 **메인 메모리에 직접 접근**하여 값을 변경, 로드 한다.

![[Pasted image 20250427192035.png]]

---

### volatile 사용 예시
```
static class MyTask implements Runnable {  
	// boolean runFlag = true;  
	volatile boolean runFlag = true;  
  
	@Override  
	public void run() {  
		log("task 시작");  
		  
		while (runFlag) {  
			// runFlag가 flase로 변하면 탈출  
			Thread.yield();  
		}  
		log("task 종료");  
	}  
}
```