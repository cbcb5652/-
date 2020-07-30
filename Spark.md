# Spark

## scala

**val:**声明的变量不可变(推荐)

**var：**声明的变量可以变

```scala
val a:Int = 123
val s: = "scala"
val s:String = "scala"
```

### Scala的值类型有7种(无引用类型)

- Byte
- Char
- Short
- Int
- Long
- Float
- Double

```scala
val y = if(x>1) 1 else -1
## 支持多种类型的返回
val z = if(x>1) 1 else "error"

val a = if(x<1) 1 # 默认返回空
# 返回AnyVal=()

# 循环
scala> 1 to 10
res1: scala.collection.immutable.Range.Inclusive = Range(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
scala> 1 until 10
res2: scala.collection.immutable.Range = Range(1, 2, 3, 4, 5, 6, 7, 8, 9)

#for循环
scala> for(i<- 1 to 10){
     | print(i)
     | }
#输出
12345678910

##循环数组
scala> val arr = Array("java","scala","C")
arr: Array[String] = Array(java, scala, C)

scala> for (i<-arr) print(i)
javascalaC

#嵌套循环
scala> for(i<-1 to 3;j<-1 to 3 if(i!=j)) println(i*10 + j +" ")
12
13
21
23
31
32

# yeild 把每次生成的值，封装到集合中
scala> val res = for (i<-1 until 10) yield i
res: scala.collection.immutable.IndexedSeq[Int] = Vector(1, 2, 3, 4, 5, 6, 7, 8, 9)

# 声明方法
scala> def m1(x:Int,y:Int):Int = x+y
m1: (x: Int, y: Int)Int

scala> print(m1(1,2))
3
-------------
scala> val f1 = (x:Int,y:Int) => x+y
f1: (Int, Int) => Int = <function2>

scala> f1(2,4)
res8: Int = 6

#声明数组
scala> val arr1 = new Array[Int](8)
arr1: Array[Int] = Array(0, 0, 0, 0, 0, 0, 0, 0)

scala> val arr1 = new Array[String](8)
arr1: Array[String] = Array(null, null, null, null, null, null, null, null)

scala> print(arr1.toBuffer)
ArrayBuffer(null, null, null, null, null, null, null, null)
---------------------
scala> val arr2 = Array("java","scala","python")
arr2: Array[String] = Array(java, scala, python)

scala> arr2
res10: Array[String] = Array(java, scala, python)

#变长数组
scala> val arr3 = ArrayBuffer[Int]()
arr3: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer()

scala> arr3 +=1
res11: arr3.type = ArrayBuffer(1)

scala> arr3 += (2,3,4)
res12: arr3.type = ArrayBuffer(1, 2, 3, 4)

scala> arr3 ++= Array(5,6)
res14: arr3.type = ArrayBuffer(1, 2, 3, 4, 5, 6)

scala> arr3 ++= ArrayBuffer(7,8)
res15: arr3.type = ArrayBuffer(1, 2, 3, 4, 5, 6, 7, 8)

# 在第0个位置，加入-1和0
scala> arr3.insert(0,-1,0)

scala> arr3
res17: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(-1, 0, 1, 2, 3, 4, 5, 6, 7, 8)

# 从下标为2的开始删除两个
scala> arr3.remove(2,2)

scala> arr3
res19: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(-1, 0, 3, 4, 5, 6, 7, 8)

# 循环取值
scala> for(i <- 0 until arr3.length) println(i)
0
1
2
3
4
5
6
7

scala> for(i <- 0 until arr3.length) println(arr3(i))
-1
0
3
4
5
6
7
8

#yeild：将值取出来封装到另外一个数组
scala> val res = for(i <- arr3) yield i*10
res: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(-10, 0, 30, 40, 50, 60, 70, 80)

scala> res
res22: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(-10, 0, 30, 40, 50, 60, 70, 80)

# 取最大值，排序
scala> arr3.sum
res24: Int = 32

scala> arr3.max
res25: Int = 8

scala> arr3.sorted
res26: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(-1, 0, 3, 4, 5, 6, 7, 8)
```

**映射**

```scala
#声明
scala> val map1 = Map("scala"->1,"java"->2,"python"->3)
map1: scala.collection.immutable.Map[String,Int] = Map(scala -> 1, java -> 2, python -> 3)

scala> val map2 = Map(("sacla",1),("java",2),("python",3))
map2: scala.collection.immutable.Map[String,Int] = Map(sacla -> 1, java -> 2, python -> 3)

#获取
scala> map1("scala")
res27: Int = 1

#修改：导入包： import scala.collection.mutable._   
scala> val map1 = Map("scala"->1,"java"->2,"python"->3)
map1: scala.collection.mutable.Map[String,Int] = Map(scala -> 1, java -> 2, python -> 3)

scala> map1("scala") = 6

scala> map1("scala")
res31: Int = 6

#查询不到返回默认值
scala> map1.getOrElse("S",-1)
res32: Int = -1

# 元组声明
scala> ("scala",1)
res33: (String, Int) = (scala,1)

scala> val t = ("scala",100L,3.14,("spark",1))
t: (String, Long, Double, (String, Int)) = (scala,100,3.14,(spark,1))
-------------------
scala> val t,(a,b,c,d) = ("scala",100L,3.14,("spark",1))
t: (String, Long, Double, (String, Int)) = (scala,100,3.14,(spark,1))
a: String = scala
b: Long = 100
c: Double = 3.14
d: (String, Int) = (spark,1)

#取元组 ：下标从1开始
scala> t._1
res34: String = scala

scala> t._3
res35: Double = 3.14

scala> t._4._1
res36: String = spark

# 元组转换为数组
scala> val arr = Array(("tingting",34),("ningning",34))
arr: Array[(String, Int)] = Array((tingting,34), (ningning,34))

scala> arr.toMap
res38: scala.collection.immutable.Map[String,Int] = Map(tingting -> 34, ningning -> 34)

# 拉链方法
scala> val arr1 = Array("tingting","ningning")
arr1: Array[String] = Array(tingting, ningning)

scala> val arr2 = Array(24,25,26)
arr2: Array[Int] = Array(24, 25, 26)

scala> arr1 zip arr2
res39: Array[(String, Int)] = Array((tingting,24), (ningning,25))
```

Scala 的集合有三大类:Seq(序列)，Set（集），Map（映射）

//**seq**

```scala
#列表追加，追加到前面
scala> var list1 = List(1,2,3)
list1: List[Int] = List(1, 2, 3)

scala> val list2 = 0 :: list1
list2: List[Int] = List(0, 1, 2, 3)

scala> val list3 = list1.::(0)
list3: List[Int] = List(0, 1, 2, 3)

scala> val list4 = 0 +:list1
list4: List[Int] = List(0, 1, 2, 3)

scala> list1.append(5)

scala> list1
res43: scala.collection.mutable.ListBuffer[Int] = ListBuffer(1, 2, 3, 4, 5)

#追加到后面
scala> val list6 = list1 :+4
list6: List[Int] = List(1, 2, 3, 4)

scala> val list7 = List(5,6,7)
list7: List[Int] = List(5, 6, 7)

scala> val list8 = list1 ++ list7
list8: List[Int] = List(1, 2, 3, 5, 6, 7)

scala> val list9 = list1 ::: list7
list9: List[Int] = List(1, 2, 3, 5, 6, 7)

---------------
#列表追加
scala> list1 ++= list2
res44: list1.type = ListBuffer(1, 2, 3, 4, 5, 0, 1, 2, 3)


```

**Set**

```scala
//基本操作
scala> val set1 = new HashSet[Int]()
set1: scala.collection.mutable.HashSet[Int] = Set()

scala> val set2 = set1 + 1
set2: scala.collection.mutable.HashSet[Int] = Set(1)

scala> set1
res45: scala.collection.mutable.HashSet[Int] = Set()

scala> set2
res46: scala.collection.mutable.HashSet[Int] = Set(1)

scala> val set3 = set2 ++ Set(2,3,4)
set3: scala.collection.mutable.HashSet[Int] = Set(1, 2, 3, 4)

scala> set3
res47: scala.collection.mutable.HashSet[Int] = Set(1, 2, 3, 4)

---追加--
scala> val set1 = new HashSet[Int]()
set1: scala.collection.mutable.HashSet[Int] = Set()

scala> set1 += 1
res48: set1.type = Set(1)

scala> set1 += 2
res49: set1.type = Set(1, 2)

scala> set1.add(3)
res50: Boolean = true

scala> set1
res51: scala.collection.mutable.HashSet[Int] = Set(1, 2, 3)

----去重-----
scala> set1 ++= Set(5,6,7)
res54: set1.type = Set(1, 5, 2, 6, 3, 7)

scala> set1
res55: scala.collection.mutable.HashSet[Int] = Set(1, 5, 2, 6, 3, 7)

-----删除------
scala> set1 -=1
res56: set1.type = Set(5, 2, 6, 3, 7)

scala> set1.remove(2)
res57: Boolean = true
```

**Map**

