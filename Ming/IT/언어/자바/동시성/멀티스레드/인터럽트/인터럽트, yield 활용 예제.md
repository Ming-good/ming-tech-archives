```java
package thread.control.print;  
  
import static thread.util.MyLogger.log;  
  
import java.util.Queue;  
import java.util.Scanner;  
import java.util.concurrent.ConcurrentLinkedQueue;  
  
public class MyPrinterV3 {  
	public static void main(String[] args) {  
		Printer printer = new Printer();  
		Thread printThread = new Thread(printer, "printer");  
		printThread.start();  
	  
		Scanner scanner = new Scanner(System.in);  
			while (true) {  
				log("프린터할 문서를 입력하세요 종료 (q): ");  
				String input = scanner.nextLine();  
				if (input.equals("q")) {  
					printThread.interrupt();  
					break;  
				}  
				printer.addJob(input);  
			}  
		}  
	  
	static class Printer implements Runnable {  
		Queue<String> que = new ConcurrentLinkedQueue<>(); // 동시성 제어에 사용  
  
		@Override  
		public void run() {  
			while (!Thread.interrupted()) {  
				if (que.isEmpty()) {  
					Thread.yield();  // 양보하기
					continue;  
				}  
				try {  
					String poll = que.poll();  
					log("출력 시작: " + poll + ", 대기문서: " + que);  
					Thread.sleep(3000);  
					log("출려 완료");  
				} catch (Exception e) {  
					log("인터럽트!");  
					break;  
				}  
			}  
				log("프린터 종료");  
		}  
		public void addJob(String input) {  
			que.offer(input);  
		}  
	}  
}
```