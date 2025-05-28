```java
FileOutputStream fos = new FileOutputStream("temp/print.txt");
PrintStream printStream = new PrintStream(fos);
printStream.println("hello java!");
printStream.println(10);
printStream.println(true);
printStream.printf("hello %s", "world");
printStream.close();
```

- 이 기능을 사용하면 마치 콘솔에 출력하는 것 처럼 파일이나 다른 스트림에 **문자를 출력**할 수 있다.