```scala
------添加----------
scala>  map1
res60: scala.collection.mutable.HashMap[String,Int] = Map(scala -> 1)

scala> map1 += (("java",2))
res61: map1.type = Map(scala -> 1, java -> 2)


scala> map1 +=(("python",3),("C",4))
res62: map1.type = Map(scala -> 1, C -> 4, java -> 2, python -> 3)

scala> map1
res63: scala.collection.mutable.HashMap[String,Int] = Map(scala -> 1, C -> 4, java -> 2, python -> 3)

scala> map1.put("C++",5)
res64: Option[Int] = None

scala> map1
res65: scala.collection.mutable.HashMap[String,Int] = Map(scala -> 1, C -> 4, java -> 2, C++ -> 5, python -> 3)

----删除---------
scala> map1 -= "java"
res66: map1.type = Map(scala -> 1, C -> 4, C++ -> 5, python -> 3)

scala> map1.remove("C")
res67: Option[Int] = Some(4)

scala> map1
res68: scala.collection.mutable.HashMap[String,Int] = Map(scala -> 1, C++ -> 5, python -> 3)
```

==lazy==：定义的变量叫惰性变量，会实现延迟加载，不可变的变量。只有在调用惰性变量才回去实例化这个变量

 ```scala
package day01

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/20 14:22
 * @Version V1.0
 **/
class ScalaLazyDemo {


}
object ScalaLazyDemo1{
  def init():Unit= {
    println("执行了init方法")
  }

  def main(args: Array[String]): Unit = {
     val property = init();//没有用lazy修饰
    println("after init()")
    println(property)
  }
}

object ScalaLazyDemo2{
  def init():Unit= {
    println("执行了init方法")
  }

  def main(args: Array[String]): Unit = {
    lazy val property = init();//没有用lazy修饰
    println("after init()")
    println(property)
  }
}

执行了init方法
after init()
()
---------------
after init()
执行了init方法
()
 ```

隐式转换和隐式参数

- 作用：能够丰富现有的类库的功能，对类的方法进行增强
- 隐式转换函数：以implicit关键字声明并带有单个参数的函数



泛型：

​	[B <: A] UpperBound 上届：B类型的上届是A类型，即B类型的父类是A类型

​	[B >: A] LowerBound 下届：B类型的下届是A类型，即B类型的子类的A类型

​	[B <% A] ViewBound 表示B类型要转换成A类型，需要一个隐式转换行数

​	[B : A] ContextBound 需要一个隐式转换的值

[-A,+B]

​	[-A]逆变。作为参数类型，如果A是T的子类，那么C[T] 是C[A] 的子类

​	[+B]协变,作为返回类型，如果B是T的子类，那么c[B] 是C[T]的子类

- 导入某个目录下所有的包

  **import java.awt._**

==import语句可以出现在任何地方，而不是只能出现在文件顶部==

**Scala数据类型**

| 数据类型 | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| Byte     | 8位有符号补码整数。数值区间为 -128 到 127                    |
| Short    | 16位有符号补码整数。数值区间为 -32768 到 32767               |
| Int      | 32位有符号补码整数。数值区间为 -2147483648 到 2147483647     |
| Long     | 64位有符号补码整数。数值区间为 -9223372036854775808 到 9223372036854775807 |
| Float    | 32 位, IEEE 754 标准的单精度浮点数                           |
| Double   | 64 位 IEEE 754 标准的双精度浮点数                            |
| Char     | 16位无符号Unicode字符, 区间值为 U+0000 到 U+FFFF             |
| String   | 字符序列                                                     |
| Boolean  | true或false                                                  |
| Unit     | 表示无值，和其他语言中void等同。用作不返回任何结果的方法的结果类型。Unit只有一个实例值，写成()。 |
| Null     | null 或空引用                                                |
| Nothing  | Nothing类型在Scala的类层级的最底端；它是任何其他类型的子类型。 |
| Any      | Any是所有其他类的超类                                        |
| AnyRef   | AnyRef类是Scala里所有引用类(reference class)的基类           |

```scala
package bobsrockets{
    package navigation{
        private[bobsrockets] class Navigator{
         protected[navigation] def useStarChart(){}
         class LegOfJourney{
             private[Navigator] val distance = 100
             }
            private[this] var speed = 200
            }
        }
        package launch{
        import navigation._
        object Vehicle{
        private[launch] val guide = new Navigator
        }
    }
}
```

> 上述例子中，类Navigator被标记为private[bobsrockets]就是说这个类对包含在bobsrockets包里的所有的类和对象可见。
>
> 比如说，从Vehicle对象里对Navigator的访问是被允许的，因为对象Vehicle包含在包launch中，而launch包在bobsrockets中，相反，所有在包bobsrockets之外的代码都不能访问类Navigator。

### Scala方法与函数

Scala有方法和函数，二者在语义上的区别很小。Scala方法是类的一部分，而函数是一个对象可以赋值给一个变量。换句话来说在类中定义的函数即是方法。

- Scala中的方法跟Java的类似，方法是组成类的一部分
- Scala中的函数则是一个完整的对象，Scala中的函数其实就是继承了Trait的类的对象
- Scala中使用val语句可以定义函数，def语句定义方法

```scala
object method{
  def main(args: Array[String]): Unit = {
    def m(x:Int) = x+3
    val f = (x:Int) => x+3

    println(m(2))
    println(f(3))
  }
}
```

定义一个具体一点的方法

> 模板

```scala
def functionName ([参数列表]) : [return type] = {
   function body
   return [expr]
}
```

**具体方法**

```scala
 def functionName1(name:String ,age:Int):String={
      return name+age
    }
println(functionName1("lvrr",23))
```

```scala
//定义一个相加的方法
    def addInt(a:Int,b:Int):Int={
      var sum :Int = 0
      sum = a+b
      return sum
    }
```

如果没有返回值需要使用Unit修饰

```
//定义一个没有返回值的相当于void 用Unit修饰
def printMe():Unit={
  println("hello Scala!")
}
```

函数传函数调用

```scala
object callByName{
  def main(args: Array[String]): Unit = {
    delayed(time())
  }
  def time() ={
    println("获取时间，单位为纳秒")
    System.nanoTime()
  }

  def delayed(t: =>Long) = {
    println("在delayed方法内")
    println("参数："+t)
    t
  }
}
```

执行结果

```java
在delayed方法内
获取时间，单位为纳秒
参数：84272855006000
获取时间，单位为纳秒
```

> ==指定函数的参数名:==一般情况下函数调用参数，就按照函数定义时的参数顺序一个个传递。但是我们也可以通过指定函数参数名，并且不需要按照顺序向函数传递参数

```scala
/**
 * 指定参数名，不需要看传入参数的顺序
 */
object pointMthodName {
  def main(args: Array[String]): Unit = {
    printInt(b=7,a=2)
  }

  def printInt(a:Int,b:Int)={
    println("value of a:"+a)
    println("value of b:"+b)
  }
}
```

> 可变参数函数

- Scala 允许你指明函数的最后一个参数可以是重复的，即我们不需要指定函数参数的个数，可以向函数传入可变长度参数列表。
- Scala 通过在参数的类型之后放一个星号来设置可变参数(可重复的参数)。

```scala
object 可变参数 {
  def main(args: Array[String]): Unit = {
    printStrings("hello","chenbin","lvrr")
  }

  def printStrings(args:String*): Unit ={
    var i:Int = 0;
    for (arg <- args) {
      println("Args value["+i+"]="+arg)
      i = i+1
    }
  }
}
```

> 函数的递归实现累加，累乘

```scala
object 递归函数 {
  def main(args: Array[String]): Unit = {
      println("10的阶乘为:="+factorial(10))
      println("1-10的累加为:"+addfactor(10))
  }

  def factorial(n:BigInt):BigInt={
    if (n<=1)
      1
    else
      n *factorial(n-1)
  }

  def addfactor(n:Int):Int={
    if(n<=1)
      1
    else
      n + addfactor(n-1)
  }
}
```

> 默认参数函数

Scala 可以为函数参数指定默认参数值，使用了默认参数，你在调用函数的过程中可以不需要传递参数，这时函数就会调用它的默认参数值，如果传递了参数，则传递值会取代默认值。

```scala
object 默认参数 {
  def main(args: Array[String]): Unit = {
      println("使用默认参数:"+addInt())
      println("返回值:"+addInt(10,10))
  }

  def addInt(a:Int = 5,b:Int =6):Int={
    var sum:Int = 0
    sum = a+b
    sum
  }
}
```

输出结果:

```java
使用默认参数:11
返回值:20
```

> 高阶函数:操作其他函数的函数

Scala 中允许使用高阶函数, 高阶函数可以使用其他函数作为参数，或者使用函数作为输出结果。

```scala
object 高阶函数 {
  def main(args: Array[String]): Unit = {
    println(apply( layout,10))
  }

  def apply(f: Int => String ,v:Int) = f(v)

  def layout[A](x:A) = "[" + x.toString + "]"
}
```

在apply中定义了一个函数，输入为Int => 返回值类型为String。

并在内部调用了这个函数，将Int类型传入，输出一个String类型/

> 嵌套调用：定义在函数内的函数称之为局部函数

实现阶乘运算，并使用内嵌函数

