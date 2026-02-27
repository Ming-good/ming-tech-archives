## 객체와 관계형 모델
 ![[Pasted image 20260225002630.png]]

## JPA 아키텍처 레이어
JPA는 애플리케이션과 JDBC 사이에서 동작하며, 객체와 관계형 DB를 매핑하는 다리 역할을 합니다.
- **Application**: 
	- 비즈니스 로직을 수행하며 `EntityManager`를 통해 데이터 작업을 요청합니다.
- **JPA Interface (API)**: 
	- 자바 표준 명세(`jakarta.persistence`). 
	- 어노테이션(@Entity, @Id 등)과 인터페이스를 제공합니다.
- **JPA Implementation (Persistence Provider)**: 
	- JPA 표준을 실제로 구현한 엔진입니다. (예: **Hibernate**, EclipseLink).
- **JDBC API**: 
	- 하이버네이트가 생성한 SQL을 실제 DB로 전달하는 자바 표준 인터페이스입니다.
- **JDBC Driver**: 
	- 특정 데이터베이스(PostgreSQL, MySQL 등)와 통신하기 위한 전용 통신 규격 라이브러리입니다.
- **Database**: 실제 데이터가 저장되는 물리적 저장소입니다.

## 퓨어 JPA 초기 설정 및 의존성
![[Pasted image 20260225175029.png]]
```kotlin
fun main() {  
    // 1. 공장 생성  
    val emf = Persistence.createEntityManagerFactory("test-customer")  
  
    // 2. 관리자 생성  
    val em = emf.createEntityManager()  
    val tx = em.transaction  
  
    try {  
        tx.begin()  
  
        //  
        // INSERT        
        /*em.persist(Customer.sample())*/  
        
        // SELECT        
        val find = em.find<Customer>(Customer::class.java, "ID0001")  
        val let = find.let {  
            "=== FIND: ${find.toString()}"  
        }  
  
        // UPDATE  
        find.name = "이민규1"  
        //  
  
        logger.info(let)  
        tx.commit()  
    } catch (e: Exception) {  
        tx.rollback() // 에러 발생 시 안전하게 롤백  
        e.printStackTrace()  
        logger.error("ERROR: ",e)  
    } finally {  
        em.close()  
        emf.close()  
    }  
}

```
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<persistence version="2.2"  
  xmlns="http://xmlns.jcp.org/xml/ns/persistence"  
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">  
  
  <persistence-unit name="test-customer">  
    <class>test.jpa.entity.Customer</class>  
    <properties>      
    <property name="javax.persistence.jdbc.driver" value="org.postgresql.Driver"/>  
      <property name="javax.persistence.jdbc.user" value="test"/>  
      <property name="javax.persistence.jdbc.password" value="1897"/>  
      <property name="javax.persistence.jdbc.url" value="jdbc:postgresql://localhost:5432/test_service"/>  
  
      <property name="hibernate.dialect" value="org.hibernate.dialect.PostgreSQLDialect"/>  
  
      <property name="hibernate.show_sql" value="true"/>  
      <property name="hibernate.format_sql" value="true"/>  
      <property name="hibernate.use_sql_comments" value="true"/>  
      <property name="hibernate.hbm2ddl.auto" value="update"/>  
    </properties>  
  </persistence-unit>  
</persistence>
```
> resource > META-INF > persistence.xml 추가


