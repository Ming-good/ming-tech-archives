```kotlin
// DATA객체 선언 밑 사용법  
fun main() {  
    dataObj()  
    enumFun()  
}  
  
fun dataObj() {  
    val a = GeneralPerson("밍", 9)  
    println(a == GeneralPerson("밍", 9))  
    println(a.hashCode())  
    println(a)  
  
    val b = PersonData("민규", 33)  
    val c = PersonData("민규", 33)  
    val d = b  
    println(b == c)  
    println(b === d)  
    println(b.hashCode())  
    println(b)  
  
    println(b.copy(age=33))  
}  
  
fun enumFun() {  
    val aliase = PersonEnum.ALIASE  
    println(aliase)  
    println(aliase == PersonEnum.ALIASE)  
    println(aliase === PersonEnum.ALIASE)  
    println(aliase.isAlias())  
  
    var v2 = PersonEnum.ALIASE  
  
    println(aliase.ad(PersonEnum.ALIASE))  
}  
  
class GeneralPerson(var name:String, var age:Int)  
data class PersonData(val name:String, val age:Int)  
enum class PersonEnum(val names:String, val age:Int) {  
    CURRENT("민규", 33),  
    ALIASE("밍", 9),  
    FULLNAME("이민규", 33);  
    fun isAlias() = this == PersonEnum.ALIASE  
}  
infix fun PersonEnum.ad(other:PersonEnum) {  
    println("${PersonEnum.ALIASE}입니다")  
}
```

- DATA객체는 안에 `equalse()`가 구현되어있다.