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

``` scala
val greetStrings = new Array[String](3)
greetStrings(0) = "Hello"
greetStrings(1) = ", "
greetStrings(2) = "world!\n"

for (i <- 0 to 2)
  print(greetStrings(i))
```