```scala
object 函数嵌套 {
  def main(args: Array[String]): Unit = {

    println(factorial(0))
    println(factorial(1))
    println(factorial(2))
    println(factorial(3))
  }

  def factorial(i:Int):Int = {
    def fact(i:Int,accumulator :Int):Int = {
      if(i<=1)
        accumulator
      else
        fact(i-1,i*accumulator)
    }
    fact(i,1)
  }
}
```

> 匿名函数:箭头左边是参数列表，右边是函数体

- 使用匿名函数的语法简单，箭头左边是参数列表，右边是函数体

```scala
object 匿名函数 {

  //定义一个简单的匿名函数
  var inc = (x:Int) => x+1

  //这个匿名函数和上面那个一样
  def add2 = new Function[Int,Int]{
    def apply(x:Int):Int = x+1
  }

  //在匿名函数中可以传递多个参数
  var mul = (x:Int,y:Int) =>{
    x*y
  }

  //不给匿名函数设置参数
  var userDir = () =>{
    System.getProperty("USER.DIR")
  }

  //实例
  var factor = 3
  val multiplier = (i:Int) => i * factor

  def main(args: Array[String]): Unit = {
    println(add2(2))
    //调用多个参数的匿名函数
    println(mul(2, 5))
    //调用没有参数的匿名函数
    println(userDir)
    //调用实例
    println("multiplier(1) value ="+multiplier(2))
  }
}
```

> 偏应用函数: 一种表达式，不需要提供函数的所有参数，只需要提供一部分，或不提供函数

```scala
object 偏应用函数 {
  def main(args: Array[String]): Unit = {
    val date = new Date
    log(date,"message1")
    Thread.sleep(1000)
    log(date,"message2")
    Thread.sleep(1000)
    log(date,"message3")
    println("-------------以下是偏应用函数的使用----------------------")
    val date1 = new Date
    //首先指定一个参数，另一个参数在下方调用
    val logWithDateBound = log(date,_:String)
    logWithDateBound.apply("message1")
    Thread.sleep(1000)
    logWithDateBound.apply("message2")
    Thread.sleep(1000)
    logWithDateBound.apply("message3")

  }

  def log(date:Date,message:String): Unit ={
    println(date+"----------"+message)
  }

}
```

log()方法接收两个参数：date和message。我们的程序执行时调用了三次，参数date值都相同，message不同。

所以使用偏应用函数优化了以上的方法，绑定了第一个date参数

> 函数柯里化

柯里化(Currying)指的是将原来接受两个参数的函数变成新的接受一个参数的函数的过程。新的函数返回一个以原有第二个参数为参数的函数。

```scala
def add(x:Int,y:Int)=x+y

def add(x:Int)(y:Int) = x + y
```

实例:

```scala
object 函数柯里化 {

//  def add(x:Int,y:Int) = x+y
//
//  def add(x:Int)(y:Int) = x+y   //柯里化
//  def add(x:Int)=(y:Int) => x+y //柯里化过程如  实现过程

  def strcat(s1:String)(s2:String) = {
    s1 + s2
  }
  def main(args: Array[String]): Unit = {
    val str1:String = "Hello"
    val str2:String = "Scala!"
    println("str1 + str2 = "+strcat(str1)(str2))
  }
}
```

### Scala闭包

> 闭包是一个函数，返回值依赖于声明在函数外部的一个或多个变量
>
> 闭包通常来讲可以简单的认为是可以访问一个函数里面局部变量的另外一个函数

```scala
val multipiler = (i:Int) => i*10
```

闭包形式，接收外部

```scala
var factor = 3
val multipiler = (i:Int) => i*factor
```

```scala
object 闭包 {
  //匿名函数
  val multiplier = (i:Int) => i*10

  var factor = 3
  val multiplier1 = (i:Int) => i* factor

  def main(args: Array[String]): Unit = {
    println("muliplier(1) value = "+multiplier1(1))
    println("muliplier(2) value ="+multiplier1(2))
  }

```

### Scala字符串

**字符串声明：**

```scala
var greeting = "Hello world"     //直接声明 系统自动匹配类型

var greeting:String = "Hello World"   //指定类型
```

因为String是常量类型不可变，每次改变值的时候会抛弃原来的字符串，新建一个我们可以直接使用StringBuilder来添加

StringBuilfer 添加字符用+=    添加字符串用++=

```scala
object Test {
  def main(args: Array[String]): Unit = {
    val buf = new StringBuilder;
    buf += 'a'
    buf ++= "bcdef"
    println( "buf is : " + buf.toString );
  }
}
```

**String 方法**

