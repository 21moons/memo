# 概要

* scala.AnyRef 是所有类的父类

* scala 即 scalable language, 它是面向对象和函数式编程的融合

* 用 scala 编写的程序易裁剪, 因为很多数据结构和类型都是用库的方式实现的, 比如说 BigInt

* Immutable data structures(不可变数据结构)是函数式编程的基石,另外一个特征是函数是第一类公民, 可以是参数，变量或返回值
  第一条的另外一种表述方法是方法没有任何 side effects, 他们仅仅从运行环境中获取参数和返回结果

* scala 支持类型推断

* scala 最常见的方式是匿名函数用 "=>" 定义, <- 主要是迭代里用, -> 主要是 map 里用, => 主要是匿名函数用.

* 语法糖

val 声明的变量是不可变的
var 声明的变量是可变的
scala 的所有类都是从 any 类派生而来的
scala 所有 value 都是 object, 所有运算符实际上都是方法
scala 中无论类型如何, == 都表示基于值的引用
!     actor 发送消息
!!    调用外部命令
println = print line

* Traits 类似于 java 中的 interface, can then be mixed together

# Programming in Scala, 3rd Edition

### 1.3 WHY SCALA?
Scala 的函数式特性也为编程提供了更高层次的思考. 其中最核心的理念是"函数是引用透明(referentially transparent)的"(引用透明指的是表达式可以在不影响程序行为的情况下被其计算后的值完全替换), 这意味着程序仅仅通过函数的返回结果来感知他. 

不像引用, 不可变数据(Immutable data)可以自由共享, 因为副本和共享的引用在使用中没有区别. 编写并发代码时, 这一优势尤为重要.

Scala 是静态类型, 允许使用泛型参数, 使用交叉点合并类型, 并使用抽象类型隐藏类型细节. 这些机制为构建和构建自己的类型打下了坚实的基础, 以便设计出安全灵活的接口.

### 类型推断(type inference)
Scala 有一个非常复杂的类型推断系统, 几乎可以让你省略代码中所有多余的类型信息.
Scala 中的类型推断可以走得更远. 事实上，用户代码中完全没有显式的类型信息并不罕见. 因此, Scala 程序通常看起来有点像动态脚本语言编写的程序. 这特别适用于构建客户侧应用程序代码, 因为它们通常用来粘合已完成的库. 然而对于库本身来说, 这种机制则是不太适合的, 因为库为了支持灵活的使用模式, 经常采用相当复杂的类型. 这是很自然的. 毕竟, 对于构成可重用组件接口的成员来说, 它的类型签名应该是明确的, 因为它们是组件与客户之间契约的重要组成部分。
<br>
<br>

## Chapter 2 First Steps in Scala

Packages in Scala are similar to packages in Java: They partition the global namespace and provide a mechanism for information hiding.
all of Java's primitive types have corresponding classes in the scala package.And when you compile your Scala code to Java bytecodes, the Scala compiler will use Java's primitive types where possible to give you the performance benefits of the primitive types.

scala 有两种类型的变量:

* val 声明的变量是不可变的 A val is similar to a final variable in Java. Once initialized, a val can never be reassigned.
* var 声明的变量是可变的   A var can be reassigned throughout its lifetime.

To enter something into the interpreter that spans multiple lines, just keep typing after the first line. If the code you typed so far is not complete, the interpreter will respond with a vertical bar("|") on the next line.
If you realize you have typed something wrong, but the interpreter is still waiting for more input, you can escape by pressing enter twice.

定义函数时, 函数的参数必须带有类型信息, 因为 scala 不能推断函数的参数类型.

``` scala
def max(x: Int, y: Int): Int = {
  if (x > y) x
  else y
}
```

