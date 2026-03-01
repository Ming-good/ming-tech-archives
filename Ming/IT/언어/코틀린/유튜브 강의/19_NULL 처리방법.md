```kotlin
  
fun main() {  
    var str:String? = null  
  
    // null safe 연산자  
    println(str?.uppercase())  
    // elvis 연산자  
    println(str?:"test".uppercase())  
    try {  
        println(str?:throw Exception("elvis 응용"))  
    } catch (e: Exception) {  
        println("elvis오류오류")  
    }  
  
    // non-null assertion 연산자  
    try {  
        println(str!!.uppercase())  
    } catch (e: Exception) {  
        println("assertion오류오류")  
    }  
  
    // null safe연산자 응용 권장  
    str?.run {  
        println(str.uppercase())  
        println(str.uppercase())  
    }  
  
    println("====================================")  
    var a = Product("콜라", 1000)  
    var b = Product("콜라", 1000)  
    var c = a  
    var d = Product("사이다", 1000)  
  
    println(a == b)  
    println(a === b)  
  
    println(a == c)  
    println(a === c)  
  
    println(a == d)  
    println(a === d)  
}  
  
class Product(var name:String, val price:Int) {  
    override fun equals(other: Any?): Boolean {  
        if (other is Product)  
        {  
            return other.name == name && other.price == price  
        } else {  
            return false  
        }  
    }  
}
```

| **연산자 / 방식**    | **명칭**                 | **동작 방식 (Runtime 기준)**          | **자바(Java) 대응 코드**                         |
| --------------- | ---------------------- | ------------------------------- | ------------------------------------------ |
| **`?.`**        | **Safe Call**          | 객체가 `null`이면 `null` 반환, 아니면 실행  | `if (obj != null) obj.func() else null`    |
| **`?:`**        | **Elvis Operator**     | 좌항이 `null`이면 우항(기본값)을 실행        | `Optional.ofNullable(obj).orElse(default)` |
| **`!!`**        | **Non-null Assertion** | **무조건 실행.** `null`이면 **NPE 던짐** | `obj.func()` (자바의 기본 동작과 동일)               |
| **`?.run { }`** | **Safe Call + Scope**  | 객체가 `null`이 아닐 때만 블록(`{ }`) 실행  | `if (obj != null) { ... }`                 |