| 序号 | 方法及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | **char charAt(int index)**返回指定位置的字符                 |
| 2    | **int compareTo(Object o)**比较字符串与对象                  |
| 3    | **int compareTo(String anotherString)**按字典顺序比较两个字符串 |
| 4    | **int compareToIgnoreCase(String str)**按字典顺序比较两个字符串，不考虑大小写 |
| 5    | **String concat(String str)**将指定字符串连接到此字符串的结尾 |
| 6    | **boolean contentEquals(StringBuffer sb)**将此字符串与指定的 StringBuffer 比较。 |
| 7    | **static String copyValueOf(char[] data)**返回指定数组中表示该字符序列的 String |
| 8    | **static String copyValueOf(char[] data, int offset, int count)**返回指定数组中表示该字符序列的 String |
| 9    | **boolean endsWith(String suffix)**测试此字符串是否以指定的后缀结束 |
| 10   | **boolean equals(Object anObject)**将此字符串与指定的对象比较 |
| 11   | **boolean equalsIgnoreCase(String anotherString)**将此 String 与另一个 String 比较，不考虑大小写 |
| 12   | **byte getBytes()**使用平台的默认字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中 |
| 13   | **byte[] getBytes(String charsetName**使用指定的字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中 |
| 14   | **void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)**将字符从此字符串复制到目标字符数组 |
| 15   | **int hashCode()**返回此字符串的哈希码                       |
| 16   | **int indexOf(int ch)**返回指定字符在此字符串中第一次出现处的索引 |
| 17   | **int indexOf(int ch, int fromIndex)**返回在此字符串中第一次出现指定字符处的索引，从指定的索引开始搜索 |
| 18   | **int indexOf(String str)**返回指定子字符串在此字符串中第一次出现处的索引 |
| 19   | **int indexOf(String str, int fromIndex)**返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始 |
| 20   | **String intern()**返回字符串对象的规范化表示形式            |
| 21   | **int lastIndexOf(int ch)**返回指定字符在此字符串中最后一次出现处的索引 |
| 22   | **int lastIndexOf(int ch, int fromIndex)**返回指定字符在此字符串中最后一次出现处的索引，从指定的索引处开始进行反向搜索 |
| 23   | **int lastIndexOf(String str)**返回指定子字符串在此字符串中最右边出现处的索引 |
| 24   | **int lastIndexOf(String str, int fromIndex)**返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索 |
| 25   | **int length()**返回此字符串的长度                           |
| 26   | **boolean matches(String regex)**告知此字符串是否匹配给定的正则表达式 |
| 27   | **boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)**测试两个字符串区域是否相等 |
| 28   | **boolean regionMatches(int toffset, String other, int ooffset, int len)**测试两个字符串区域是否相等 |
| 29   | **String replace(char oldChar, char newChar)**返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的 |
| 30   | **String replaceAll(String regex, String replacement**使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串 |
| 31   | **String replaceFirst(String regex, String replacement)**使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串 |
| 32   | **String[] split(String regex)**根据给定正则表达式的匹配拆分此字符串 |
| 33   | **String[] split(String regex, int limit)**根据匹配给定的正则表达式来拆分此字符串 |
| 34   | **boolean startsWith(String prefix)**测试此字符串是否以指定的前缀开始 |
| 35   | **boolean startsWith(String prefix, int toffset)**测试此字符串从指定索引开始的子字符串是否以指定前缀开始。 |
| 36   | **CharSequence subSequence(int beginIndex, int endIndex)**返回一个新的字符序列，它是此序列的一个子序列 |
| 37   | **String substring(int beginIndex)**返回一个新的字符串，它是此字符串的一个子字符串 |
| 38   | **String substring(int beginIndex, int endIndex)**返回一个新字符串，它是此字符串的一个子字符串 |
| 39   | **char[] toCharArray()**将此字符串转换为一个新的字符数组     |
| 40   | **String toLowerCase()**使用默认语言环境的规则将此 String 中的所有字符都转换为小写 |
| 41   | **String toLowerCase(Locale locale)**使用给定 Locale 的规则将此 String 中的所有字符都转换为小写 |
| 42   | **String toString()**返回此对象本身（它已经是一个字符串！）  |
| 43   | **String toUpperCase()**使用默认语言环境的规则将此 String 中的所有字符都转换为大写 |
| 44   | **String toUpperCase(Locale locale)**使用给定 Locale 的规则将此 String 中的所有字符都转换为大写 |
| 45   | **String trim()**删除指定字符串的首尾空白符                  |
| 46   | **static String valueOf(primitive data type x)**返回指定类型参数的字符串表示形式 |

### Scala数组

==需要导入这个包：import Array._==

**声明数组**

```scala
var z:Array[String] = new Array[String](3)

或

var z = new Array[String](3)
```

**多维数组**

多维数组一个数组中的值可以是另一个数组，另一个数组的值也可以是一个数组。矩阵与表格是我们常见的二维数组。

```scala
var myMatrix = ofDim[Int](3,3)
```

**合并数组:(concat())**

**创建区间数组**

我们使用了 range() 方法来生成一个区间范围内的数组。range() 方法最后一个参数为步长，默认为 1

```scala
object 数组 {
  //第一张定义数组
  var z:Array[String] = new Array[String](3)
  //第二种定义数组
  var zh = new Array[String](3)

  z(0) = "Runoob"
  z(1) = "Baidu"
  z(4/2) = "Google"
  //第三种定义数组
  var zz = Array("Runoob", "Baidu", "Google")

  //处理数组
  def main(args: Array[String]): Unit = {
    var myList = Array(1.9,2.9,3.4,3.5)
    //输出所有元素
    for (x <- myList){
      println(x)
    }

    //计算数组所有元素的总和
    var total = 0.0
    for(i<-0 to (myList.length-1)){
      total += myList(i)
    }
    println("总和：" +total)

    //查找数组中最大的元素
    var max = myList(0)
    for (i<-1 to (myList.length-1)){
      if (myList(i) > max){
        max = myList(i)
      }
    }
    println("最大值为 ："+max)

    //多维数组
    var myMatrix = ofDim[Int](3,3)
    // 创建矩阵
    for (i <- 0 to 2) {
      for ( j <- 0 to 2) {
        myMatrix(i)(j) = j;
      }
    }

    // 打印二维阵列
    for (i <- 0 to 2) {
      for ( j <- 0 to 2) {
        print(" " + myMatrix(i)(j));
      }
      println();
    }

    //合并数组
    var myList1 = Array(1.9, 2.9, 3.4, 3.5)
    var myList2 = Array(8.9, 7.9, 0.4, 1.5)
    //合并两个数组
    var myList3 =  concat( myList1, myList2)

    //输出所有元素
    for (x <- myList3){
      println(x)
    }

    //创建区间数组,最后的2为步长
    var mList1 = range(10, 20, 2)
    var mList2 = range(10,20)

    //输出所有数组元素

    println("区间数组开始====")
    for (x <- mList1){
      print(" "+x)
    }
    println()
    for (x <- mList2){
      print(" "+x)
    }
  }
}
```

**Scala数组方法**

下表中为 Scala 语言中处理数组的重要方法，使用它前我们需要使用 **import Array._** 引入包。

| 序号 | 方法和描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | **def apply( x: T, xs: T\* ): Array[T]**创建指定对象 T 的数组, T 的值可以是 Unit, Double, Float, Long, Int, Char, Short, Byte, Boolean。 |
| 2    | **def concat[T]( xss: Array[T]\* ): Array[T]**合并数组       |
| 3    | **def copy( src: AnyRef, srcPos: Int, dest: AnyRef, destPos: Int, length: Int ): Unit**复制一个数组到另一个数组上。相等于 Java's System.arraycopy(src, srcPos, dest, destPos, length)。 |
| 4    | **def empty[T]: Array[T]**返回长度为 0 的数组                |
| 5    | **def iterate[T]( start: T, len: Int )( f: (T) => T ): Array[T]**返回指定长度数组，每个数组元素为指定函数的返回值。以上实例数组初始值为 0，长度为 3，计算函数为**a=>a+1**：`scala> Array.iterate(0,3)(a=>a+1) res1: Array[Int] = Array(0, 1, 2)` |
| 6    | **def fill[T]( n: Int )(elem: => T): Array[T]**返回数组，长度为第一个参数指定，同时每个元素使用第二个参数进行填充。 |
| 7    | **def fill[T]( n1: Int, n2: Int )( elem: => T ): Array[Array[T]]**返回二数组，长度为第一个参数指定，同时每个元素使用第二个参数进行填充。 |
| 8    | **def ofDim[T]( n1: Int ): Array[T]**创建指定长度的数组      |
| 9    | **def ofDim[T]( n1: Int, n2: Int ): Array[Array[T]]**创建二维数组 |
| 10   | **def ofDim[T]( n1: Int, n2: Int, n3: Int ): Array[Array[Array[T]]]**创建三维数组 |
| 11   | **def range( start: Int, end: Int, step: Int ): Array[Int]**创建指定区间内的数组，step 为每个元素间的步长 |
| 12   | **def range( start: Int, end: Int ): Array[Int]**创建指定区间内的数组 |
| 13   | **def tabulate[T]( n: Int )(f: (Int)=> T): Array[T]**返回指定长度数组，每个数组元素为指定函数的返回值，默认从 0 开始。以上实例返回 3 个元素：`scala> Array.tabulate(3)(a => a + 5) res0: Array[Int] = Array(5, 6, 7)` |
| 14   | **def tabulate[T]( n1: Int, n2: Int )( f: (Int, Int ) => T): Array[Array[T]]**返回指定长度的二维数组，每个数组元素为指定函数的返回值，默认从 0 开始。 |

### Scala集合

#### **列表(List)**

> 声明列表的形式

```scala
//第一种声明方式
// 字符串列表
val site: List[String] = List("Runoob", "Google", "Baidu")

// 整型列表
val nums: List[Int] = List(1, 2, 3, 4)

// 空列表
val empty: List[Nothing] = List()

// 二维列表
val dim: List[List[Int]] =
   List(
      List(1, 0, 0),
      List(0, 1, 0),
      List(0, 0, 1)
   )

//第二种声明方式
// 字符串列表
val site = "Runoob" :: ("Google" :: ("Baidu" :: Nil))

// 整型列表
val nums = 1 :: (2 :: (3 :: (4 :: Nil)))

// 空列表
val empty = Nil

// 二维列表
val dim = (1 :: (0 :: (0 :: Nil))) ::
          (0 :: (1 :: (0 :: Nil))) ::
          (0 :: (0 :: (1 :: Nil))) :: Nil
```

**列表的基本操作**

- head:返回列表的第一个元素
- tail：返回一个列表，包含除了第一个元素之外的其他元素
- isEmpty:在列表为空时返回true

**连接列表三种方法**

```scala
 val site1 = "Runoob" :: ("Google" :: ("Baidu" :: Nil))
      val site2 = "Facebook" :: ("Taobao" :: Nil)

      // 使用 ::: 运算符
      var fruit = site1 ::: site2
      println( "site1 ::: site2 : " + fruit )
      
      // 使用 List.:::() 方法
      fruit = site1.:::(site2)
      println( "site1.:::(site2) : " + fruit )

      // 使用 concat 方法
      fruit = List.concat(site1, site2)
      println( "List.concat(site1, site2) : " + fruit  )
```

**List.fill()方法来创建一个指定的重复元素的元素列表**

```scala
val site = List.fill(3)("Runoob") // 重复 Runoob 3次
    println( "site : " + site  )

    val num = List.fill(10)(2)         // 重复元素 2, 10 次
    println( "num : " + num  )
```

​	**List.tabulate()创建多维列表**

```scala
// 通过给定的函数创建 5 个元素
      val squares = List.tabulate(6)(n => n * n)
      println( "一维 : " + squares  )

      // 创建二维列表
      val mul = List.tabulate( 4,5 )( _ * _ )      
      println( "多维 : " + mul  )
```

| 序号 | 方法及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | **def +:(elem: A): List[A]**为列表预添加元素`scala> val x = List(1) x: List[Int] = List(1) scala> val y = 2 +: x y: List[Int] = List(2, 1) scala> println(x) List(1)` |
| 2    | **def ::(x: A): List[A]**在列表开头添加元素                  |
| 3    | **def :::(prefix: List[A]): List[A]**在列表开头添加指定列表的元素 |
| 4    | **def :+(elem: A): List[A]**复制添加元素后列表。`scala> val a = List(1) a: List[Int] = List(1) scala> val b = a :+ 2 b: List[Int] = List(1, 2) scala> println(a) List(1)` |
| 5    | **def addString(b: StringBuilder): StringBuilder**将列表的所有元素添加到 StringBuilder |
| 6    | **def addString(b: StringBuilder, sep: String): StringBuilder**将列表的所有元素添加到 StringBuilder，并指定分隔符 |
| 7    | **def apply(n: Int): A**通过列表索引获取元素                 |
| 8    | **def contains(elem: Any): Boolean**检测列表中是否包含指定的元素 |
| 9    | **def copyToArray(xs: Array[A], start: Int, len: Int): Unit**将列表的元素复制到数组中。 |
| 10   | **def distinct: List[A]**去除列表的重复元素，并返回新列表    |
| 11   | **def drop(n: Int): List[A]**丢弃前n个元素，并返回新列表     |
| 12   | **def dropRight(n: Int): List[A]**丢弃最后n个元素，并返回新列表 |
| 13   | **def dropWhile(p: (A) => Boolean): List[A]**从左向右丢弃元素，直到条件p不成立 |
| 14   | **def endsWith[B](that: Seq[B]): Boolean**检测列表是否以指定序列结尾 |
| 15   | **def equals(that: Any): Boolean**判断是否相等               |
| 16   | **def exists(p: (A) => Boolean): Boolean**判断列表中指定条件的元素是否存在。判断l是否存在某个元素:`scala> l.exists(s => s == "Hah") res7: Boolean = true` |
| 17   | **def filter(p: (A) => Boolean): List[A]**输出符号指定条件的所有元素。过滤出长度为3的元素:`scala> l.filter(s => s.length == 3) res8: List[String] = List(Hah, WOW)` |
| 18   | **def forall(p: (A) => Boolean): Boolean**检测所有元素。例如：判断所有元素是否以"H"开头：scala> l.forall(s => s.startsWith("H")) res10: Boolean = false |
| 19   | **def foreach(f: (A) => Unit): Unit**将函数应用到列表的所有元素 |
| 20   | **def head: A**获取列表的第一个元素                          |
| 21   | **def indexOf(elem: A, from: Int): Int**从指定位置 from 开始查找元素第一次出现的位置 |
| 22   | **def init: List[A]**返回所有元素，除了最后一个              |
| 23   | **def intersect(that: Seq[A]): List[A]**计算多个集合的交集   |
| 24   | **def isEmpty: Boolean**检测列表是否为空                     |
| 25   | **def iterator: Iterator[A]**创建一个新的迭代器来迭代元素    |
| 26   | **def last: A**返回最后一个元素                              |
| 27   | **def lastIndexOf(elem: A, end: Int): Int**在指定的位置 end 开始查找元素最后出现的位置 |
| 28   | **def length: Int**返回列表长度                              |
| 29   | **def map[B](f: (A) => B): List[B]**通过给定的方法将所有元素重新计算 |
| 30   | **def max: A**查找最大元素                                   |
| 31   | **def min: A**查找最小元素                                   |
| 32   | **def mkString: String**列表所有元素作为字符串显示           |
| 33   | **def mkString(sep: String): String**使用分隔符将列表所有元素作为字符串显示 |
| 34   | **def reverse: List[A]**列表反转                             |
| 35   | **def sorted[B >: A]: List[A]**列表排序                      |
| 36   | **def startsWith[B](that: Seq[B], offset: Int): Boolean**检测列表在指定位置是否包含指定序列 |
| 37   | **def sum: A**计算集合元素之和                               |
| 38   | **def tail: List[A]**返回所有元素，除了第一个                |
| 39   | **def take(n: Int): List[A]**提取列表的前n个元素             |
| 40   | **def takeRight(n: Int): List[A]**提取列表的后n个元素        |
| 41   | **def toArray: Array[A]**列表转换为数组                      |
| 42   | **def toBuffer[B >: A]: Buffer[B]**返回缓冲区，包含了列表的所有元素 |
| 43   | **def toMap[T, U]: Map[T, U]**List 转换为 Map                |
| 44   | **def toSeq: Seq[A]**List 转换为 Seq                         |
| 45   | **def toSet[B >: A]: Set[B]**List 转换为 Set                 |
| 46   | **def toString(): String**列表转换为字符串                   |

#### 集合(Set)

> 基本操作

- head返回集合的第一个元素
- tail返回一个集合，包含除第一元素之外的其他元素
- isEmpty 在集合为空时返回true

```scala
val site = Set("Runoob", "Google", "Baidu")
      val nums: Set[Int] = Set()

      println( "第一网站是 : " + site.head )
      println( "最后一个网站是 : " + site.tail )
      println( "查看列表 site 是否为空 : " + site.isEmpty )
      println( "查看 nums 是否为空 : " + nums.isEmpty )
```

```java
$ vim Test.scala 
$ scala Test.scala 
第一网站是 : Runoob
最后一个网站是 : Set(Google, Baidu)
查看列表 site 是否为空 : false
查看 nums 是否为空 : true
```

**连接集合**

你可以使用 **++** 运算符或 **Set.++()** 方法来连接两个集合。如果元素有重复的就会移除重复的元素

```scala
val site1 = Set("Runoob", "Google", "Baidu")
      val site2 = Set("Faceboook", "Taobao")

      // ++ 作为运算符使用
      var site = site1 ++ site2
      println( "site1 ++ site2 : " + site )

      //  ++ 作为方法使用
      site = site1.++(site2)
      println( "site1.++(site2) : " + site )
```

```
$ vim Test.scala 
$ scala Test.scala 
site1 ++ site2 : Set(Faceboook, Taobao, Google, Baidu, Runoob)
site1.++(site2) : Set(Faceboook, Taobao, Google, Baidu, Runoob)
```



**交集**

你可以使用 **Set.&** 方法或 **Set.intersect** 方法来查看两个集合的交集元素。

```scala
val num1 = Set(5,6,9,20,30,45)
      val num2 = Set(50,60,9,20,35,55)

      // 交集
      println( "num1.&(num2) : " + num1.&(num2) )
      println( "num1.intersect(num2) : " + num1.intersect(num2) )
```

```java
$ vim Test.scala 
$ scala Test.scala 
num1.&(num2) : Set(20, 9)
num1.intersect(num2) : Set(20, 9)
```

==Scala Set常用的方法==

| 序号 | 方法及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | **def +(elem: A): Set[A]**为集合添加新元素，x并创建一个新的集合，除非元素已存在 |
| 2    | **def -(elem: A): Set[A]**移除集合中的元素，并创建一个新的集合 |
| 3    | **def contains(elem: A): Boolean**如果元素在集合中存在，返回 true，否则返回 false。 |
| 4    | **def &(that: Set[A]): Set[A]**返回两个集合的交集            |
| 5    | **def &~(that: Set[A]): Set[A]**返回两个集合的差集           |
| 6    | **def +(elem1: A, elem2: A, elems: A\*): Set[A]**通过添加传入指定集合的元素创建一个新的不可变集合 |
| 7    | **def ++(elems: A): Set[A]**合并两个集合                     |
| 8    | **def -(elem1: A, elem2: A, elems: A\*): Set[A]**通过移除传入指定集合的元素创建一个新的不可变集合 |
| 9    | **def addString(b: StringBuilder): StringBuilder**将不可变集合的所有元素添加到字符串缓冲区 |
| 10   | **def addString(b: StringBuilder, sep: String): StringBuilder**将不可变集合的所有元素添加到字符串缓冲区，并使用指定的分隔符 |
| 11   | **def apply(elem: A)**检测集合中是否包含指定元素             |
| 12   | **def count(p: (A) => Boolean): Int**计算满足指定条件的集合元素个数 |
| 13   | **def copyToArray(xs: Array[A], start: Int, len: Int): Unit**复制不可变集合元素到数组 |
| 14   | **def diff(that: Set[A]): Set[A]**比较两个集合的差集         |
| 15   | **def drop(n: Int): Set[A]]**返回丢弃前n个元素新集合         |
| 16   | **def dropRight(n: Int): Set[A]**返回丢弃最后n个元素新集合   |
| 17   | **def dropWhile(p: (A) => Boolean): Set[A]**从左向右丢弃元素，直到条件p不成立 |
| 18   | **def equals(that: Any): Boolean**equals 方法可用于任意序列。用于比较系列是否相等。 |
| 19   | **def exists(p: (A) => Boolean): Boolean**判断不可变集合中指定条件的元素是否存在。 |
| 20   | **def filter(p: (A) => Boolean): Set[A]**输出符合指定条件的所有不可变集合元素。 |
| 21   | **def find(p: (A) => Boolean): Option[A]**查找不可变集合中满足指定条件的第一个元素 |
| 22   | **def forall(p: (A) => Boolean): Boolean**查找指定条件是否适用于此集合的所有元素 |
| 23   | **def foreach(f: (A) => Unit): Unit**将函数应用到不可变集合的所有元素 |
| 24   | **def head: A**获取不可变集合的第一个元素                    |
| 25   | **def init: Set[A]**返回所有元素，除了最后一个               |
| 26   | **def intersect(that: Set[A]): Set[A]**计算两个集合的交集    |
| 27   | **def isEmpty: Boolean**判断集合是否为空                     |
| 28   | **def iterator: Iterator[A]**创建一个新的迭代器来迭代元素    |
| 29   | **def last: A**返回最后一个元素                              |
| 30   | **def map[B](f: (A) => B): immutable.Set[B]**通过给定的方法将所有元素重新计算 |
| 31   | **def max: A**查找最大元素                                   |
| 32   | **def min: A**查找最小元素                                   |
| 33   | **def mkString: String**集合所有元素作为字符串显示           |
| 34   | **def mkString(sep: String): String**使用分隔符将集合所有元素作为字符串显示 |
| 35   | **def product: A**返回不可变集合中数字元素的积。             |
| 36   | **def size: Int**返回不可变集合元素的数量                    |
| 37   | **def splitAt(n: Int): (Set[A], Set[A])**把不可变集合拆分为两个容器，第一个由前 n 个元素组成，第二个由剩下的元素组成 |
| 38   | **def subsetOf(that: Set[A]): Boolean**如果集合中含有子集返回 true，否则返回false |
| 39   | **def sum: A**返回不可变集合中所有数字元素之和               |
| 40   | **def tail: Set[A]**返回一个不可变集合中除了第一元素之外的其他元素 |
| 41   | **def take(n: Int): Set[A]**返回前 n 个元素                  |
| 42   | **def takeRight(n: Int):Set[A]**返回后 n 个元素              |
| 43   | **def toArray: Array[A]**将集合转换为数组                    |
| 44   | **def toBuffer[B >: A]: Buffer[B]**返回缓冲区，包含了不可变集合的所有元素 |
| 45   | **def toList: List[A]**返回 List，包含了不可变集合的所有元素 |
| 46   | **def toMap[T, U]: Map[T, U]**返回 Map，包含了不可变集合的所有元素 |
| 47   | **def toSeq: Seq[A]**返回 Seq，包含了不可变集合的所有元素    |
| 48   | **def toString(): String**返回一个字符串，以对象来表示       |

