## Class 메타데이터 조회 방법 3가지
```java
// 1. 클래스에서 찾기  
Class<BasicData> basicDataClass = BasicData.class;  
System.out.println("basicDataClass1 : " + basicDataClass);  
  
// 2 인스턴스에서 찾기  
BasicData basicInstance = new BasicData();  
Class<? extends BasicData> basicDataClass2 = basicInstance.getClass();  
System.out.println("basicDataClass2 : " + basicDataClass2);  
  
// 3. 문자로 찾기  
String classNm = "reflection.data.BasicData";  
Class<?> basicDataClass3 = Class.forName(classNm);  
System.out.println("basicDataClass3 : " + basicDataClass3);
```


## Class 기본 정보 조회
```java
Class<BasicData> basicDataClass = BasicData.class;  
  
System.out.println("basicData.getName() : " + basicDataClass.getName());  
System.out.println("basicData.getSimpleName() : " + basicDataClass.getSimpleName());  
System.out.println("basicData.getPackage() : " + basicDataClass.getPackage());  
  
System.out.println("basicData.getSuperclass() : " + basicDataClass.getSuperclass());  
System.out.println("basicData.getInterfaces() : " + Arrays.toString(basicDataClass.getInterfaces()));  
  
System.out.println("basicData.isInterface() : " + basicDataClass.isInterface()); 
System.out.println("basicData.isEnum() : " + basicDataClass.isEnum());  
System.out.println("basicData.isAnnotation() : " + basicDataClass.isAnnotation());  
  
int modifiers = basicDataClass.getModifiers();  
System.out.println("basicData.getModifiers() : " + modifiers);  
System.out.println("Modifier.isPublic() : " + Modifier.isPublic(modifiers));  
System.out.println("Modifier.toString() : " + Modifier.toString(modifiers));
```
``` console
basicData.getName() : reflection.data.BasicData
basicData.getSimpleName() : BasicData
basicData.getPackage() : package reflection.data
basicData.getSuperclass() : class java.lang.Object
basicData.getInterfaces() : []
basicData.isInterface() : false
basicData.isEnum() : false
basicData.isAnnotation() : false
basicData.getModifiers() : 1
Modifier.isPublic() : true
Modifier.toString() : public
```


