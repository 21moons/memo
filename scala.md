Programming in Scala Third Edition Martin Odersky, Lex Spoon, Bill Vennerss


* scala 即 scalable language, 它是面向对象和函数式编程的融合

* 用 scala 编写的程序易裁剪, 因为很多数据结构和类型都是用库的方式实现的, 比如说 BigInt

* Immutable data structures(不可变数据结构)是函数式编程的基石,另外一个特征是函数是第一类公民, 可以是参数，变量或返回值
  第一条的另外一种表述方法是方法没有任何 side effects, 他们仅仅从运行环境中获取参数和返回结果

* scala 支持类型推断

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

![](https://raw.githubusercontent.com/21moons/memo/master/res/img/scala/basic_form_of_a_function_definition.png)
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

![](https://raw.githubusercontent.com/21moons/memo/master/res/img/scala/syntax_of_a_function_literal.png)
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