#### 映射(Map)

声明Map

```scala
// 空哈希表，键为字符串，值为整型
var A:Map[Char,Int] = Map()

// Map 键值对演示
val colors = Map("red" -> "#FF0000", "azure" -> "#F0FFFF")
```

使用++号来添加对应的元素

```scala
A += ('I' -> 1)
A += ('J' -> 5)
A += ('K' -> 10)
A += ('L' -> 100)
```

**Map的基本操作**

- keys : 返回Map所有的键(key)
- values:返回Map所有的值 (value)
- isEmpty: 在Map为空时返回true

演示:

```scala
val colors = Map("red" -> "#FF0000",
                       "azure" -> "#F0FFFF",
                       "peru" -> "#CD853F")

      val nums: Map[Int, Int] = Map()

      println( "colors 中的键为 : " + colors.keys )
      println( "colors 中的值为 : " + colors.values )
      println( "检测 colors 是否为空 : " + colors.isEmpty )
      println( "检测 nums 是否为空 : " + nums.isEmpty )
```

**合并**

```scala
val colors1 = Map("red" -> "#FF0000",
                        "azure" -> "#F0FFFF",
                        "peru" -> "#CD853F")
      val colors2 = Map("blue" -> "#0033FF",
                        "yellow" -> "#FFFF00",
                        "red" -> "#FF0000")

      //  ++ 作为运算符
      var colors = colors1 ++ colors2
      println( "colors1 ++ colors2 : " + colors )

      //  ++ 作为方法
      colors = colors1.++(colors2)
      println( "colors1.++(colors2) : " + colors )
```

