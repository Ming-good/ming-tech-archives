- DataOutputStream` 을 사용하면 자바의 `String, int, double, boolean 같은 데이터 형을 편리하게 다룰 수 있다.

```java
FileOutputStream fos = new FileOutputStream("temp/data.dat");
DataOutputStream dos = new DataOutputStream(fos);
dos.writeUTF("회원A");
dos.writeInt(20);
dos.writeDouble(10.5);
dos.writeBoolean(true);
dos.close();

FileInputStream fis = new FileInputStream("temp/data.dat");
DataInputStream dis = new DataInputStream(fis);
System.out.println(dis.readUTF());
System.out.println(dis.readInt());
System.out.println(dis.readDouble());
System.out.println(dis.readBoolean());
dis.close();
```
##### 저장된 파일 내용
![[Pasted image 20250528221929.png]]
##### 읽어오는 방식
```java
dos.writeUTF("id1"); // 저장
dis.readUTF(); // 조회 id1
// 2byte(문자 길이) + 3byte(실제 문자 데이터)
------------------------

//저장 예시
dos.writeUTF("id1");
dos.writeUTF("name1");
dos.writeInt(20);
dos.writeUTF("id2");
dos.writeUTF("name2");
dos.writeInt(30);

//저장된 파일 예시
3id1(2byte(문자 길이) + 3byte)
5name1(2byte(문자 길이) + 5byte)
20(4byte)
3id2(2byte(문자 길이) + 3byte)
5name2(2byte(문자 길이) + 5byte)
30(4byte)
```
- DataInputStream은 2byte를 사용해서 문자 길이를 숫자로 보관하고 나머지 byte로 실제 문자 데이터를 보관한다
##### 주의 
- 꼭! 저장한 순서대로 읽어야 한다는 것이다 그렇지 않으면 잘못된 데이터가 조회될 수 있다.
- 저장한 `data.dat` 파일을 직접 열어보면 제대로 보이지 않는다. 왜냐하면 `writeUTF()` 의 경우 UTF-8 형식으로 저장하지만, 나머지의 경우 문자가 아니라 각 타입에 맞는 byte 단위로 저장하기 때문이다.