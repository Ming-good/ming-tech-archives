
```
public class StartTest4Main {  
	public static void main(String[] args) {  
			Runnable ARunnable = new TestRunnable("A", 1000);  
			Runnable BRunnable = new TestRunnable("B", 500);  
			Thread a = new Thread(ARunnable, "Thread-A");  
			a.start();  
			Thread b = new Thread(BRunnable, "Thread-B");  
			b.start();  
		}  
		  
		static class TestRunnable implements Runnable{  
			String name = "";  
			Integer time = 0;  
		public TestRunnable(String name, Integer time) {  
			this.name = name;  
			this.time = time;  
		}  
		  
		@Override  
		public void run(){  
			while (true){  
				log(this.name);  
				try {  
					Thread.sleep(this.time);  
				} catch (InterruptedException e) {  
					throw new RuntimeException(e);  
				}  
			}  
		}  
	}
}
```