**输出Map的keys和values**

```scala
val sites = Map("runoob" -> "http://www.runoob.com",
                       "baidu" -> "http://www.baidu.com",
                       "taobao" -> "http://www.taobao.com")

      sites.keys.foreach{ i =>  
                           print( "Key = " + i )
                           println(" Value = " + sites(i) )}
```

输出

```java
Key = runoob Value = http://www.runoob.com
Key = baidu Value = http://www.baidu.com
Key = taobao Value = http://www.taobao.com
```

#### 元组

与列表一样，元组也是不可变的，但与列表不同的是元组可以包含不同类型的元素

```scala
val t = (1, 3.14, "Fred")  
```

我们可以使用 t._1 访问第一个元素， t._2 访问第二个元素，如下所示：

```scala
 val t = (4,3,2,1)

      val sum = t._1 + t._2 + t._3 + t._4

      println( "元素之和为: "  + sum )
```

**迭代元素**

```scala
val t = (4,3,2,1)
      
t.productIterator.foreach{ i =>println("Value = " + i )}
```

调用tostring方法将元组转换为字符串

```scala
val t = new Tuple3(1, "hello", Console)
      
println("连接后的字符串为: " + t.toString() )
```

**元素交换**

你可以使用 **Tuple.swap** 方法来交换元组的元素。

```scala
val t = new Tuple2("www.google.com", "www.runoob.com")
      
println("交换后的元组: " + t.swap )
```

#### 选项(Option)

- Scala Option(选项)类型用来表示一个值是可选的（有值或无值)。
- Option[T] 是一个类型为 T 的可选值的容器： 如果值存在， Option[T] 就是一个 Some[T] ，如果不存在， Option[T] 就是对象 None 。

```scala
// 虽然 Scala 可以不定义变量的类型，不过为了清楚些，我还是
// 把他显示的定义上了
 
val myMap: Map[String, String] = Map("key1" -> "value")
val value1: Option[String] = myMap.get("key1")
val value2: Option[String] = myMap.get("key2")
 
println(value1) // Some("value1")
println(value2) // None
```

实例:

```scala
 val sites = Map("runoob" -> "www.runoob.com", "google" -> "www.google.com")
      
      println("sites.get( \"runoob\" ) : " +  sites.get( "runoob" )) // Some(www.runoob.com)
      println("sites.get( \"baidu\" ) : " +  sites.get( "baidu" ))  //  None
```

输出结果:

```java
sites.get( "runoob" ) : Some(www.runoob.com)
sites.get( "baidu" ) : None
```

也可以通过==模式匹配==来输出匹配值。实例如下:

```scala
object Test {
   def main(args: Array[String]) {
      val sites = Map("runoob" -> "www.runoob.com", "google" -> "www.google.com")
      
      println("show(sites.get( \"runoob\")) : " +  
                                          show(sites.get( "runoob")) )
      println("show(sites.get( \"baidu\")) : " +  
                                          show(sites.get( "baidu")) )
   }
   
   def show(x: Option[String]) = x match {
      case Some(s) => s
      case None => "?"
   }
}
```

输出结果：

```java
show(sites.get( "runoob")) : www.runoob.com
show(sites.get( "baidu")) : ?
```

**getOrElse方法**

```scala
object Test {
   def main(args: Array[String]) {
      val a:Option[Int] = Some(5)
      val b:Option[Int] = None 
      
      println("a.getOrElse(0): " + a.getOrElse(0) )
      println("b.getOrElse(10): " + b.getOrElse(10) )
   }
}
```

输出结果 

```java
a.getOrElse(0): 5
b.getOrElse(10): 10
```

**Option常用的方法**

| 序号 | 方法及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | **def get: A**获取可选值                                     |
| 2    | **def isEmpty: Boolean**检测可选类型值是否为 None，是的话返回 true，否则返回 false |
| 3    | **def productArity: Int**返回元素个数， A(x_1, ..., x_k), 返回 k |
| 4    | **def productElement(n: Int): Any**获取指定的可选项，以 0 为起始。即 A(x_1, ..., x_k), 返回 x_(n+1) ， 0 < n < k. |
| 5    | **def exists(p: (A) => Boolean): Boolean**如果可选项中指定条件的元素存在且不为 None 返回 true，否则返回 false。 |
| 6    | **def filter(p: (A) => Boolean): Option[A]**如果选项包含有值，而且传递给 filter 的条件函数返回 true， filter 会返回 Some 实例。 否则，返回值为 None 。 |
| 7    | **def filterNot(p: (A) => Boolean): Option[A]**如果选项包含有值，而且传递给 filter 的条件函数返回 false， filter 会返回 Some 实例。 否则，返回值为 None 。 |
| 8    | **def flatMap[B](f: (A) => Option[B]): Option[B]**如果选项包含有值，则传递给函数 f 处理后返回，否则返回 None |
| 9    | **def foreach[U](f: (A) => U): Unit**如果选项包含有值，则将每个值传递给函数 f， 否则不处理。 |
| 10   | **def getOrElse[B >: A](default: => B): B**如果选项包含有值，返回选项值，否则返回设定的默认值。 |
| 11   | **def isDefined: Boolean**如果可选值是 Some 的实例返回 true，否则返回 false。 |
| 12   | **def iterator: Iterator[A]**如果选项包含有值，迭代出可选值。如果可选值为空则返回空迭代器。 |
| 13   | **def map[B](f: (A) => B): Option[B]**如果选项包含有值， 返回由函数 f 处理后的 Some，否则返回 None |
| 14   | **def orElse[B >: A](alternative: => Option[B]): Option[B]**如果一个 Option 是 None ， orElse 方法会返回传名参数的值，否则，就直接返回这个 Option。 |
| 15   | **def orNull**如果选项包含有值返回选项值，否则返回 null。    |

### Scala Iterator(迭代器)

- 迭代器 it 的两个基本操作是 **next** 和 **hasNext**。
- 调用 **it.next()** 会返回迭代器的下一个元素，并且更新迭代器的状态。
- 调用 **it.hasNext()** 用于检测集合中是否还有元素。
- 让迭代器 it 逐个返回所有元素最简单的方法是使用 while 循环：

实例:

```scala
object Test {
   def main(args: Array[String]) {
      val it = Iterator("Baidu", "Google", "Runoob", "Taobao")
      
      while (it.hasNext){
         println(it.next())
      }
    //获取最大和最小值
     println("最大的元素是:"+it.max)
   	 println("最小的元素是:"+it.min)
    	//获取长度
   	 println("it.size的值是:"+it.size)
     println("it.length的值是:"+it.length)
   }
}
```

**Scala Iterator 常用的方法**