![basic_form_of_a_function_definition](https://raw.githubusercontent.com/21moons/memo/master/res/img/scala/basic_form_of_a_function_definition.png)

<font size=2>Figure 2.1 - The basic form of a function definition in Scala.</font>

如果函数是递归的, 必须显式的指定函数返回值的类型.
如果函数语句只有一行, 函数定义时可以省略大括号.
Unit 类型的返回值表示函数没有返回值, 类似于 Java 的 void 类型.因此, 如果函数返回值的类型是 Unit, 那么意味着执行该函数的目的是为了它的 side effects.
scala 中注释用 // 或者 /* .... */
scala 中不支持 ++i 和 i++
scala 中用 ";" 来分隔语句, 而且 ";" 是可选的
如果函数 body 只有一行, 而且只有一个参数, 那么你在定义函数时可以不用显式的定义参数

``` scala
args.foreach((arg: String) => println(arg))
args.foreach(println)
```

![syntax_of_a_function_literal](https://raw.githubusercontent.com/21moons/memo/master/res/img/scala/syntax_of_a_function_literal.png)
<font size=2>Figure 2.2 - The syntax of a function literal in Scala.</font>

``` scala
for (arg <- args)
  println(arg)
```

"<-" 号在这里是 in 的意思, arg 是 val, for (arg <- args) 的意思是 "for arg in args." 
<br>
<br>

## Chapter 3 Next Steps in Scala

### PARAMETERIZE ARRAYS WITH TYPES

Parameterization means "configuring" an instance when you create it. You parameterize an instance with values by passing objects to a constructor in parentheses.

``` scala
val greetStrings = new Array[String](3)
greetStrings(0) = "Hello"
greetStrings(1) = ", "
greetStrings(2) = "world!\n"

for (i <- 0 to 2)
  print(greetStrings(i))
```

<font size=2>Listing 3.1 - Parameterizing an array with a type.</font>

When you define a variable with val, the variable can't be reassigned, but the object to which it refers could potentially still be changed. 

``` scala
for (i <- 0 to 2)
  print(greetStrings(i))
```

上面代码中的第一行说明了 Scala 的另一个通用规则: 如果一个方法只有一个参数, 调用它的时候可以不加圆点或圆括号. 在这个例子中, 实际上是调用了一个参数类型为 Int 的方法. **代码 "0 to 2" 被转换成方法调用 "(0).to(2)"**. 请注意, 只有在显式的指明方法调用者的情况下, 此语法才有效. 你不能这样写 "println 10", 但是可以写成 "Console println 10".

Scala 并没有实现运算符重载技术, 因为它实际上并没有传统意义上的运算符. 作为替代, scala 中可以使用 +, - , * 和 / 等字符作为方法名称. 因此, 当你将 "1 + 2" 键入到Scala解释器中时, 实际上是在 Int object 1 上调用名为 "+" 的方法, 传入 2 作为方法参数。 "1 + 2" 与 "(1).+(2)" 是等价的.

![](https://raw.githubusercontent.com/21moons/memo/master/res/img/scala/All_operations_are_method_calls.png)
<font size=2>Figure 3.1 - All operations are method calls in Scala.</font>

这个例子另一个重要的方面是让你了解为什么要用括号访问数组中的元素. 与Java相比，Scala的特例更少. 数组不过是类的实例, 就像 Scala 中的其他类一样. 当你将包含一个或多个值的括号应用于变量时, Scala 会将代码转换成在该变量上调用 apply 方法. 所以 greetStrings(i) 会被转换为 greetStrings.apply(i). 因此在 scala 中访问数组中的元素也是通过调用方法. 这个原则不仅限于数组: **任何对象后面如果跟着括号括起来的参数(作为右值), 都将被转换为 apply 方法调用.** 当然这个只有当该对象定义了 apply 方法时才会通过编译. 所以这不是特例. 这是一条通用规则.

类似的,**对象后面如果跟着括号括起来的参数, 当赋值给它时(作为左值), 编译器会把它转换成一个 update 方法调用, 调用参数就是括号中的值和等号右边的值.**

``` scala
greetStrings(0) = "Hello"
greetStrings.update(0, "Hello")
```

Scala 通过将从数组到表达式的所有一切都视为带有方法的对象, 来实现概念上的简化.

``` scala
val numNames = Array("zero", "one", "two")
val numNames2 = Array.apply("zero", "one", "two")
```

### USE LISTS

函数式编程的一个重要思想就是方法调用不应该有副作用(side effects). 一个方法的唯一行为应该是计算并返回一个值. 采取这种编程范式获得的好处是方法变得更独立(方法之间依赖变少), 逻辑变得更清晰, 因此更可靠和更容易重用. 另一个好处(对于静态类型语言来说)是方法的入参和返回值都由类型检查器进行检查, 这样的话逻辑错误可以以类型错误的形式被检测出来. 将函数式编程的哲学应用到对象的世界(world of objects), 意味着使对象不可变(没有状态了, 实际上不是没有状态, 命令式编程中的状态变化转换为函数式编程中的新状态替换旧
状态).

正如你所看到的, 一个 Scala 数组是一个可变的对象序列, 它们都是相同的类型. 例如, 数组一旦初始化后, 就不能更改数组的长度, 但是你可以更改数组元素的值. 因此, 我们认为数组是可变对象.

对于由一组类型相同的对象组成的不可变序列, 可以使用 Scala 的 List 类. 与 Array[String] 一样, List[String] 只包含字符串. Scala 的 List 类 scala.List, 与 Java 的 java.util.List 类是不同的, Scala 中的 list 类是不可变的(而Java 的 list 类是可变的). 换句话说, Scala 的 list 类生来就是为了支持函数式编程的.

``` scala
val oneTwoThree = List(1, 2, 3)
```

List 中的 ":::" 方法用于连接两个 List, 该方法返回一个新的 List.

``` scala
val oneTwo = List(1, 2)
val threeFour = List(3, 4)
val oneTwoThreeFour = oneTwo ::: threeFour
```

List 中的 "::" 方法用于在 List 头部加入一个新的元素, 该方法也返回一个新的 List.

``` scala
val twoThree = List(2, 3)
val oneTwoThree = 1 :: twoThree
```

如果方法名以冒号结尾, 方法在右操作数上调用, 如果方法名不是以冒号结尾, 方法在左操作数上调用.

Nil 用来指定一个空 List, 初始化新 List 的一种方法是用 "::" 方法连接各个元素, Nil 作为最后一个元素.
下面的两个表达式是等价的: 

``` scala
val oneTwoThree = List(1, 2, 3)
val oneTwoThree = 1 :: 2 :: 3 :: Nil
```


| What it is | What it does | 
| ---------- | ------------ | 
| List() or Nil | The empty List |
| List("Cool", "tools", "rule") | Creates a new List[String] with the three values"Cool", "tools", and "rule" |
| val thrill = "Will" :: "fill" :: "until" :: Nil | Creates a new List[String] with the three values"Will", "fill", and "until" |
| List("a", "b") ::: List("c", "d") | Concatenates two lists (returns a new List[String] with values "a", "b", "c", and "d") |
| thrill(2) | Returns the element at index 2 (zero based) of thethrill list (returns "until") |
| thrill.count(s => s.length == 4) | Counts the number of string elements in thrill that have length 4 (returns 2) |
| thrill.drop(2) | Returns the thrill list without its first 2 elements (returns List("until")) |
| thrill.dropRight(2) | Returns the thrill list without its rightmost 2 elements (returns List("Will")) |
| thrill.exists(s => s == "until") | Determines whether a string element exists in thrillthat has the value "until" (returns true) |
| thrill.filter(s => s.length == 4) | Returns a list of all elements, in order, of the thrilllist that have length 4 (returns List("Will", "fill")) |
| thrill.forall(s => s.endsWith("l")) | Indicates whether all elements in the thrill list end with the letter "l" (returns true) |
| thrill.foreach(s => print(s)) | Executes the print statement on each of the strings in the thrill list (prints "Willfilluntil") |
| thrill.foreach(print) | Same as the previous, but more concise (also prints"Willfilluntil") |
| thrill.head | Returns the first element in the thrill list (returns"Will") |
| thrill.init | Returns a list of all but the last element in the thrilllist (returns List("Will", "fill")) |
| thrill.isEmpty | Indicates whether the thrill list is empty (returnsfalse) |
| thrill.last | Returns the last element in the thrill list (returns"until") |
| thrill.length | Returns the number of elements in the thrill list (returns 3) |
| thrill.map(s => s + "y") | Returns a list resulting from adding a "y" to each string element in the thrill list (returnsList("Willy", "filly", "untily")) |
| thrill.mkString(", ") | Makes a string with the elements of the list (returns"Will, fill, until") |
| thrill.filterNot(s => s.length == 4) | Returns a list of all elements, in order, of the thrilllist except those that have length 4 (returnsList("until")) |
| thrill.reverse | Returns a list containing all elements of the thrilllist in reverse order(returnsList("until", "fill", "Will")) |
| thrill.sort((s, t) => s.charAt(0).toLower < t.charAt(0).toLower)| Returns a list containing all elements of the thrilllist in alphabetical order of the first character lowercased (returns List("fill", "until", "Will")) |
| thrill.tail | Returns the thrill list minus its first element (returns List("fill", "until")) |

### USE TUPLES

另一个有用的容器对象是元组(tuple). 像 List 一样, 元组是不可改变的, 但是与 List 不同, 元组可以包含不同类型的元素. List 可以是一个 List[Int] 或者 List [String], 而元组可以同时包含整数和字符串. 例如, 如果你需要在方法中返回多个对象, 那么元组将非常有用. Java 通常会创建一个类似 JavaBean 的类来保存多个返回值, 而在 Scala 中你可以直接返回一个元组. 要实例化一个新的元组来保存一些对象, 只需将对象放在括号中, 对象之间用逗号隔开. 一旦元组实例化成功, 你可以点, 下划线和索引(**索引从1开始**)组成的标记来访问元组中的元素.

``` scala
val pair = (99, "Luftballons")
println(pair._1)
println(pair._2)
```

### USE SETS AND MAPS

因为 Scala 旨在帮助你同时利用函数式和命令式编程风格, 所以集合相关的库特别注重区分可变集合和不可变集合. 例如, arrays 总是可变的; lists 总是不变的. Scala 还为 sets 和 maps 同时提供了可变和不可变的备选方案, 但是两个版本都使用相同的简单名称. 对于 sets 和 maps, Scala 通过类的层次结构对可变/不可变进行了建模.

例如, Scala API 将 sets 作为一个基础 trait(trait 类似于 Java 中的接口). 然后 Scala 又提供两个 subtraits, 一个用于可变集, 另一个用于不可变集. 

![](https://raw.githubusercontent.com/21moons/memo/master/res/img/scala/Class_hierarchy_for_Scala_sets.png)
<font size=2>Figure 3.2 - Class hierarchy for Scala sets.</font>

* immutable sets

``` scala
var jetSet = Set("Boeing", "Airbus")
jetSet += "Lear"
println(jetSet.contains("Cessna"))
```

* mutable set ("+=" 是方法名)

``` scala
import scala.collection.mutable

val movieSet = mutable.Set("Hitch", "Poltergeist")
movieSet += "Shrek"
println(movieSet)
```

![](https://raw.githubusercontent.com/21moons/memo/master/res/img/scala/Class_hierarchy_for_Scala_maps.png)
<font size=2>Figure 3.2 - Class hierarchy for Scala maps.</font>

* immutable map (没有指定导入的包, 默认的 map 就是 immutable map, map 中的元素是元组)
``` scala
val romanNumeral = Map(
  1 -> "I", 2 -> "II", 3 -> "III", 4 -> "IV", 5 -> "V"
)
println(romanNumeral(4))
```

* mutable map ("+=" 是方法名)
1 -> "Go to island." 和 (1).->("Go to island.")) 等价

``` scala
import scala.collection.mutable

val treasureMap = mutable.Map[Int, String]()
treasureMap += (1 -> "Go to island.")
treasureMap += (2 -> "Find big X on ground.")
treasureMap += (3 -> "Dig.")
println(treasureMap(2))
```

### LEARN TO RECOGNIZE THE FUNCTIONAL STYLE

正如第1章所提到的, Scala 允许程序员以命令式的风格进行编程, 但是也鼓励你采用函数式的风格. 如果你是 Java 程序员, 那么在学习 Scala 时可能会面临的主要挑战之一是如何进行函数式编程.

首先是识别两种编程风格之间的区别. 如果代码包含任何变量, 它可能是命令式编程. 如果代码根本不包含变量, 比如说只包含 vals, 那么则可能是功能性风格. 走向函数式编程的一种方式就是在编程时不使用变量.

* 命令式风格

``` scala
def printArgs(args: Array[String]): Unit = {
  var i = 0
  while (i < args.length) {
    println(args(i))
    i += 1
  }
}
```

* 函数式风格

``` scala
def printArgs(args: Array[String]): Unit = {
  for (arg <- args)
    println(arg)
}
```

printArgs 并不是纯函数, 因为它依然有 side effects -- 打印到标准输出流, 另外一个 side effects 就是返回值是 Unit, 如果一个函数什么值都不返回, 那么它肯定在世界上做了一些其他的事情(暗示). 更偏向于函数化的编码是仅仅格式化参数，然后返回格式化后的字符串

``` scala
def formatArgs(args: Array[String]) = args.mkString("\n")
```

每个有用的程序都可能有某种形式的副作用(side effects); 否则, 它将无法为使用者提供价值. 编写没有副作用的方法, 鼓励你设计副作用最小的程序. 这种方法的好处之一是它可以让你的程序更容易测试.

要记住, 无论是变量还是副作用都不是生来就是邪恶的. scala 不是一个纯粹的函数式的语言. scala 是函数式/命令式的混合体. 你可能会发现, 在某些情况下, 命令式编程风格更适合解决手头的问题, 在这种情况下, 你应该毫不犹豫地使用它(命令式编程). 


## Chapter 4 Classes and Objects

### 4.1 CLASSES, FIELDS, AND METHODS

类是对象的蓝图. 定义类后, 可以使用关键字 new 从蓝图创建对象.

在类定义中会放置字段和方法, 这些字段和方法统称为 members.Fields. 那些使用 val 或 var 修饰的字段用来引用对象. 而使用 def 定义的方法包含可执行代码. 变量保存对象的状态或数据, 而方法使用该数据来执行对象的计算工作. 当你实例化某个类时, 运行环境分配一些内存来保存对象的状态(即变量).

* 追求对象健壮性的一个重要方法是确保对象的状态在其整个生命周期内保持可用, 解决方法是将字段设为 private, 使其只能被同一个类中定义的方法访问, 所有可以更新状态的代码都将在字段所属的类中.
* Public 是 Scala 的默认访问级别.
* Scala 中方法参数的一个重要特征是它们是 vals, 而不是 vars, 如果你尝试在方法内部修改参数, 将会导致编译失败.(呃, 函数式)
* 如果方法中没有显式的返回语句, 那么 scala 方法将返回该方法计算的最后一个值. scala 建议编码时不要显式的指定返回值, 相反, 将每个方法视为一个表达式, 该表达式返回一个值. 这种理念将鼓励您将方法设计得非常小.
* 如果方法只有一行表达式, 那么可以将方法的大括号省略. 如果表达式很短, 它甚至可以与 def 放在同一行. 为了将简洁做到极致, 您甚至可以省略函数返回类型, Scala 会自行推断(不建议, 容易出错).
* 方法的 side effect 主要指改变了外部变量的状态或做了 I/O 操作. (A side effect is generally defined as mutating state somewhere external to the method or performing an I/O action.)
* 一个返回值为 Unit 的仅为了其 side effect (通常指改变外部变量状态)而存在, 我们将这种方法称为过程(procedure).
* ";" 号用来分隔语句, 如果一行只有一个语句, 则可以省略 ";" 号.
* 如果你链接多行包含类似 "+" 符合的语句, 通常会将操作符放在行尾而不是行首.

偶尔 Scala 会违背你的意愿将声明分为两部分:(所以省略 ";" 号这功能没啥用)

``` scala
x
+ y
```

scala 会将上面的代码解析为两行语句, 而不是你预期的 x + y, 因此, 无论何时链接诸如 + 的中缀操作, 常见的Scala样式是将操作符放在行的末尾而不是开头：

``` scala
x +
y +
z
```

scala 会将上面的代码解析为一行语句.

关于语句分离的准确规则很简单. 简而言之, 除非满足下列条件之一, 否则将行结尾视为语句的结束:

1. 行尾的符号作为语句的结尾来说不合法, 例如句号或中缀运算符.
2. 下一行作为一个语句的开头来说无法解析.
3. 该行在括号 (...) 或括号 [...] 内结束.(因为 (...) 或 [...] 无论如何都不能包含多个语句)

### 4.3 SINGLETON OBJECTS

* scala 中的类不能含有静态成员(声称比 java 更面向对象), 单例(singleton)对象则是一个替换方案. 单例对象用 object 定义.

* 当一个单例对象与类同名时, 我们就把它称为类的 "伴生对象"(companion object), 而这个类被称为单例对象的 "伴生类"(companion class), **类和它的伴生对象必须在同一个文件中定义**. 类和伴生对象的私有成员对于对方来说都是可见的(类似于 C++ 中的友元, 还是一个对象拆成了两个部分).没有同名伴生类的单例对象称为孤立对象(standalone object).

``` scala
// In file ChecksumAccumulator.scala
import scala.collection.mutable

object ChecksumAccumulator {
  // 对象引用不变, 而不是对象不变
  private val cache = mutable.Map.empty[String, Int]

  def calculate(s: String): Int =
    if (cache.contains(s))
      cache(s)
    else {
      val acc = new ChecksumAccumulator

      for (c <- s)
        acc.add(c.toByte)

      val cs = acc.checksum()
      cache += (s -> cs)
      cs
  }
}
```

单例对象不仅仅是静态方法的持有者, 它还是头等对象(irst-class object). 因此, 您可以将单例对象的名称视为附加到对象的 "名称标记".

定义单例对象并不是定义类型(Scala 抽象级别). 仅给出 ChecksumAccumulator object 的定义, 并不能创建 ChecksumAccumulator 类型的变量. 相反, 名为 ChecksumAccumulator 的类型由 singleton object 的伴随类定义. 但是, 单例对象扩展了超类, 并且可以混合 traits(通过 extends 关键字). 每个单例对象都是其超类和混入的 traits 的实例，你可以通过这些类型调用其方法, 从这些类型的变量引用它, 并将其传递给期望这些类型的方法. 我们将在 13 章中展示一些继承自类(classes)和特征(traits)的单例对象的例子.

类和单例对象之间的一个区别是单例对象不能接受参数, 而类可以. 因为你无法使用 new 关键字实例化单例对象, 所以无法将参数传递给它. 每个单例对象都实现为从静态变量引用的合成类(synthetic class 合成类的名称是对象名称加上美元符号)的实例, 因此它们具有与 Java 中 static 相同的初始化语义. 特别是, 单例对象在第一次访问时才初始化.

与伴随类不共享相同名称的单例对象称为 "独立对象"(standalone object). 独立对象有很多用途, 包括作为通用方法的集合或定义 Scala 应用程序的入口点.

### 4.4 A SCALA APPLICATION

要运行 Scala 程序, 必须提供一个独立对象的名称, 该独立对象存在 main 方法的，并且 main 方法接受一个参数 Array [String], 返回类型为 Unit. 任何一个拥有 main 方法的独立对象都可以用作应用程序的入口点.

``` scala
// In file Summer.scala
import ChecksumAccumulator.calculate

object Summer {
  def main(args: Array[String]) = {
    for (arg <- args)
      println(arg + ": " + calculate(arg))
  }
}
```

Scala 隐式地将 java.lang 包和 scala 包的成员, 以及名为 Predef 的单例对象的成员导入到每个 Scala 源文件中. Predef 驻留在 scala 包中, 包含许多有用的方法. 例如, 当你在 Scala 源文件中说 println 时, 实际上是在 Predef 上调用 println. (Predef.println 转身调用Console.println, 它完成真正的工作). 当你说 assert 时, 你正在调用 Predef.assert.

Scala 和 Java之间的一个区别是, Java 要求你将类放在以类命名的文件中 - 例如, 你将类 SpeedRacer 放在文件 SpeedRacer.java 中, 然而在 scala 中你可以将 .scala 源码文件命名为任何你想要的东西, 无论你在文件中放入了什么 Scala 类或代码. 但是, 通常在非脚本(non-scripts)的情况下, 建议使用 java 的样式命名源文件, 这样程序员可以通过查看文件名来更轻松地找到类.

ChecksumAccumulator.scala 和 Summer.scala 都不是脚本, 因为它们以定义结尾. 与之形成对比的是, 脚本必须以结果表达式结束. 因此, 如果您尝试将 Summer.scala 作为脚本运行, Scala 解释器会抱怨 Summer.scala 没有以结果表达式结束. 所以, 您需要使用 Scala 编译器编译这些文件, 然后运行生成的类文件. 一种方法是使用 scalac, 它是基本的 Scala 编译器, 如下所示:

$ scalac ChecksumAccumulator.scala Summer.scala

这会编译您的源文件, 但在编译完成可能要花一点时间. 原因是每次编译器启动时, 它都会花时间扫描 jar 文件的内容并进行其他初始工作. 出于这个原因, Scala 发行版还包括一个名为 fsc 的 Scala compilerdaemon(用于快速 Scala 编译器). 你这样使用它:

$ fsc ChecksumAccumulator.scala Summer.scala

第一次运行 fsc 时, 它将在本地创建一个连接到计算机端口的服务器守护进程. 然后它通过端口将要编译的文件发送到守护程序, 由守护程序来进行编译. 下次运行 fsc 时, 守护程序仍然在运行, 因此 fsc 只用将文件列表发送到守护程序, 守护程序立即开始编译文件. 使用 fsc, 您只需要在 Java 运行环境第一次启动时等待. 任何时候你想停止 fsc 守护程序, 都可以使用 fsc-shutdown 来执行此操作.

运行 scalac 或 fsc 命令将生成 Java 类文件, 然后可以通过 scala 命令运行这些文件, 这与您在前面示例中用于调用解释器的命令相同. 但是, 这次并不是给它一个带有 .scala 扩展名的文件来解释, 在这种情况下, 你会给它一个包含正确方法的独立对象的名称. 因此, 您可以通过键入以下命令来运行 Summer 应用程序:

$ scala Summer of love

### 4.5 THE APP TRAIT

Scala 提供了一个 trait, scala.App, 可以节省一些输入时间. 

``` scala
import ChecksumAccumulator.calculate

object FallWinterSpringSummer extends App {
  for (season <- List("fall", "winter", "spring"))
    println(season + ": " + calculate(season))
}
```

要使用 trait, 首先要在单例对象的名称后面加上 "extends App". 然后, 不用编写 main 方法, 而是将你打算在 main 方法中放入的代码直接放在单例对象的花括号之间. 您可以通过名为 args 的字符串数组访问命令行参数, 这样就够了, 你可以编译和运行它.

## Chapter 5 Basic Types and Operations

-----------------------

### 5.1 SOME BASIC TYPES

* Byte    8-bit signed two's complement integer (-27 to 27 - 1, inclusive)
* Short   16-bit signed two's complement integer (-215 to 215 - 1, inclusive)
* Int     32-bit signed two's complement integer (-231 to 231 - 1, inclusive)
* Long    64-bit signed two's complement integer (-263 to 263 - 1, inclusive)
* Char    16-bit unsigned Unicode character (0 to 216 - 1, inclusive)
* String  a sequence of Chars
* Float   32-bit IEEE 754 single-precision float
* Double  64-bit IEEE 754 double-precision float
* Boolean true or false

除了 string 类型在 java.lang 包中定义, 其他类型都是在 scala 中定义. scala 包和java.lang 包的所有成员都自动导入到每个 Scala 源文件中, 您可以在任何地方使用简称(即 Boolean, Char 或 String 等名称).

scala> val tower = 35L
tower: Long = 35

scala> val of = 31l
of: Long = 31

scala> val little = 1.2345F
little: Float = 1.2345

scala> val bigger = 1.2345e1
bigger: Double = 12.345

scala> val a = 'A'
a: Char = A

scala> val f = '\u0044'
f: Char = D

scala> val hello = "hello"
hello: String = hello

scala> val bool = true
bool: Boolean = true

转义符号:

* \n   line feed (\u000A)
* \b   backspace (\u0008)
* \t   tab (\u0009)
* \f   form feed (\u000C)
* \r   carriage return (\u000D)
* \"   double quote (\u0022)
* \'   single quote (\u0027)
* \\   backslash (\u005C)

你使用连续三个双引号(""")开始和结束一个 raw string. raw string  的内部可能包含任何字符, 包括换行符, 引号和特殊字符.

``` scala
println("""Welcome to Ultamix 3000.
          Type "HELP" for help.""")
```

上面的语句返回的结果是:

Welcome to Ultamix 3000.
        Type "HELP" for help.

现在的问题是第二行之前的前导空格也包含在了字符串中! 要帮助解决这种问题, 可以在字符串上调用 stripMargin. 要使用此方法, 请在每行的前面放置一个管道符(|), 然后在整个字符串上调用 stripMargin:

``` scala
println("""|Welcome to Ultamix 3000.
           |Type "HELP" for help.""".stripMargin)
```

上面的语句返回的结果是:

Welcome to Ultamix 3000.
Type "HELP" for help.

symbol 在字面上写作 'ident, 其中 ident 可以是任何字母数字标识符. 此类文字映射到预定义类 scala.Symbol 的实例. 具体来说, literal'cymbal 将由编译器扩展为工厂方法调用: Symbol("cymbal"). symbol 通常在动态类型语言用作标识符. 例如, 您可能希望定义更新数据库中记录的方法:

  scala> def updateRecordByName(r: Symbol, value: Any) = {
  // code goes here
  }
  updateRecordByName: (Symbol,Any)Unit

该方法将指示记录字段名称的 symbol 和用于更新记录中字段的值作为参数. 在动态类型语言中，您可以将未声明的字段标识符传递给方法, 但在 Scala 中, 这将无法通过编译:

scala> updateRecordByName(favoriteAlbum, "OK Computer")
<console>:6: error: not found: value favoriteAlbum
updateRecordByName(favoriteAlbum, "OK Computer")

相反, 几乎同样简洁, 您可以传递 symbol:

  scala> updateRecordByName('favoriteAlbum, "OK Computer")

除了找出它的名字之外, 用符号做的事情并不多:

  scala> val s = 'aSymbol
  s: Symbol = 'aSymbol

  scala> val nm = s.name
  nm: String = aSymbol

另一件值得注意的事情是如果两次写入相同的 Symbol, 则两个表达式都将引用完全相同的 Symbol 对象.

<font color=#fd0209 size=6 >symbol 这东西不过是为了规避函数参数必须为 val 的限制</font>

### 5.3 STRING INTERPOLATION

Scala 包含一个灵活的字符串插值机制, 允许您在字符串中嵌入表达式. 它最常见的用例是为字符串连接提供简洁易读的替代方法. 这是一个例子:

``` scala
val name = "reader"
println(s"Hello, $name!")
```

可以在字符串中的美元符号后面的大括号内放置表达式, 对于单变量表达式, 可以将变量名称放在美元符号后面(不需要大括号)

  scala> s"The answer is ${6 * 7}."
  res0: String = The answer is 42.

除了 s 以外, Scala 默认提供另外两个字符串插值器: raw 和 f.

raw 字符串插值器的行为类似于 s, 除了它不识别转义字符.

  println(raw"No\\\\escape!") // prints: No\\\\escape!

f 字符串插值器允许您将 printf 样式的格式化指令附加到嵌入式表达式.

  scala> f"${math.Pi}%.5f"
  res1: String = 3.14159

如果没有为嵌入式表达式提供格式化指令, 则 f 字符串插值器将默认为 %s.

  scala> val pi = "Pi"
  pi: String = Pi
  scala> f"$pi is approximately ${math.Pi}%.8f."
  res2: String = Pi is approximately 3.14159265.

在 Scala 中, 字符串插值是通过在编译时重写代码来实现的. 库和用户可以为其他目的自定义字符串插值器.

### 5.4 OPERATORS ARE METHODS

Scala 为其基本类型提供了丰富的运算符. 正如前面章节中提到的, 这些运算符实际上只是普通方法调用. 例如, 1 + 2 实际上与1.+(2) 相同. 换句话说, 类 Int 包含一个名为 + 的方法, 它接受一个 Int 并返回一个 Int 结果. 添加两个 Ints 时会调用  + 方法:
  scala> val sum = 1 + 2 // Scala invokes 1.+(2)
  sum: Int = 3

实际上, Int 包含几个带有不同参数类型的重载 + 方法. 例如, Int 有另一个方法, 也叫做 +, 它接受 Long 类型的参数并返回一个 Long. 如果添加 Long 和 Int, 将调用此 + 方法, 如下所示:

  scala> val longSum = 1 + 2L // Scala invokes 1.+(2L)
  longSum: Long = 3

<p align="left" style="color:red;"><font size=5><b>注: 算术运算都是方法调用. </b></font></p>

符号 "+" 是一个运算符 - 一个特定的中缀运算符. scala 中的运算符不同于其他语言中的运算符, 您可以像使用运算符一样调用任何方法.

<p align="left" style="color:red;"><font size=5><b>注: infix notation: 中缀表示法, 意味着调用的方法位于对象和您希望传递给方法的参数之间. </b></font></p>

  scala> val s = "Hello, world!"
  s: String = Hello, world!
  scala> s indexOf 'o' // Scala invokes s.indexOf('o')
  res0: Int = 4

但是如果方法接受多个参数, 必须将这些参数放在括号中.

  scala> s indexOf ('o', 5) // Scala invokes s.indexOf('o', 5)
  res1: Int = 8

**ANY METHOD CAN BE AN OPERATOR**

除了中缀表示法外, Scala 还有另外两种运算符表示法: 前缀和后缀. 在前缀(prefix)表示法中, 将方法名称放在要调用方法的对象之前(例如, -7 中的  "-"). 在后缀(postfix)表示法中, 将方法放在对象之后(例如, "7 toLong" 中的 "toLong"). 前缀和后缀运算符是一元的: 它们只需要一个操作数. 中缀运算符需要两个操作数.

可用作前缀运算符的标识符是 "+", "-", "!", 和 "~". 因此, 如果定义名为 unary_! 的方法, 则可以使用前缀运算符表示法(例如 !p)对相应类型的值或变量调用该方法. 但是, 如果定义一个名为 unary_* 的方法, 则无法使用前缀运算符表示法, 因为 "*" 不是可用作前缀运算符的四个标识符之一. 您可以直接调用该方法, 如 inp.unary_*, 但如果您尝试通过 *p 调用它, Scala将解析为 *.p 一样, 这可不是您想要的!

后缀运算符是在没有点或括号的情况下调用不带参数的方法. 在 Scala 中, 您可以在方法调用上不写空括号(没有参数时). 如果方法有副作用, 你可以包括括号, 例如 println(), 但如果方法没有副作用, 你可以不写, 例如在 String 上调用 toLowerCase:

  scala> val s = "Hello, world!"
  s: String = Hello, world!

  scala> s toLowerCase
  res4: String = hello, world!

### 5.5 ARITHMETIC OPERATIONS

当左右操作数都是整数类型(Int, Long, Byte, Short 或 Char)时, "/" 运算符将告诉您商的整数部分, 不包括任何余数. "%"运算符表示整数除法的余数.

使用 "%" 获得的浮点余数不是 IEEE 754 标准定义的值. IEEE 754 余数在计算余数时使用舍入除法, 而不是截断除法, 因此它与整数余数运算完全不同. 如果您真的需要 IEEE 754 余数, 可以在 scala.math 上调用 IEEEremainder, 如下所示:

 scala> math.IEEEremainder（11.0,4.0）
 res14：Double = -1.0

数字类型还提供一元前缀运算符 "+"(对应方法 unary_+)和 "-"(对应方法 unary_-), 它们允许您标识数字是正数还是负数, 如 -3 或 +4.0. 如果未指定一元运算符 "+" 或 "-", 则将数字默认为正数. 一元 - 也可用于反转变量. 这里有些例子:

 scala> val neg = 1 + -3
 neg：Int = -2

 scala> val y = +3
 y：Int = 3

 scala> -neg
 res15：Int = 2

### 5.6 RELATIONAL AND LOGICAL OPERATIONS

### 5.7 BITWISE OPERATIONS

### 5.8 OBJECT EQUALITY

如果要比较两个对象是否相等，可以使用 "==" 或其反向 "!=". 这些操作实际上适用于所有对象, 而不仅仅是基本类型.

如您所见, "==" 经过精心设计, 因此在大多数情况下能够如你所愿. 这是通过一个非常简单的规则来完成的: 首先检查左侧是否为空. 如果它不为 null, 则调用 equals 方法. 由于 equals 是一种方法, 因此您获得的比较结果取决于左侧操作数的类型. 由于存在自动空检查, 因此您无需亲自进行检查.

在 Java 中, 您可以使用 == 来比较原始类型和引用类型. 在原始类型上, Java "==" 比较的是它们的值, 就像在 Scala 中一样. 但是, 在引用类型上, Java 的 == 比较的是引用相等性, 这意味着两个变量是否指向 JVM 堆上的同一个对象. Scala 提供了一个用于比较引用相等性的工具, 名称为 eq. 但是, eq 及 ne 仅适用于直接映射到 Java 对象的对象. 有关 eq 和 ne 的完整详细信息, 请参见第 11.1 节和第 11.2 节. 另外, 请参阅第 30 章, 了解如何编写好的 equals 方法.

### 5.9 OPERATOR PRECEDENCE AND ASSOCIATIVITY

### 5.10 RICH WRAPPERS

Table 5.4 - Some rich operations
| Code | Result |
| ---------- | ------------ |
| 0 max 5    |          5   |
| 0 min 5    |          0   |
| -2.7 abs   |      2.7     |
| -2.7 round |       -3L    |
| 1.5 isInfinity       | false          |
| (1.0 / 0) isInfinity |true            |
| 4 to 6               |Range(4, 5, 6)  |
| "bob" capitalize     |"Bob"           |
| "robert" drop 2      |"bert"          |

Table 5.5 - Rich wrapper classes

| Basic type  | Rich wrapper |
| ----------- | ------------ |
|     Byte    | scala.runtime.RichByte |
|     Short   | scala.runtime.RichShort  |
|     Int     | scala.runtime.RichInt    |
|     Long    | scala.runtime.RichLong   |
|     Char    | scala.runtime.RichChar   |
|     Float   | scala.runtime.RichFloat  |
|     Double  | scala.runtime.RichDouble |
|     Boolean | scala.runtime.RichBoolean |
|     String  | scala.collection.immutable.StringOps |

## Chapter 6 Functional Objects

-----------------------

### 6.1 A SPECIFICATION FOR CLASS RATIONAL

### 6.2 CONSTRUCTING A RATIONAL

**IMMUTABLE OBJECT TRADE-OFFS**

不可变对象相对于可变对象提供了若干优点, 但也有一个潜在的缺点. 首先, 不可变对象通常比可变对象更容易推理, 因为它们没有随时间变化的复杂状态空间. 其次, 你可以非常自由地传递不可变对象, 而在传递可变对象时, 你可能需要制作防御性副本. 第三, 一旦正确构造了不可变对象, 两个线程同时访问不可变对象时就没有办法破坏它的状态, 因为没有线程可以改变不可变对象的状态. 第四, 不可变对象制作安全的哈希表键. 例如, 如果可变对象在放入 HashSet 后发生变化, 则下次查看 HashSet 时就可能找不到该对象.

不可变对象的主要缺点在更新时它们有时需要复制大批对象. 在某些情况下, 这可能很难表达, 也可能导致性能瓶颈. 因此, 库为不可变类提供可变替代方案的情况并不少见. 例如, StringBuilder 类是对不可变类 String 的可变替代. 我们将在 18 章中为您提供有关在 Scala 中设计可变对象的更多信息.

在 Java 中, 类的构造函数可以使用参数; 而在 Scala 中, 类可以直接获取参数. Scala 表示法更简洁, 参数可以直接在类的主体中使用; 没有必要定义字段并编写构造函数将参数赋值给字段.

``` scala
class Rational(n: Int, d: Int) {
  println("Created " + n + "/" + d)
}
```

### 6.3 REIMPLEMENTING THE TOSTRING METHOD

``` scala
class Rational(n: Int, d: Int) {
  override def toString = n + "/" + d
}
```

### 6.4 CHECKING PRECONDITIONS

前提条件是对传递给方法或构造函数的值的约束, 这是调用者必须满足的要求.

``` scala
class Rational(n: Int, d: Int) {
  require(d != 0)
  override def toString = n + "/" + d
}
```

### 6.5 ADDING FIELDS

为了保持 Rational 的不可变特性, add 方法不能将传递的 Rational 添加到自身. 相反, 它必须创建一个新的 Rational 来保存总和, 然会返回.

``` scala
class Rational(n: Int, d: Int) { // This won't compile
  require(d != 0)
  override def toString = n + "/" + d
  def add(that: Rational): Rational = new Rational(n * that.d + that.n * d, d * that.d)
}
```

尽管类参数 n 和 d 在 add 方法的代码中可见, 但是只有对象内部的方法才能访问它们的值. 因此, 当您在 add 的实现中说 n 或 d 时, 编译器很乐意为您提供这些类参数的值. 但它不会让你说 that.n 或 that.d, 因为 that 并没有引用调用 add 的Rational 对象. 要访问其他类的 n 和 d, 您需要将它们放入类的定义中.

``` scala
class Rational(n: Int, d: Int) {
  require(d != 0)
  val numer: Int = n
  val denom: Int = d
  override def toString = numer + "/" + denom
  def add(that: Rational): Rational =
    new Rational(
      numer * that.denom + that.numer * denom,
      denom * that.denom
    )
}
```

<p align="left" style="color:red;"><font size=5><b>注: 类参数的可见性问题. </b></font></p>

### 6.6 SELF REFERENCES

this

### 6.7 AUXILIARY CONSTRUCTORS

在 Scala 中, 除主要构造函数之外的构造函数称为辅助构造函数(auxiliary constructors). Scala 中的辅助构造函数以 def this(...) 开头.

``` scala
class Rational(n: Int, d: Int) {

  require(d != 0)

  val numer: Int = n
  val denom: Int = d

  def this(n: Int) = this(n, 1) // auxiliary constructor

  override def toString = numer + "/" + denom

  def add(that: Rational): Rational =
    new Rational(
      numer * that.denom + that.numer * denom,
      denom * that.denom
    )
}
```

在 Scala 中, 每个辅助构造函数必须首先调用同一个类中的其他构造函数. 换句话说, 每个 Scala 类中每个辅助构造函数中的第一个语句都将具有 "this(...)" 形式. 调用的构造函数可以是主构造函数(如 Rational 示例中所示), 也可以是另一个辅助构造函数. 此规则的效果是 Scala 中的每个构造函数调用最终都会调用类的主构造函数. 因此, 主构造函数是构建类的唯一入口点.

在 Java 中, 构造函数必须调用同一个类的另一个构造函数, 或者直接调用超类的构造函数作为其第一个操作. 在 Scala 类中, 只有主构造函数可以调用超类构造函数.

### 6.8 PRIVATE FIELDS AND METHODS

### 6.9 DEFINING OPERATORS

### 6.10 IDENTIFIERS IN SCALA

字母数字标识符以字母或下划线开头, 后面可以跟其他字母, 数字或下划线. \$ 字符也算作一个字母; 但是, 它被保留, 用于 Scala 编译器生成的标识符. 用户程序中的标识符不应包含 \$ 字符, 即使它可以通过编译; 如果非要这样做, 这可能会导致与 Scala 编译器生成标识符的名称冲突.

Scala 遵循 Java 使用 camel-case 标识符的惯例, 例如 toString 和 HasSet. 虽然下划线在标识符中是合法的, 但它们通常不会在 Scala 程序中使用, 部分是为了与 Java 保持一致, 还因为下划线在 Scala 代码中有许多其他非标识符用法. 因此, 最好避免使用 to_string, _init_ 或 name_ 等标识符. 字段, 方法参数, 局部变量和函数的 Camel-case 名称应以小写字母开头, 例如: length, flatMap 和 s. 类和 traits 的 Camel-case  名称应以大写字母开头, 例如: BigInt, List 和 UnbalancedTreeMap.

在 Scala 中, 常量不仅仅意味着 val. 即使 val 在初始化后就保持不变, 它仍然是一个变量. 例如, 方法参数是 val, 但每次调用该方法时, 这些 val 可以包含不同的值.

<p align="left" style="color:red;"><font size=5><b>注: val 是初始化不可变. 常量是编译时就已经不可变了. </b></font></p>

在 Java 中, 惯例是将常量名称全部大写, 下划线分隔单词, 例如 MAX_VALUE 或 PI. 在 Scala 中, 约定仅仅将第一个字符大写.

Scala 编译器将在内部 "修改" 运算符标识符, 将它们转换为嵌入了 \$ 字符的合法Java标识符. 例如, 标识符 :-> 将在内部表示为 \$colon\$minus\$greater. 如果您想从 Java 代码访问此标识符, 则需要使用此内部表示.

由于 Scala 中的运算符标识符可以是任意长度, 因此 Java 和 Scala 之间存在细微差别. 在 Java 中, 输入 x<-y 将被解析为四个词法符号, 因此它将等同于 x < - y. 在 Scala 中, <- 将被解析为单个标识符, 给出 x <- y. 如果你想要第一个解释, 你需要用空格分隔 < 和 - 字符. 这在实践中不太可能成为问题, 因为很少有人会在 Java 中编写 x <- y 而不在运算符之间插入空格或括号.

混合标识符由字母数字标识符组成, 后跟下划线和运算符标识符. 例如, 用作方法名称的 unary_+ 定义了一元 "+" 运算符. 或者, myvar_= 用作方法名称定义赋值运算符. 此外, 像 myvar_= 一样的混合标识符由 Scala 编译器生成以支持属性(更多内容见 18 章).

文字标识符是 \`...\` 中包含的任意字符串. 文字标识符的一些示例是: \`x\` \`<clinit>\` \`yield\`

我们的想法是, 您可以将运行时接受的任何字符串作为反引号之间的标识符. 结果始终是 Scala 标识符. 即使反引号中包含的名称是 Scala 保留字也可以工作. 一个典型的用例是访问 Java 的 Thread 类中的静态 yield 方法. 你不能写 Thread.yield() 因为 yield 是 Scala 中的保留字. 但是, 您仍然可以在反引号中命名方法, 例如 Thread.\`yield\`().

### 6.11 METHOD OVERLOADING

### 6.12 IMPLICIT CONVERSIONS

您可以创建一个隐式转换, 在需要时自动将整数转换为有理数.

  scala> implicit def intToRational(x: Int) = new Rational(x)

  scala> val r = new Rational(2,3)
  r: Rational = 2/3

  scala> 2 * r
  res15: Rational = 4/3

要使隐式转换起作用, 它必须在 scope 内. 如果将隐式方法定义放在 Rational 类中, 它将不在解释器的范围内. 现在, 您需要直接在解释器中定义它.

### 6.13 A WORD OF CAUTION

正如本章所述, 使用运算符名称和定义隐式转换创建方法可以帮助您设计客户端代码简洁易懂的库. Scala 为您提供了设计这些易于使用的库的强大功能. 但请记住, 能力越强, 责任越大.

如果使用不当, 操作符方法和隐式转换都会导致难以阅读和理解的客户端代码. 因为隐式转换是由编译器隐式应用的, 而不是在源代码中明确写出, 所以对于客户端程序员来说, 应用隐式转换是不感知的. 虽然运算符方法通常会使客户端代码更简洁, 但它们只会使客户端程序员能够识别并记住每个运算符的含义, 使其更具可读性.

在设计库时, 您应该牢记的目标不仅仅是启用简洁的客户端代码, 而是可读, 易懂的客户端代码. 简洁性通常是可读性的重要组成部分, 但是你可以把它简洁做到极致. 通过设计能够实现高雅简洁且同时可理解的客户端代码的库, 从而帮助客户端程序员高效地工作.

### 6.14 CONCLUSION

## Chapter 7 Built-in Control Structures

您会注意到的一件事是, 几乎所有 Scala 的控制结构都会产生一些值. 这是函数式语言所采用的方法, 其中程序被视为值的计算, 因此程序的组件也应该计算值. 在命令式语言中, 函数调用可以返回一个值, 即使被调用函数更新作为参数传进来的输出变量也可以正常工作. 此外, 命令式语言通常具有三元运算符(ternary operator, 例如 C, C++ 和 Java 的 ?: 运算符), 其行为与 if 完全相同, 但会返回一个值. Scala 采用这种三元运算符模型, 只是将其称为 if. 换句话说, Scala 的 if 可以产生一个值. 除此以外, Scala 中的 for, try 和 match 也一样.

程序员可以使用这些结果值来简化代码, 就像它们使用函数的返回值一样. 如果没有此工具, 程序员必须创建临时变量, 以保存在控制结构内计算的结果. 删除这些临时变量会使代码变得更简单, 并且它还可以防止在一个分支中设置变量但又忘记在另一个分支中设置变量的许多错误.

### 7.1 IF EXPRESSIONS

命令式风格:

``` scala
var filename = "default.txt"
if (!args.isEmpty)
  filename = args(0)
```

Scala 中 if 有返回值:

``` scala
val filename =
  if (!args.isEmpty) args(0)
  else "default.txt"
```

上例中使用 val 是一种函数式样式, 它可以像 Java 中的 final 变量一样帮助您. 它告诉读者变量永远不会改变, 从而使他们不必扫描变量范围内的所有代码, 看它是否会发生变化.

使用 val 而不是 var 的第二个好处是它更好地支持等式推理(equational reasoning). 假设表达式没有副作用, 变量等于计算它的表达式. 因此, 无论何时你想用变量时, 您都可以用表达式来代替. 例如, 你可以写下这个, 而不是 println(filename):

``` scala
println(if (!args.isEmpty) args(0) else "default.txt")
```

### 7.2 WHILE LOOPS

while 和 do-while 结构被称作 "循环", 而不是表达式, 因为它们不会产生有意义的值. "循环" 产生的结果类型是 Unit. 事实证明存在一个值(实际上只有一个值), 其类型为 Unit. 它被称为 unit value 并写做 (). () 的存在是 Scala Unit 与 Java void 的区别.

  scala> def greet() = { println("hi") }
  greet: ()Unit

  scala> () == greet()
  hi
  res0: Boolean = true

因为 greet 函数体中没有等号(没有求值操作), 所以 greet 被定义为返回类型为 Unit 的过程. 因此 greet 函数返回 unit value. 这在下一行中得到证实: 将 greet 函数的返回值与 () 进行比较, 结果为 true.

返回 Unit 值的另一个结构是重新给变量赋值. 例如, 如果您尝试使用以下 while 循环来读取 Scala 中的行, 您将遇到麻烦:

``` scala
var line = ""
while ((line = readLine()) != "") // This doesn't work!
  println("Read: " + line)
```

编译此代码时, Scala 会给出一个警告, 即使用 != 比较 Unit 和 String 类型的值将始终为 true. 而在 Java 中, 赋值操作的结果总是得到所赋的值(在本例中是标准输入中的一行), 而在 Scala 赋值中总是得到 unit 值, (). 因此, 赋值 "line = readLine()" 的值将始终为 () 且永远不会为 "". 因此, 此 while 循环的条件永远不会为 false, 因此循环将永远不会终止.

<p align="left" style="color:red;"><font size=5><b>注: scala 中, readLine() 返回的是 unit, 而不是 java 中的字符串. 感觉 scala 中所有 io 相关的函数返回的都是 unit. </b></font></p>

因为 while 循环不返回任何值, 所以它常常被纯函数式语言排除在外. 这些语言只有表达式, 而不包括循环. 尽管如此, Scala 包含 while 循环, 因为有时候命令式解决方案可读性更强. 例如, 如果要编写一个重复某个过程直到某些条件发生变化的算法, while 循环可以直接表达它, 如果使用函数递归来代替, 对于某些读者来说可能是拗口的.

例如，代码清单 7.4 显示了另一种确定两个数字的最大公约数的方法. 给定 x 和 y 的两个相同值, 清单 7.4 中显示的 gcd 函数将返回与 gcdLoop 函数相同的结果, 如清单 7.2 所示. 这两种方法的区别在于 gcdLoop 是以命令式的方式编写的, 使用了变量和 while 循环.

而 gcd 是以更偏向函数式的风格编写的, 涉及递归(gcd 调用自身)并且不需要变量.

``` scala
def gcdLoop(x: Long, y: Long): Long = {
  var a = x
  var b = y
  while (a != 0) {
    val temp = a
    a = b % a
    b = temp
  }
  b
}
```

``` scala
def gcd(x: Long, y: Long): Long =
  if (y == 0) x else gcd(y, x % y)
```

通常情况下, 我们建议您以与对待变量相同的方式来对待代码中的 while 循环. 实际上, 虽然循环和变量通常是齐头并进的. 因为虽然 while 循环不会产生值, 但为了控制程序跳出循环, while 循环通常需要更新变量或执行 I/O. 您可以在前面显示的 gcdLoop 示例中看到此操作. while 循环会更新变量 a 和 b. 因此, 我们建议您对代码中的 while 循环持怀疑态度. 如果没有使用 while 或 do-while 循环的良好理由, 试着找到一种方法来代替它们.

### 7.3 FOR EXPRESSIONS

**generator**

``` scala
val filesHere = (new java.io.File(".")).listFiles

for (file <- filesHere)         //generator
  println(file)
```

**Filtering**

``` scala
val filesHere = (new java.io.File(".")).listFiles

for (file <- filesHere if file.getName.endsWith(".scala"))
  println(file)
```

**Nested iteration**

``` scala
def fileLines(file: java.io.File) =
  scala.io.Source.fromFile(file).getLines().toList

def grep(pattern: String) =
  for (
    file <- filesHere
    if file.getName.endsWith(".scala");
    line <- fileLines(file)
    if line.trim.matches(pattern)
  ) println(file + ": " + line.trim)

grep(".*gcd.*")
```

如果您愿意, 可以使用花括号而不是括号来围绕生成器和过滤器. 使用花括号的一个优点是你可以省略使用括号时所需的一些分号, 因为如第 4.2 节所述, Scala 编译器在括号内不会推断出分号.

** Mid-stream variable bindings **

``` scala
def grep(pattern: String) =
  for (
    file <- filesHere
    if file.getName.endsWith(".scala");
    line <- fileLines(file)
    trimmed = line.trim
    if trimmed.matches(pattern)
  ) println(file + ": " + trimmed)

grep(".*gcd.*")
```

**Producing a new collection**

``` scala
def scalaFiles =
  for {
    file <- filesHere
    if file.getName.endsWith(".scala")
  } yield file
```

### 7.4 EXCEPTION HANDLING WITH TRY EXPRESSIONS

**Catching exceptions**

``` scala
import java.io.FileReader
import java.io.FileNotFoundException
import java.io.IOException

try {
  val f = new FileReader("input.txt")
// Use and close file
} catch {
  case ex: FileNotFoundException => // Handle missing file
  case ex: IOException => // Handle other I/O error
}
```

与 Java 不同, Scala 不要求您在 catch 中列出待检查的异常或在 throws 子句中声明它们. 如果您希望使用 @throws 注释, 则可以声明 throws 子句, 但这不是必需的.

**Yielding a value**

与在 Java 中一样, 如果 finally 子句包含显式返回语句或抛出异常, 则该返回值或异常将 "覆盖" 源自 try 块或其 catch 子句之一的任何先前子句.

``` scala
def f(): Int = try return 1 finally return 2
```

调用上面的函数将返回 2.

``` scala
def g(): Int = try 1 finally 2
```

调用上面的函数将返回 1.

### 7.5 MATCH EXPRESSIONS

``` scala
val firstArg = if (args.length > 0) args(0) else ""
firstArg match {
  case "salt" => println("pepper")
  case "chips" => println("salsa")
  case "eggs" => println("bacon")
  case _ => println("huh?")      // default case
}
```

与 Java 的 switch 语句有一些重要的区别. 一个是在 Scala 中可以使用任何类型的常量以及其他东西, 而不仅仅是 Java  case 语句支持的整数类型, 枚举和字符串常量. 另一个区别是 case 的末尾都没有 break.

``` scala
val firstArg = if (!args.isEmpty) args(0) else ""
val friend =
  firstArg match {
    case "salt" => "pepper"
    case "chips" => "salsa"
    case "eggs" => "bacon"
    case _ => "huh?"
  }

println(friend)
```

### 7.6 LIVING WITHOUT BREAK AND CONTINUE

最简单的方法是用布尔变量替换 break, 用 if 替换 continue.

``` java
int i = 0; // This is Java
boolean foundIt = false;
while (i < args.length) {
  if (args[i].startsWith("-")) {
    i = i + 1;
    continue;
  }
  if (args[i].endsWith(".scala")) {
    foundIt = true;
    break;
  }

  i = i + 1;
}
```

``` scala
var i = 0
var foundIt = false

while (i < args.length && !foundIt) {
  if (!args(i).startsWith("-")) {
    if (args(i).endsWith(".scala"))
      foundIt = true
  }
  i = i + 1
}
```

``` scala
def searchFrom(i: Int): Int =
  if (i >= args.length) -1
  else if (args(i).startsWith("-")) searchFrom(i + 1)
  else if (args(i).endsWith(".scala")) i
  else searchFrom(i + 1)

val i = searchFrom(0)
```

Scala 编译器实际上不会将上面的代码编译成递归调用. 因为所有递归调用都处于函数末尾, 所以编译器的输出类似于使用 while 循环的代码. 每个递归调用的实现都是跳回函数的开头. 尾递归优化在 8.9 节讨论.

``` scala
import scala.util.control.Breaks._
import java.io._

val in = new BufferedReader(new InputStreamReader(System.in))

breakable {
  while (true) {
    println("? ")
    if (in.readLine() == "") break
  }
}
```

### 7.7 VARIABLE SCOPE

### 7.8 REFACTORING IMPERATIVE-STYLE CODE

``` scala
// Returns a row as a sequence
def makeRowSeq(row: Int) =
  for (col <- 1 to 10) yield {
    val prod = (row * col).toString
    val padding = " " * (4 - prod.length)
    padding + prod
  }

// Returns a row as a string
def makeRow(row: Int) = makeRowSeq(row).mkString

// Returns table as a string with one row per line
def multiTable() = {

  val tableSeq = // a sequence of row strings
    for (row <- 1 to 10)
    yield makeRow(row)

  tableSeq.mkString("\n")
}
```

### 7.9 CONCLUSION

## Chapter 8 Functions and Closures

### 8.1 METHODS

``` scala
import scala.io.Source

object LongLines {

  def processFile(filename: String, width: Int) = {
    val source = Source.fromFile(filename)
    for (line <- source.getLines())
      processLine(filename, width, line)
  }

  private def processLine(filename: String,
    width: Int, line: String) = {

    if (line.length > width)
      println(filename + ": " + line.trim)
  }
}
```

在方法中定义方法:

``` scala
def processFile(filename: String, width: Int) = {

  def processLine(filename: String,
    width: Int, line: String) = {
  if (line.length > width)
    println(filename + ": " + line.trim)
  }

  val source = Source.fromFile(filename)
  for (line <- source.getLines()) {
    processLine(filename, width, line)
  }
}
```

### 8.3 FIRST-CLASS FUNCTIONS

  scala> increase = (x: Int) => x + 9999
  increase: Int => Int = <function1>
  
  scala> increase(10)
  res1: Int = 10009

### 8.4 SHORT FORMS OF FUNCTION LITERALS

  scala> someNumbers.filter((x) => x > 0)
  res5: List[Int] = List(5, 10)

### 8.5 PLACEHOLDER SYNTAX

  scala> someNumbers.filter(_ > 0)
  res7: List[Int] = List(5, 10)

  scala> val f = (_: Int) + (_: Int)
  f: (Int, Int) => Int = <function2>
  scala> f(5, 10)
  res9: Int = 15

### 8.6 PARTIALLY APPLIED FUNCTIONS

  scala> sum(1, 2, 3)
  res10: Int = 6

  scala> val a = sum _
  a: (Int, Int, Int) => Int = <function3>

  scala> a(1, 2, 3)
  res11: Int = 6

  scala> val b = sum(1, _: Int, 3)
  b: Int => Int = <function1>

  scala> b(2)
  res13: Int = 6

### 8.7 CLOSURES

### 8.8 SPECIAL FUNCTION CALL FORMS

**Repeated parameters**(可变参数)

"String*" 实际上是 Array[String]

  scala> def echo(args: String*) =
           for (arg <- args) println(arg)
  echo: (args: String*)Unit


  scala> val arr = Array("What's", "up", "doc?")
  arr: Array[String] = Array(What's, up, doc?)

  scala> echo(arr)
  <console>:10: error: type mismatch;
  found : Array[String]

  scala> echo(arr: _*)
  What's
  up
  doc? 

**Named arguments**

  scala> def speed(distance: Float, time: Float): Float =
           distance / time
  speed: (distance: Float, time: Float)Float

  scala> speed(100, 10)
  res27: Float = 10.0

  scala> speed(distance = 100, time = 10)
  res28: Float = 10.0

  scala> speed(time = 10, distance = 100)
  res29: Float = 10.0

也可以混合使用位置和命名参数. 在这种情况下, 位置参数首先出现. 命名参数最常与默认参数值结合使用.

**Default parameter values**

``` scala
def printTime2(out: java.io.PrintStream = Console.out, divisor: Int = 1) =
  out.println("time = " + System.currentTimeMillis()/divisor)
```

### 8.9 TAIL RECURSION

尾调用优化仅限于方法或嵌套函数直接将其自身称为最后一个操作的情况, 而不通过函数值或其他中介.

###　8.10 CONCLUSION

foreach 方法定义在 Traversable trait 中

## Chapter 9 Control Abstraction

### 9.1 REDUCING CODE DUPLICATION

高阶函数 - 将函数作为参数的函数 - 为您提供了压缩和简化代码的额外机会.

``` scala
def filesMatching(query: String, matcher: (String, String) => Boolean) = {
  for (file <- filesHere; if matcher(file.getName, query))
    yield file
}
```

``` scala
object FileMatcher {
  private def filesHere = (new java.io.File(".")).listFiles

  private def filesMatching(matcher: String => Boolean) =
    for (file <- filesHere; if matcher(file.getName))
      yield file

  def filesEnding(query: String) =
    filesMatching(_.endsWith(query))

  def filesContaining(query: String) =
    filesMatching(_.contains(query))

  def filesRegex(query: String) =
    filesMatching(_.matches(query))
}
```

### 9.2 SIMPLIFYING CLIENT CODE

``` scala
def containsNeg(nums: List[Int]): Boolean = {
  var exists = false
  for (num <- nums)
    if (num < 0)
      exists = true
  exists
}
```

``` scala
def containsNeg(nums: List[Int]): Boolean = {
  var exists = false
  for (num <- nums)
    if (num % 2 == 1)
      exists = true
  exists
}
```

``` scala
def containsOdd(nums: List[Int]) = nums.exists(_ % 2 == 1)
```

### 9.3 CURRYING

  scala> def plainOldSum(x: Int, y: Int) = x + y
  plainOldSum: (x: Int, y: Int)Int

  scala> plainOldSum(1, 2)
  res4: Int = 3

  scala> def curriedSum(x: Int)(y: Int) = x + y
  curriedSum: (x: Int)(y: Int)Int

  scala> curriedSum(1)(2)
  res5: Int = 3

当你调用柯里化后的 sum (curriedSum)时, 你实际上得到了两个传统的函数调用. 第一个函数调用采用名为 x 的单个 Int 参数, 并返回一个单参数的函数. 第二个函数采用 Int 参数 y.

### 9.4 WRITING NEW CONTROL STRUCTURES

``` scala
def withPrintWriter(file: File, op: PrintWriter => Unit) = {
  val writer = new PrintWriter(file)
  try {
    op(writer)
  } finally {
    writer.close()
  }
}

withPrintWriter(
  new File("date.txt"),
  writer => writer.println(new java.util.Date)
)
```

这种技术称为贷款模式, 因为控制抽象函数(如 withPrintWriter)打开资源并将其 "贷款" 给函数. 例如, 在上一个示例中, withPrintWriter 将一个 PrintWriter 借给函数 op. 当函数完成时, 它表示它不再需要 "借用" 资源. 然后在 finally 块中关闭资源, 无论函数是正常返回还是抛出异常.

``` scala
def withPrintWriter(file: File)(op: PrintWriter => Unit) = {
  val writer = new PrintWriter(file)
  try {
    op(writer)
  } finally {
    writer.close()
  }
}

val file = new File("date.txt")

withPrintWriter(file) { writer =>
  writer.println(new java.util.Date)
}
```

在此示例中, 第一个参数列表(包含一个 File 参数)被括号括起来. 第二个参数列表包含一个函数参数, 由大括号括起来.

### 9.5 BY-NAME PARAMETERS(传名参数)

传值参数在函数调用之前表达式会被求值, 例如 Int, Long 等数值参数类型; 传名参数在函数调用前表达式不会被求值, 而是会被包裹成一个匿名函数作为函数参数传递下去, 例如参数类型为无参函数的参数就是传名参数.

在函数声明时, 参数类型中添加一个 =>, 参数的类型就变成了无参函数, 类型为 () => String, 按照 Scala 针对无参函数的简化规则, 可以省略 () 写作 => String. 因为参数的类型是无参函数, 所以此处是按名传递。

如果参数类型是无参函数, 则按名传递, 否则按值传递. 注意, 如果参数类型是函数类型, 但不是无参函数, 还是按值传递.

传值参数:

``` scala
object Test {
  def invode(f: String => Int => Long) = {
    println("call invoke")
    f("1")(2)
  }
  def curry(s: String)(i: Int): Long = {
    s.toLong + i.toLong
  }
  def main(args: Array[String]) {
    invode{println("eval parameter expression");curry}
  }
}
```

传名参数:

``` scala
object Test {
  def invode(f: => String => Int => Long) = {  // 参数 f 的类型有变化
    println("call invoke")
    f("1")(2)
  }
  def curry(s: String)(i: Int): Long = {
    s.toLong + i.toLong
  }
  def main(args: Array[String]) {
    invode{println("eval parameter expression");curry}
  }
}
```

## Chapter 10 Composition and Inheritance

组合(Composition)意味着一个类引用另一个类, 使用引用的类来帮助它完成其任务. 继承是超类/子类关系.

### 10.1 A TWO-DIMENSIONAL LAYOUT LIBRARY

组合器(combinators)的参数都是一个函数, 这个函数的输入输出都是列表元素, 它们将某些域的元素组合成新元素.

### 10.2 ABSTRACT CLASSES

抽象类不能实例化, 抽象方法没有函数体, 不用 abstract 声明.

``` scala
abstract class Element {
  def contents: Array[String]
}
```

### 10.3 DEFINING PARAMETERLESS METHODS

``` scala
abstract class Element {
  def contents: Array[String]              // 因为函数没有参数, 所以省略 ()
  def height: Int = contents.length
  def width: Int = if (height == 0) 0 else contents(0).length
}
```

``` scala
abstract class Element {
  def contents: Array[String]
  val height = contents.length
  val width = if (height == 0) 0 else contents(0).length
}
```

从客户的角度来看, 这两对定义完全相同. 唯一的区别是访问类的属性可能比调用类的方法稍快, 因为类的属性是在初始化类时算好的, 而不是在每次方法调用时都要重新计算. 另一方面, 属性在每个 Element 对象中需要额外的内存空间.

如果函数没有副作用, 则可以用其结果替换函数调用, 而不改变程序的行为. 这意味着没有参数或副作用的函数在语义上等同于保存该函数返回值的 val. 由于这个特性, 随着类的发展, 程序员可能会在使用 val 或调用函数之间来回切换, 因为方便或效率的要求.

由于您可以省略括号(parentheses), 这意味着调用类似 Element.height 的代码不需要知道也不关心 height 是函数还是 val. 因此, Element 类的实现者可以在两者之间自由地进行切换, 而无需更改任何调用代码(尽管需要重新编译). 它稳定了类的对外接口. 例如, 您可以通过调用 List 上的 size 方法来获取 size, 函数复杂度可能是 O(n), 然后出于效率的原因将 size 更改为 val.

用空括号定义的方法, 例如 def height():Int, 称为 **empty-paren** 方法. 如果函数调用存在副作用, 约定建议保留括号, 以明确该类成员肯定是函数调用, 因此可能不是引用透明的. 了解函数是否产生副作用非常重要, 因此可以避免重复调用产生副作用. 如果你不关心它是否是一个函数, 你最好把它当成不是.

总而言之, Scala 鼓励将不带参数且没有副作用的方法定义为无参数(parameterless)方法, 即省略空括号. 另一方面，您永远不应该定义一个没有括号的但有副作用的方法, 因为那个方法的调用看起来像一个字段选择.

### 10.4 EXTENDING CLASSES

``` scala
class ArrayElement(conts: Array[String]) extends Element {
  def contents: Array[String] = conts
}
```

如果省略 extends 子句, Scala 编译器会隐式假设您的类继承自 scala.AnyRef, 它在 Java 平台上与类 java.lang.Object 相同.

### 10.5 OVERRIDING METHODS AND FIELDS

在 Scala 中, 字段和方法属于同一名称空间. 这使得字段可以覆盖无参数方法. 另一方面, 在 Scala 中, 禁止在同一个类中定义具有相同名称的字段和方法, 而在 Java 中允许这样做.

``` java
// This is Java
class CompilesFine {
  private int f = 0;

  public int f() {
    return 1;
  }
}
```

``` scala
class WontCompile {
  private var f = 0 // Won't compile, because a field
  def f = 1 // and method have the same name
}
```

通常, Scala 只有两个用于定义的命名空间来, 而 Java 有四个. Java 的四个名称空间是字段, 方法, 类型和包. 相比之下, Scala 的两个名称空间是:

* 值(字段, 方法, 包和单例对象)
* 类型(class 和 trait 名称)

正是为了让您可以使用 val 重载无参数方法, Scala 将字段和方法放入同一名称空间, 这是 Java 无法做到的.

### 10.6 DEFINING PARAMETRIC FIELDS

``` scala
class ArrayElement(conts: Array[String]) extends Element {
  def contents: Array[String] = conts
}
```

``` scala
class ArrayElement(val contents: Array[String]) extends Element
```

请注意, 现在 contents 参数以 val 为前缀. 这是一种速记, 它同时定义了具有相同名称的参数和字段. 具体来说, 类 ArrayElement 现在具有(不可重新分配的)字段内容, 可以从类外部访问. 该字段使用参数值初始化.

您还可以使用 var 为 class 参数添加前缀, 在这种情况下, 相应的字段可以重新分配.

``` scala
class Cat {
  val dangerous = false
}

class Tiger(
  override val dangerous: Boolean,
  private var age: Int
) extends Cat
```

### 10.7 INVOKING SUPERCLASS CONSTRUCTORS

### 10.8 USING OVERRIDE MODIFIERS

Scala 要求覆盖父类中具体成员的所有成员使用此类修饰符(override). 如果类成员实现具有相同名称的抽象类成员, 则修饰符 override 是可选的. 如果类成员没有覆盖或实现基类中的某个类成员, 则禁止使用 override 修饰符.

### 10.9 POLYMORPHISM AND DYNAMIC BINDING

polymorphism 多态

``` scala
class UniformElement(
  ch: Char,
  override val width: Int,
  override val height: Int
) extends Element {
  private val line = ch.toString * width
  def contents = Array.fill(height)(line)
}
```

``` scala
val e1: Element = new ArrayElement(Array("hello", "world"))
val ae: ArrayElement = new LineElement("hello")
val e2: Element = ae
val e3: Element = new UniformElement('x', 2, 3)
```

如果检查继承层次结构, 您会发现在上面代码中四个 val 定义语句中, 等号右侧的表达式类型在继承树中低于等号左侧初始化的 val 的类型.(父类可以转化为子类).

然而, 故事的另一半是基于变量或表达式的方法调用是动态绑定(dynamically bound)的. 这意味着调用的实际方法是在运行时根据对象的类确定的, 而不是根据变量或表达式的类型. 为了演示这种行为, 我们将暂时从 Element 类中删除所有现有成员, 并将一个名为 demo 的方法添加到 Element. 我们将在 ArrayElement 和 LineElement 类中覆盖 demo 方法, 但不会覆盖 UniformElement 类:

``` scala
abstract class Element {
  def demo() = {
    println("Element's implementation invoked")
  }
}

class ArrayElement extends Element {
  override def demo() = {
    println("ArrayElement's implementation invoked")
  }
}

class LineElement extends ArrayElement {
  override def demo() = {
    println("LineElement's implementation invoked")
  }
}

// UniformElement inherits Element's demo
class UniformElement extends Element
```

``` scala
def invokeDemo(e: Element) = {
  e.demo()
}
```

  scala> invokeDemo(new ArrayElement)
  ArrayElement's implementation invoked

  scala> invokeDemo(new LineElement)
  LineElement's implementation invoked

  scala> invokeDemo(new UniformElement)
  Element's implementation invoked

### 10.10 DECLARING FINAL MEMBERS

final 确保子类不能覆盖成员函数 demo.

``` scala
class ArrayElement extends Element {
  final override def demo() = {
    println("ArrayElement's implementation invoked")
  }
}
```

final 确保 ArrayElement 类不能被继承.

``` scala
final class ArrayElement extends Element {
  override def demo() = {
    println("ArrayElement's implementation invoked")
  }
}
```

### 10.11 USING COMPOSITION AND INHERITANCE

组合(Composition)和继承(inheritance)是根据一个现有类定义新类的两种方法. 如果你所追求的主要是代码重用, 你通常应该更喜欢组合而不是继承. 只有继承会受到脆弱基类问题的影响, 这个问题是指更改超类会无意中破坏子类.

你可以问自己一个关于继承关系的问题 - 它是否模拟了一个 is-a 关系.例如, 可以合理地说 ArrayElement 是一个 Element. 另一个问题是客户端是否希望将子类类型视为超类类型. 对于 ArrayElement, 我们确实希望客户端希望将 ArrayElement 当成 Element 来使用.

实际上, 我们将 LineElement 定义为 ArrayElement 的子类主要是为了重用 ArrayElement 的内容定义. 因此, 将 LineElement 定义为 Element 的直接子类可能会更好, 如下所示:

改写成组合关系如下:

``` scala
class LineElement(s: String) extends Element {
  val contents = Array(s)
  override def width = s.length
  override def height = 1
}
```

### 10.12 IMPLEMENTING ABOVE, BESIDE, AND TOSTRING





### 10.13 DEFINING A FACTORY OBJECT


### 10.14 HEIGHTEN AND WIDEN


### 10.15 PUTTING IT ALL TOGETHER


### 10.16 CONCLUSION


## Chapter 11 Scala's Hierarchy


### 11.1 SCALA'S CLASS HIERARCHY























