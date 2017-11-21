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

* Traits 类似于 java 中的 interface, can then be mixed together