| 序号 | 方法及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | **def hasNext: Boolean**如果还有可返回的元素，返回true。     |
| 2    | **def next(): A**返回迭代器的下一个元素，并且更新迭代器的状态 |
| 3    | **def ++(that: => Iterator[A]): Iterator[A]**合并两个迭代器  |
| 4    | **def ++[B >: A](that :=> GenTraversableOnce[B]): Iterator[B]**合并两个迭代器 |
| 5    | **def addString(b: StringBuilder): StringBuilder**添加一个字符串到 StringBuilder b |
| 6    | **def addString(b: StringBuilder, sep: String): StringBuilder**添加一个字符串到 StringBuilder b，并指定分隔符 |
| 7    | **def buffered: BufferedIterator[A]**迭代器都转换成 BufferedIterator |
| 8    | **def contains(elem: Any): Boolean**检测迭代器中是否包含指定元素 |
| 9    | **def copyToArray(xs: Array[A], start: Int, len: Int): Unit**将迭代器中选定的值传给数组 |
| 10   | **def count(p: (A) => Boolean): Int**返回迭代器元素中满足条件p的元素总数。 |
| 11   | **def drop(n: Int): Iterator[A]**返回丢弃前n个元素新集合     |
| 12   | **def dropWhile(p: (A) => Boolean): Iterator[A]**从左向右丢弃元素，直到条件p不成立 |
| 13   | **def duplicate: (Iterator[A], Iterator[A])**生成两个能分别返回迭代器所有元素的迭代器。 |
| 14   | **def exists(p: (A) => Boolean): Boolean**返回一个布尔值，指明迭代器元素中是否存在满足p的元素。 |
| 15   | **def filter(p: (A) => Boolean): Iterator[A]**返回一个新迭代器 ，指向迭代器元素中所有满足条件p的元素。 |
| 16   | **def filterNot(p: (A) => Boolean): Iterator[A]**返回一个迭代器，指向迭代器元素中不满足条件p的元素。 |
| 17   | **def find(p: (A) => Boolean): Option[A]**返回第一个满足p的元素或None。注意：如果找到满足条件的元素，迭代器会被置于该元素之后；如果没有找到，会被置于终点。 |
| 18   | **def flatMap[B](f: (A) => GenTraversableOnce[B]): Iterator[B]**针对迭代器的序列中的每个元素应用函数f，并返回指向结果序列的迭代器。 |
| 19   | **def forall(p: (A) => Boolean): Boolean**返回一个布尔值，指明 it 所指元素是否都满足p。 |
| 20   | **def foreach(f: (A) => Unit): Unit**在迭代器返回的每个元素上执行指定的程序 f |
| 21   | **def hasDefiniteSize: Boolean**如果迭代器的元素个数有限则返回 true（默认等同于 isEmpty） |
| 22   | **def indexOf(elem: B): Int**返回迭代器的元素中index等于x的第一个元素。注意：迭代器会越过这个元素。 |
| 23   | **def indexWhere(p: (A) => Boolean): Int**返回迭代器的元素中下标满足条件p的元素。注意：迭代器会越过这个元素。 |
| 24   | **def isEmpty: Boolean**检查it是否为空, 为空返回 true，否则返回false（与hasNext相反）。 |
| 25   | **def isTraversableAgain: Boolean**Tests whether this Iterator can be repeatedly traversed. |
| 26   | **def length: Int**返回迭代器元素的数量。                    |
| 27   | **def map[B](f: (A) => B): Iterator[B]**将 it 中的每个元素传入函数 f 后的结果生成新的迭代器。 |
| 28   | **def max: A**返回迭代器迭代器元素中最大的元素。             |
| 29   | **def min: A**返回迭代器迭代器元素中最小的元素。             |
| 30   | **def mkString: String**将迭代器所有元素转换成字符串。       |
| 31   | **def mkString(sep: String): String**将迭代器所有元素转换成字符串，并指定分隔符。 |
| 32   | **def nonEmpty: Boolean**检查容器中是否包含元素（相当于 hasNext）。 |
| 33   | **def padTo(len: Int, elem: A): Iterator[A]**首先返回迭代器所有元素，追加拷贝 elem 直到长度达到 len。 |
| 34   | **def patch(from: Int, patchElems: Iterator[B], replaced: Int): Iterator[B]**返回一个新迭代器，其中自第 from 个元素开始的 replaced 个元素被迭代器所指元素替换。 |
| 35   | **def product: A**返回迭代器所指数值型元素的积。             |
| 36   | **def sameElements(that: Iterator[_]): Boolean**判断迭代器和指定的迭代器参数是否依次返回相同元素 |
| 37   | **def seq: Iterator[A]**返回集合的系列视图                   |
| 38   | **def size: Int**返回迭代器的元素数量                        |
| 39   | **def slice(from: Int, until: Int): Iterator[A]**返回一个新的迭代器，指向迭代器所指向的序列中从开始于第 from 个元素、结束于第 until 个元素的片段。 |
| 40   | **def sum: A**返回迭代器所指数值型元素的和                   |
| 41   | **def take(n: Int): Iterator[A]**返回前 n 个元素的新迭代器。 |
| 42   | **def toArray: Array[A]**将迭代器指向的所有元素归入数组并返回。 |
| 43   | **def toBuffer: Buffer[B]**将迭代器指向的所有元素拷贝至缓冲区 Buffer。 |
| 44   | **def toIterable: Iterable[A]**Returns an Iterable containing all elements of this traversable or iterator. This will not terminate for infinite iterators. |
| 45   | **def toIterator: Iterator[A]**把迭代器的所有元素归入一个Iterator容器并返回。 |
| 46   | **def toList: List[A]**把迭代器的所有元素归入列表并返回      |
| 47   | **def toMap[T, U]: Map[T, U]**将迭代器的所有键值对归入一个Map并返回。 |
| 48   | **def toSeq: Seq[A]**将代器的所有元素归入一个Seq容器并返回。 |
| 49   | **def toString(): String**将迭代器转换为字符串               |
| 50   | **def zip[B](that: Iterator[B]): Iterator[(A, B)**返回一个新迭代器，指向分别由迭代器和指定的迭代器 that 元素一一对应而成的二元组序列 |

**伴生对象**

> 伴生对象和类名相同，可以访问类的私有属性和方法

```scala
// 私有构造方法
class Marker private(val color:String) {

  println("创建" + this)
  
  override def toString(): String = "颜色标记："+ color
  
}

// 伴生对象，与类名字相同，可以访问类的私有属性和方法
object Marker{
  
    private val markers: Map[String, Marker] = Map(
      "red" -> new Marker("red"),
      "blue" -> new Marker("blue"),
      "green" -> new Marker("green")
    )
    
    def apply(color:String) = {
      if(markers.contains(color)) markers(color) else null
    }
  
    
    def getMarker(color:String) = { 
      if(markers.contains(color)) markers(color) else null
    }
    def main(args: Array[String]) { 
        println(Marker("red"))  
        // 单例函数调用，省略了.(点)符号  
        println(Marker getMarker "blue")  
    }
}
```

### Scala Trait(特征)

- Scala Trait(特征) 相当于 Java 的接口，实际上它比接口还功能强大。
- 与接口不同的是，它还可以定义属性和方法的实现。
- 一般情况下Scala的类只能够继承单一父类，但是如果是 Trait(特征) 的话就可以继承多个，从结果来看就是实现了多重继承。

```scala
trait Equal {
  def isEqual(x: Any): Boolean
  def isNotEqual(x: Any): Boolean = !isEqual(x)
}

class Point(xc: Int, yc: Int) extends Equal {
  var x: Int = xc
  var y: Int = yc
  def isEqual(obj: Any) =
    obj.isInstanceOf[Point] &&
    obj.asInstanceOf[Point].x == x
}

object Test {
   def main(args: Array[String]) {
      val p1 = new Point(2, 3)
      val p2 = new Point(2, 4)
      val p3 = new Point(3, 3)

      println(p1.isNotEqual(p2))
      println(p1.isNotEqual(p3))
      println(p1.isNotEqual(2))
   }
}
```

执行结果:

```java
false
true
true
```

**特征构造顺序**

特征也可以有构造器，由字段的初始化和其他特征体中的语句构成。这些语句在任何混入该特征的对象在构造时都会被执行。

- 调用超类的构造器
- 特征构造器在超类构造器之后，类构造器之前执行
- 特征由左到右被构造
- 每个特征当中，父特征先被构造
- 如果多个特征共有一个父特征，父特征不会被重复构造
- 所有特征被构造完毕，子类被构造

### Scala 模式匹配

一个模式匹配包含了一系列备选项，每个都开始于关键字 **case**。每个备选项都包含了一个模式及一到多个表达式。箭头符号 **=>** 隔开了模式和表达式。

一下是简单的Scala模式匹配实例:

```scala
object Test {
   def main(args: Array[String]) {
      println(matchTest("two"))
      println(matchTest("test"))
      println(matchTest(1))
      println(matchTest(6))

   }
   def matchTest(x: Any): Any = x match {
      case 1 => "one"
      case "two" => 2
      case y: Int => "scala.Int"
      case _ => "many"
   }
}
```

以上的执行代码为: (能匹配多种类型的值，或者是类型对应的字段)

```java
2
many
one
scala.Int
```

使用了case关键字定义就是样例类，样例类是种特殊的类，经过优化用于模式匹配

```scala
object Test {
   def main(args: Array[String]) {
       val alice = new Person("Alice", 25)
    val bob = new Person("Bob", 32)
       val charlie = new Person("Charlie", 32)
   
    for (person <- List(alice, bob, charlie)) {
        person match {
            case Person("Alice", 25) => println("Hi Alice!")
            case Person("Bob", 32) => println("Hi Bob!")
            case Person(name, age) =>
               println("Age: " + age + " year, name: " + name + "?")
         }
      }
   }
   // 样例类
   case class Person(name: String, age: Int)
}
```

输出：

```java
Hi Alice!
Hi Bob!
Age: 32 year, name: Charlie?
```

在声明样例类时，下面的过程自动发生了

- 构造器的每个参数成为val，除非显示被声明var，但并不推荐这么做；
- 在伴生对象种提供了apply方法，所以可以不适用new关键字可构建对象
- 提供unapply方法使模式匹配可以工作
- 生成toString，equals，hashCode和copy方法，除非显示给出

### Scala正则表达式

Scala 通过 scala.util.matching 包中的 **Regex** 类来支持正则表达式。

如果需要查看所有的匹配项可以使用 findAllIn 方法。

你可以使用 mkString( ) 方法来连接正则表达式匹配结果的字符串，并可以使用管道(|)来设置不同的模式：

```scala
import scala.util.matching.Regex

object Test {
   def main(args: Array[String]) {
      val pattern = new Regex("(S|s)cala")  // 首字母可以是大写 S 或小写 s
      val str = "Scala is scalable and cool"
      
      println((pattern findAllIn str).mkString(","))   // 使用逗号 , 连接返回结果
   }
}
```

输出结果为:

```java
Scala,scala
```



如果你需要将匹配的文本替换为指定的关键词，可以使用 **replaceFirstIn( )** 方法来替换第一个匹配项，使用 **replaceAllIn( )** 方法替换所有匹配项，实例如下:

```scala
object Test {
   def main(args: Array[String]) {
      val pattern = "(S|s)cala".r
      val str = "Scala is scalable and cool"
      
      println(pattern replaceFirstIn(str, "Java"))
   }
}
```

执行以上代码，输出结果为：

```java
Java is scalable and cool
```

**正则表达式**(常用)

| 表达式    | 匹配规则                                                     |
| :-------- | :----------------------------------------------------------- |
| ^         | 匹配输入字符串开始的位置。                                   |
| $         | 匹配输入字符串结尾的位置。                                   |
| .         | 匹配除"\r\n"之外的任何单个字符。                             |
| [...]     | 字符集。匹配包含的任一字符。例如，"[abc]"匹配"plain"中的"a"。 |
| [^...]    | 反向字符集。匹配未包含的任何字符。例如，"[^abc]"匹配"plain"中"p"，"l"，"i"，"n"。 |
| \\A       | 匹配输入字符串开始的位置（无多行支持）                       |
| \\z       | 字符串结尾(类似$，但不受处理多行选项的影响)                  |
| \\Z       | 字符串结尾或行尾(不受处理多行选项的影响)                     |
| re*       | 重复零次或更多次                                             |
| re+       | 重复一次或更多次                                             |
| re?       | 重复零次或一次                                               |
| re{ n}    | 重复n次                                                      |
| re{ n,}   |                                                              |
| re{ n, m} | 重复n到m次                                                   |
| a\|b      | 匹配 a 或者 b                                                |
| (re)      | 匹配 re,并捕获文本到自动命名的组里                           |
| (?: re)   | 匹配 re,不捕获匹配的文本，也不给此分组分配组号               |
| (?> re)   | 贪婪子表达式                                                 |
| \\w       | 匹配字母或数字或下划线或汉字                                 |
| \\W       | 匹配任意不是字母，数字，下划线，汉字的字符                   |
| \\s       | 匹配任意的空白符,相等于 [\t\n\r\f]                           |
| \\S       | 匹配任意不是空白符的字符                                     |
| \\d       | 匹配数字，类似 [0-9]                                         |
| \\D       | 匹配任意非数字的字符                                         |
| \\G       | 当前搜索的开头                                               |
| \\n       | 换行符                                                       |
| \\b       | 通常是单词分界位置，但如果在字符类里使用代表退格             |
| \\B       | 匹配不是单词开头或结束的位置                                 |
| \\t       | 制表符                                                       |
| \\Q       | 开始引号：**\Q(a+b)\*3\E** 可匹配文本 "(a+b)*3"。            |
| \\E       | 结束引号：**\Q(a+b)\*3\E** 可匹配文本 "(a+b)*3"。            |

**正则表达式**(实例)

| 实例            | 描述                                          |
| :-------------- | :-------------------------------------------- |
| .               | 匹配除"\r\n"之外的任何单个字符。              |
| [Rr]uby         | 匹配 "Ruby" 或 "ruby"                         |
| rub[ye]         | 匹配 "ruby" 或 "rube"                         |
| [aeiou]         | 匹配小写字母 ：aeiou                          |
| [0-9]           | 匹配任何数字，类似 [0123456789]               |
| [a-z]           | 匹配任何 ASCII 小写字母                       |
| [A-Z]           | 匹配任何 ASCII 大写字母                       |
| [a-zA-Z0-9]     | 匹配数字，大小写字母                          |
| [^aeiou]        | 匹配除了 aeiou 其他字符                       |
| [^0-9]          | 匹配除了数字的其他字符                        |
| \\d             | 匹配数字，类似: [0-9]                         |
| \\D             | 匹配非数字，类似: [^0-9]                      |
| \\s             | 匹配空格，类似: [ \t\r\n\f]                   |
| \\S             | 匹配非空格，类似: [^ \t\r\n\f]                |
| \\w             | 匹配字母，数字，下划线，类似: [A-Za-z0-9_]    |
| \\W             | 匹配非字母，数字，下划线，类似: [^A-Za-z0-9_] |
| ruby?           | 匹配 "rub" 或 "ruby": y 是可选的              |
| ruby*           | 匹配 "rub" 加上 0 个或多个的 y。              |
| ruby+           | 匹配 "rub" 加上 1 个或多个的 y。              |
| \\d{3}          | 刚好匹配 3 个数字。                           |
| \\d{3,}         | 匹配 3 个或多个数字。                         |
| \\d{3,5}        | 匹配 3 个、4 个或 5 个数字。                  |
| \\D\\d+         | 无分组： + 重复 \d                            |
| (\\D\\d)+/      | 分组： + 重复 \D\d 对                         |
| ([Rr]uby(, )?)+ | 匹配 "Ruby"、"Ruby, ruby, ruby"，等等         |

### Scala提取器模式的调用

- 提取器是从传递给它的对象中提取出构造该对象的参数。

- Scala 提取器是一个带有unapply方法的对象。unapply方法算是apply方法的反向操作：unapply接受一个对象，然后从对象中提取值，提取的值通常是用来构造该对象的值。

**apply** 和 **unapply** 方法。通过 apply 方法我们无需使用 new 操作就可以创建对象。所以你可以通过语句 Test("Zara", "gmail.com") 来构造一个字符串 "Zara@gmail.com"。

​	**提取器使用模式匹配**

在我们实例化一个类的时，可以带上0个或者多个的参数，编译器在实例化的时会调用 apply 方法。我们可以在类和对象中都定义 apply 方法。

就像我们之前提到过的，unapply 用于提取我们指定查找的值，它与 apply 的操作相反。 当我们在提取器对象中使用 match 语句是，unapply 将自动执行，如下所示：

```scala
object Test {
   def main(args: Array[String]) {
      
      val x = Test(5)
      println(x)

      x match
      {
         case Test(num) => println(x + " 是 " + num + " 的两倍！")
         //unapply 被调用
         case _ => println("无法计算")
      }

   }
   def apply(x: Int) = x*2
   def unapply(z: Int): Option[Int] = if (z%2==0) Some(z/2) else None
}
```

输出结果:

```java
10
10 是 5 的两倍！
```

### Scala 文件 I/O

- **对文件进行写操作**

```scala
import java.io._

object Test {
   def main(args: Array[String]) {
      val writer = new PrintWriter(new File("test.txt" ))

      writer.write("spark 学习")
      writer.close()
   }
}
```

执行以上代码，会在你的当前目录下生产一个 test.txt 文件，文件内容为"spark 学习":

- **从屏幕上读取用户输入**

```scala
import scala.io._
object Test {
   def main(args: Array[String]) {
      print("请输入菜鸟教程官网 : " )
      val line = StdIn.readLine()

      println("谢谢，你输入的是: " + line)
   }
}
```

> *cala2.11 后的版本* **Console.readLine** *已废弃，使用* **scala.io.StdIn.readLine()** *方法代替。*

```java
请输入菜鸟教程官网 : www.runoob.com
谢谢，你输入的是: www.runoob.com
```

- **从文件上读取内容**

```scala
import scala.io.Source

object Test {
   def main(args: Array[String]) {
      println("文件内容为:" )

      Source.fromFile("test.txt" ).foreach{ 
         print 
      }
   }
}
```

执行的内容为:

```java
文件内容为:
菜鸟教程
```



























