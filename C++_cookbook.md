# C++11 的新特性

## type_traits

C++11 的 type_traits 头文件定义了很多类型检查相关的方法.

1. 在模板里可以通过 std::is_same 判断模板的类型, 从而实现对不同类型的区别对待.
2. 在堆类型要求不是非常严格的情况下, 可以使用 std::decay 把类型退化为基本形态, 结合 std::is_same 用, 可以判断出更多的情况.

## 右值引用

## 可变模版参数(variadic templates)

## decltype 关键字

编译时类型推导的出现正是为了泛型编程, 在非泛型编程中, 我们的类型都是确定的, 根本不需要再进行推导. 而编译时类型推导, 除了 auto 关键字, 还有 decltype.

decltype 与 auto 关键字一样, 用于进行编译时类型推导, 不过它与 auto 不同, decltype 的类型推导并不是像 auto 一样是从变量声明的初始化表达式获得变量的类型, 而是总是以一个普通表达式作为参数, 返回该表达式的类型, 而且 decltype 并不会对该表达式进行求值.

decltype 用法:

```cpp
int i = 4;
decltype(i) a; //推导结果为 int. a 的类型为 int.
```

* 泛型编程中结合auto，用于追踪函数的返回值类型

```cpp
template <typename _Tx, typename _Ty>
auto multiply(_Tx x, _Ty y)->decltype(_Tx*_Ty)
{
    return x*y;
}
```

* 重用匿名类型

```cpp
struct
{
    int d ;
    doubel b;
}anon_s;
decltype(anon_s) as;
```

* 与 using/typedef 合用, 用于定义类型

```cpp
using size_t = decltype(sizeof(0)); //sizeof(a) 的返回值为 size_t 类型
using ptrdiff_t = decltype((int*)0 - (int*)0);
using nullptr_t = decltype(nullptr);
```

```cpp
vector<int>vec;
typedef decltype(vec.begin()) vectype;
for (vectype i = vec.begin; i != vec.end(); i++)
{
    //...
}
```

decltype 推导规则:

1. 如果 e 是一个没有带括号的标记符表达式或者类成员访问表达式, 那么的 decltype(e) 就是 e 所命名的实体的类型. 此外, 如果 e 是一个被重载的函数, 则会导致编译错误.
2. 否则, 假设 e 的类型是 T, 如果 e 是一个将亡值(当一个右值准备完成初始化或赋值任务时, 它已经 "将亡"了), 那么 decltype(e) 为 T&&(右值引用).
3. 否则, 假设 e 的类型是 T, 如果 e 是一个左值, 那么 decltype(e) 为 T&.
4. 否则, 假设 e 的类型是 T, 则 decltype(e)为 T.

<p align="left" style="color:red;"><font size=5><b>注: 变量推导实际上是在变量之间构建了一种依赖关系, 方便变量类型变更. </b></font></p>

-------------------------------------------------------------------------------------------------------------------------------------

# C++ 术语

## C++ 值类型

表达式是指由运算符(operator)和运算对象(operand)构成的计算式. 表达式是可求值的, 对表达式求值将得到一个结果(result). 这个结果有两个属性: 类型和值类别(value categories). 值类型包括:

* 左值(left value,lvalue)
  能够用&取地址的表达式是左值表达式.
* 将亡值(expiring value,xvalue)
  "将亡值" 概念的产生, 是由右值引用的产生而引起的, 将亡值与右值引用息息相关. 包括返回右值引用的函数的调用表达式与转换为右值引用的转换函数的调用表达式.
* 纯右值(pure rvalue,pralue)
  本身就是赤裸裸的, 纯粹的字面值, 如 3,false. 或者求值结果相当于字面值或是一个不具名的临时对象.
  在 C++11 中, 我们用左值去初始化一个对象或为一个已有对象赋值时, 会调用拷贝构造函数或拷贝赋值运算符来拷贝资源, 而当我们用一个右值(包括纯右值和将亡值)来初始化或赋值时, 会调用移动构造函数或移动赋值运算符(有移动构造函数或移动赋值运算符可供调用, 或者 std::move(), static_cast<X&&>(x))来移动资源, 从而避免拷贝, 提高效率. 当该右值完成初始化或赋值的任务时, 它的资源已经移动给了被初始化者或被赋值者, 同时该右值也将会马上被销毁(析构). 也就是说, 当一个右值准备完成初始化或赋值任务时, 它已经 "将亡" 了.

其中, 左值和将亡值合称泛左值(generalized lvalue,glvalue), 纯右值和将亡值合称右值(right value,rvalue).

事实上, 无论是左值, 将亡值还是纯右值, 我们目前都没有一个精准的定义. 它们事实上表征了表达式的属性, 而这种属性的区别主要体现在使用上, 如能否做运算符的左操作数, 能否使用移动构造/赋值语义(关于移动语义, 在下的后续文章中会详细介绍)等.

## C++ 中的 struct 和 class

因为 C++ 的 class 真的只是个 struct, 只不过 C++ 编译器会在编译期对它做一些 "魔改" 动作而已.

对 C 来说, struct 可以放任何它支持的数据类型. 包括函数指针--后来 C++ 所谓的虚函数表, 很多编译器的实现, 就是一个指向另一个全局结构体的指针, 这个全局结构体里面是一条一条的函数指针, 指向对应类重载后的所谓 "虚函数". 至于各种所谓的成员函数, 实际上就是一个普通函数, 不过函数的第一个参数是 classXX 类型的 this 指针的.

换言之, struct 本身已经足以支持面向对象编程运行期需要的一切了; 我们只需要增加一些新的语法约定, 使得编译器可以帮助用户自动维护这些琐碎细节就行了--这正是  Bjarne Stroustrup 当年搞 Cfront 编译器的设计思路: 先把 C++ 代码自动魔改到 C, 然后调用 C 编译器完成编译. 后期虽然有了直接支持 C++ 的编译器(因为在 cfront 中加入异常支持失败), 但编译器关于 struct 的处理并没有本质的不同. 于是, 所谓 class, 其实就是加了某些外部支持的 struct, 除了编译期可能会被编译器魔改的面目全非外, 它和普通 struct 毫无差别.

这个支持一般来说应该是这样的(并不禁止编译器厂商选择其他方案):

1. 对普通成员函数, 为它自动添加 this 参数, 并在调用它时, 自动把 obj.method() 转换成 method(obj)格式; 并识别出函数中涉及的, 没有显式使用 this 的成员变量, 为它加上 this. 除此之外, 别的什么都不用做.

2. 对虚函数, 需要为继承链上的每个类产生一个 **全局结构体**, 在这个结构体里按次序安排指向该类所有虚函数的指针, 这就是虚函数表; 然后在类里添加一个指向属于自己的虚函数表的指针. 那么, 当用户调用某个对象的第 N 个虚函数时, 到虚函数表查找并获取第 N 个函数指针指向的内容; 然后类似调用普通成员函数一样, 把 obj.method() 转换成 method(obj) 格式, 多态就实现了.

当然, 除此之外, 还要在编译时执行权限检查, 避免非法访问类的 protect/private 成员(struct 默认权限是 public, class 是 private), 以及另外一些琐碎工作. 所以你看, C++ 的类归根结底, 它就是个 C 语言的 struct. 只是(相当于)在编译期做了些预处理而已--只要你在里面声明了成员函数, 就会自动触发预处理, 从而实现 "自动魔改你所定义的数据结构, 使其支持 OO 诸要素" 功能. 那么, 除了让 C 代码共用 C++ 里面定义的 struct 这个特殊场景外(想了解这类能兼容 C 的数据类型, 可搜索 POD 类型), 你说还有什么理由禁止 struct 拥有成员函数呢?

## 强制类型转换符

C++ 有四种强制类型转换符,分别是 dynamic_cast, const_cast, static_cast, reinterpret_cast. 其中 dynamic_cast 与运行时类型转换密切相关.

### const_cast

常量指针/引用被转化成非常量的指针，并且仍然指向原来的对象. 转换定义为常量的参数时, 可能会有不同的效果(error 或 warning).

### static_cast

* static_cast 作用和 C 语言风格强制转换的效果基本一样, 由于没有运行时类型检查来保证转换的安全性, 所以这类型的强制转换和C语言风格的强制转换都有安全隐患.
* 用于类层次结构中基类(父类)和派生类(子类)之间指针或引用的转换. 注意: 进行上行转换(把派生类的指针或引用转换成基类表示)是安全的; 进行下行转换(把基类指针或   引用转换成派生类表示)时, 由于没有动态类型检查, 所以是不安全的.
* 用于基本数据类型之间的转换, 如把 int 转换成 char, 把 int 转换成 enum. 这种转换的安全性需要开发者来维护.
* static_cast 不能转换掉原有类型的 const, volatile 或 __unaligned 属性.(前两种可以使用 const_cast 来去除)
* 在 c++ primer 中说道: c++ 的任何的隐式转换都是使用 static_cast 来实现.

### dynamic_cast

涉及到面向对象的多态性和程序运行时的状态, 也与编译器的属性设置有关. 所以不能完全使用 C 语言的强制转换替代, 它也是最常有用的, 最不可缺少的一种强制转换.
dynami_cast 在程序运行时对 "运行期类型识别"(Run-Time Type Identification, RTTI)进行了检查.

* dynamic_cast 转换符只能用于指针或者引用.
* dynamic_cast 的转换并不会总是成功的.
* dynamic_cast 只能用于含有虚函数的类.

比如如果指向派生类的基类 B 的指针 pb 想访问派生类 D 中的除虚函数之外的成员, 就需要把该指针转换为指向派生类 D 的指针, 以达到访问派生类 D 中特有的成员的目的, 比如派生类 D 中含有特有的成员函数 g(), 这时可以这样来访问该成员函数:

```cpp
dynamic_cast<D*>(pb)->g();
```

因为 dynamic_cast 转换后的结果是一个指向派生类的指针, 所以可以访问派生类中特有的成员. 但是该语句不影响原指针的类型, 即基类指针 pb 仍然是指向基类 B.

### reinterpret_cast

reinterpret_cast 是强制类型转换符用来处理无关类型转换的, 通常为操作数的位模式提供较低层次的重新解释! 但是他仅仅是重新解释了给出的对象的比特模型, 并没有进行二进制的转换!

## RTTI

RTTI(Run-Time Type Identification), 通过"运行时类型识别", 程序能够使用基类的指针或引用来检查这些指针或引用所指的对象的实际派生类型. RTTI 机制为每一个类型产生一个 type_info 类型的数据, 而 typeid 查询返回的变量相应 type_info 数据, 通过 name 成员函数返回类型的名称. RTTI 会导致运行时效率降低, 在泛型编程中, 我们需要在编译时确定类型, RTTI 并无法满足这样的要求.

RTTI 提供了以下两个非常有用的操作符:

1. typeid 操作符, 返回指针和引用所指的实际类型.
2. dynamic_cast 操作符, 将基类类型的指针或引用安全地转换为派生类型的指针或引用.

## 虚函数

虚函数的作用, 是为了实现多态性(Polymorphism), 多态性的目的是将接口与实现进行分离.

非类的成员函数不能定义为虚函数, 类的成员函数中静态成员函数和构造函数也不能定义为虚函数, 但可以将析构函数定义为虚函数.

类中若有虚函数存在, 编译器就在类中插入 vptr 指针, 指向虚函数地址表 vtbl. 假如继承该类的子类定义并实现了一个同名并具有同样函数签名(function siguature)的方法, 那么子类的虚函数表会替换该函数地址为新地址.

```cpp
class A
{
    public:
        void print()
        {
            cout<<"This is A"<<endl;
        }
};
class B : public A
{
    public:
        void print()
        {
            cout<<"This is B"<<endl;
        }
};

int main()
{
    A a;
    B b;
    A *p1 = &a;  
    A *p2 = &b;   // B 类引用视为 A 类指针
    p1->print();
    p2->print();  // 视为 A 类指针
    return 0;
}
```

上面代码的运行结果是输出两个 "This is A".

```cpp
class A
{
    public:
        virtual void print(){cout<<"This is A"<<endl;}
};
class B : public A
{
    public:
    void print(){cout<<"This is B"<<endl;} // 派生类对应函数也是虚函数
};

int main()
{
    A a;
    B b;
    A *p1 = &a;  
    A *p2 = &b;   // B 类引用转换为 A 类指针
    p1->print();
    p2->print();  // 自动识别为 B 类, 指向基类的指针在操作它的子类对象时, 因为虚函数的地址在虚函数表中, 会根据实际类型调用其相应的函数
    return 0;
}
```

上面代码的运行结果是输出 "This is A", "This is B".

## C++ 实现反射

构造并保存类名和全局创建实例函数的映射.(参考 "C++反射机制：可变参数模板实现C++反射" https://my.oschina.net/cqcbw/blog/1845889)

## C++ 实现垃圾回收

## C++ 智能指针实现

## 析构函数申请为虚函数

这样做是为了当用一个基类的指针删除一个派生类的对象时, 保证派生类的析构函数会被调用.(因为此时派生类会用自己实现的析构函数, 隐藏基类的 virtual 析构函数)
当然, 并不是要把所有类的析构函数都写成虚函数. 因为当类里面有虚函数的时候, 编译器会给类添加一个虚函数表, 里面来存放虚函数指针, 这样就会增加类的存储空间. 所以, 只有当一个类被用来作为基类的时候, 才把析构函数写成虚函数.

## 匿名命名空间

当定义一个命名空间时, 可以忽略这个命名空间的名称, 编译器在内部会为这个命名空间生成一个唯一的名字, 而且还会为这个匿名的命名空间生成一条 using 指令.在匿名命名空间中声明的名称也将被编译器转换, 与编译器为这个匿名命名空间生成的唯一内部名称(即这里的__UNIQUE_NAME_)绑定在一起. 还有一点很重要, 就是这些名称具有internal 链接属性, 这和声明为 static 的全局名称的链接属性是相同的, 即名称的作用域被限制在当前文件中, 无法在另外的文件中通过使用 extern 声明来进行链接. 如果不提倡使用全局 static 声明一个名称拥有 internal 链接属性, 则匿名命名空间可以作为一种更好的替换方法.
 
注意:命名空间都是具有 external 连接属性的, 只是匿名的命名空间产生的__UNIQUE_NAME__在别的文件中无法得到, 这个唯一的名字在外部是不可见的.
 
C++ 新的标准中提倡使用匿名命名空间, 而不推荐使用 static, 因为 static 用在不同的地方, 涵义不同, 容易造成混淆. 另外, static 不能修饰 class.

## 多重继承

多重继承的问题是会导致 "菱形继承"(继承的两个类有同一个父类), 带来二义性, 此时需要显式的指出同名函数所属的类.

关于多重继承, 可以考虑通过在模板中定义两个参数类(一个参数代表一个类)来替换(其实多态可以通过类模板来实现).

## 虚拟继承

虚拟继承是多重继承中特有的概念. 虚拟基类是为解决多重继承的"菱形继承"问题而出现的. 如: 类 D 继承自类 B1, B2, 而类 B1, B2 都继承自类 A, 因此在类 D 中两次出现类 A 中的变量和函数. 为了节省内存空间, 可以将 B1, B2 对 A 的继承定义为虚拟继承, 而 A 就成了虚拟基类. 实现的代码如下:

```cpp
class A

class B1:public virtual A;
class B2:public virtual A;

class D:public B1,public B2;
```

由于有了 **间接性** 和 **共享性** 两个特征, 虚继承体系下的对象在访问时必然在时间上和空间上与一般情况有较大不同:

1. 时间. 在通过继承类对象访问虚基类对象中的成员(包括数据成员和函数成员)时, 都必须通过某种间接引用来完成, 这样会增加引用寻址时间(就和虚函数一样), 其实就是调整 this 指针以指向虚基类对象, 只不过这个调整是运行时间接完成的.

2. 空间. 由于共享所以不必要在对象内存中保存多份虚基类子对象的拷贝, 这样较之普通多继承节省空间. 虚拟继承与普通继承不同的是, 虚拟继承可以防止出现"菱形继承"时, 一个派生类中同时出现了两个基类的子对象. 为了保证这一点, 在虚拟继承情况下, 基类子对象的布局是不同于普通继承的. 因此, 它需要多出一个指向基类子对象的指针.

离开了多重继承, 虚拟继承就完全失去了存在的必要, 因为这样只会降低效率和占用更多的空间.

-------------------------------------------------------------------------------------------------------------------------------------

# C++ 关键字

## friend(友元)

在实现类之间数据共享时, 友元可以减少系统开销, 提高效率. 但是友元函数破坏了封装机制, 尽量使用成员函数, 除非不得已的情况下才使用友元函数.

实际上具体大概有下面两种情况需要使用友元函数:

1. 运算符重载(比较不同对象)的某些场合需要使用友元.
2. 两个类要共享数据的时候.

因为友元函数没有 this 指针, 要访问对象的非 static 成员时, 需要对象做参数, 要访问 static 成员或全局变量时, 则不需要对象做参数.

因为友元函数是类外的函数, 所以它的声明可以放在类的私有段或公有段, 没有区别.

友元包括友元函数和友元类, 也可以将类中的某个函数设置为另外一个类的友元.

使用友元类时注意:

1. 友元关系不能被继承.
2. 友元关系是单向的, 不具有交换性. 若类 B 是类 A 的友元, 类 A 不一定是类 B 的友元, 要看在类中是否有相应的声明. 
3. 友元关系不具有传递性. 若类 B 是类 A 的友元, 类 C 是 B 的友元, 类 C 不一定是类 A 的友元, 同样要看类中是否有相应的申明.

## const

* 若一个变量声明为 const 类型, 则试图修改该变量的值的操作都被视编译错误.
* 在 C++ 中, 只有被声明为 const 的成员函数才能被一个 const 类的对象调用.
* 要声明一个 const 类型的类成员函数, 只需要在成员函数参数列表后加上关键字 const.
* 如果类成员是指针, 则 const 修饰的成员函数并不能保证不修改指针指向的对象, 编译器不会把这种修改检测为错误.
* const 成员函数可以被具有相同参数列表的非 const 成员函数重载,  在这种情况下, 类对象的常量性决定调用哪个函数.
* **const 成员函数可以访问非 const 对象的非 const 数据成员,  const 数据成员,  也可以访问 const 对象内的所有数据成员**.
* **非 const 成员函数可以访问非 const 对象的非 const 数据成员,  const 数据成员,  但不可以访问 const 对象的任意数据成员**.
* **作为一种良好的编程风格, 在声明一个成员函数时, 若该成员函数并不对数据成员进行修改操作, 应尽可能将该成员函数声明为 const 成员函数**.
* const 关键字不能与 static 关键字同时使用, 因为 static 关键字修饰静态成员函数, 静态成员函数不含有 this 指针, 即不能实例化, const 成员函数必须具体到某一实例(调用时会将 this 指针作为默认参数).

## explicit

按照默认规定, 只有一个参数的构造函数也定义了一个隐式转换, 将该构造函数对应数据类型的数据转换为该类对象

```cpp
class String {
    String ( const char* p ); // 用C风格的字符串p作为初始化值
}
String s1 = "hello"; //OK 隐式转换，等价于String s1 = String(“hello”);
```

但是有的时候可能会不需要这种隐式转换, 如下:

```cpp
class String {
    String ( int n ); //本意是预先分配n个字节给字符串
    String ( const char* p ); // 用C风格的字符串p作为初始化值
}
```

下面两种写法比较正常:

```cpp
String s2 ( 10 );   //OK 分配10个字节的空字符串
String s3 = String ( 10 ); //OK 分配10个字节的空字符串
```

下面两种写法就比较疑惑了:

```cpp
String s4 = 10; //编译通过，也是分配 10 个字节的空字符串
String s5 = 'a'; //编译通过，分配int ('a') 个字节的空字符串
```

s4 和 s5 分别把一个 int 型和 char 型, 隐式转换成了分配若干字节的空字符串, 容易令人误解. 为了避免这种错误的发生, 我们可以声明显示的转换, 使用 explicit 关键字.

* explicit 只对构造函数起作用, 用来抑制隐式转换, 被修饰的构造函数的类, 不能发生相应的隐式类型转换, 只能以显式的方式进行类型转换
* explicit 关键字只能用于类内部的构造函数声明
* explicit 关键字作用于单个参数的构造函数

## boost

* 在 C++ 里, 拷贝有等号拷贝和构造拷贝之分, 类的等号拷贝和构造拷贝都是可以重载的, 如果不重载，默认的拷贝模式是对每个类成员依次执行拷贝. boost::noncopyable 主要用于单例的情况, C++ 11 中为不可拷贝类提供了更简单的实现方法, 使用 delete 关键字即可

### RAII 机制(Resource Acquisition Is Initialization 资源获取即初始化)

在类的构造函数中申请所需的资源, 然后使用, 最终在析构函数中释放资源.

### 智能指针(smart pointer)

如果对象是用声明的方式在栈上创建的(局部变量), 那么 RAII 机制会工作正常, 当离开作用域时对象会自动销毁从而调用析构函数释放资源. 但如果对象是用 new 操作符在堆上创建的, 那么它的析构函数不会自动调用, 程序员必须明确地应用对应的 delete 操作符销毁它才能释放资源.

c++ 引入异常机制后, 智能指针由一种技巧升级为一种非常重要的技术, 因为如果没有智能指针, 程序员必须保证 new 对象能在正确的时机 delete, 四处编写异常捕获代码以释放资源, 而智能指针则可以在退出作用域时--无论是正常流程离开或是异常离开--总调用 delete 来析构在堆上动态分配的对象.

boost 中的智能指针都是轻量级的对象, 都是异常安全的(ecxeption safe), 而且对于所指向的类型 T 也仅有一个很小且很合理的要求--类型 T 的析构函数不能抛出异常(可能是因为会导致异常嵌套, 资源没有释放).

* scoped_ptr

scoped_ptr 包装了 new 操作符在堆上分配的动态对象, 一旦 scoped_ptr 获取了对象的管理权, 我们就无法再从它那里取回来.

```cpp
template<class T> class scoped_ptr // noncopyable
{
private:

    T * px;                                        // 原始指针
    scoped_ptr(scoped_ptr const &);                // 拷贝构造函数私有化
    scoped_ptr & operator=(scoped_ptr const &);    // 赋值操作私有化

    void operator==( scoped_ptr const& ) const;    // 操作符重载私有化
    void operator!=( scoped_ptr const& ) const;    // 操作符重载私有化

public:

    explicit scoped_ptr( T * p = 0 );             // 显式构造函数
    ~scoped_ptr();                                // 析构函数

    void reset(T * p = 0);                        // 智能指针重置
    T & operator*() const;                        // 操作符重载
    T * operator->() const;                       // 操作符重载
    T * get() const;

    explicit operator bool() const;               // 显式 bool 值转换
    void swap(scoped_ptr<T> & a, scoped_ptr<T> & b);
};

template<class T> inline bool operator==(scoped_ptr<T> const & p, boost::detail::sp_nullptr_t);
```

scoped_ptr 同时把拷贝构造函数和赋值操作符都声明为私有的, 禁止对智能指针的拷贝操作, 保证了被它管理的指针不能被转让所有权.

scoped_ptr 提供了一个可以在 bool 语境中自动转换成 bool 值的功能(如 if 的条件表达式)的功能, 用来测试 scoped_ptr 是否持有一个有效的指针(非空). 它可以代替与空指针的比较操作, 而且写法更简单.

scoped_ptr 是一个行为类似指针的对象, 而不是指针, 所以对 scoped_ptr 执行 delete 会得到一个编译错误.

一个持有 scoped_ptr 成员的类, 是不可拷贝和赋值的.

* scoped_array

scoped_array 很像 scoped_ptr, 它包装了 new[] 操作符(不是单纯的 new)在堆上分配的动态数组, 为动态数组提供了一个代理, 保证可以正确的释放内存.

scoped_array 相当于 C++ 11 标准中管理数组对象用法的 unique_ptr.

unique_ptr 是 C++ 11 标准中定义的新的智能指针, 用来取代 C++ 98 中的 std::auto_ptr. unique_ptr 不仅能够代理 new 创建的单个对象, 也能够代理 new[] 创建的数组对象, 也就是说它结合了 scoped_ptr 和 scoped_array 两者的能力. unique_ptr 比 scoped_ptr 有更多的功能, 可以像原始指针一样进行比较, 可以像 shared_ptr 一样定制 **删除器**, 也可以安全地放入标准容器.

scoped_array 的缺点是功能有限, 不能动态增长, 没有边界检查, 也没有迭代器支持, 不能搭配 STL 算法, 仅有一个纯粹的"裸"数组接口. 在需要动态数组的情况下我们应该使用 std::vector.

* shared_ptr

shared_ptr 实现的是引用计数型的智能指针, 可以自由的拷贝和赋值, 当没有代码使用(引用计数为 0)时它才删除被包装的动态分配对象, 已被收入 C++ 11 标准.

```cpp
template <typename T>
class shared_ptr
{
public:
    typedef T element_type;

    shared_ptr();

    template<class Y> explicit shared_ptr(Y * p);
    template<class Y, class D> explicit shared_ptr(Y * p, D d);

    ~shared_ptr();

    shared_ptr(shared_ptr const & r);

    shared_ptr & operator=(shared_ptr const & r);
    template<class Y> shared_ptr & operator=(shared_ptr<Y> const & r);

    void reset();
    template<class Y> void reset(Y * p);
    template<class Y, class D> void reset(Y * p, D d);

    T & operator*() const;
    T * operator->() const;
    T * get() const;

    bool unique() const;
    long use_count() const;

    explicit operator bool() const;
    void swap(shared_ptr & b);
}
```

shared_ptr 有多种类型的构造函数:

* 无参的 shared_ptr() 创建一个持有空指针的 shared_ptr.
* shared_ptr(Y * p) 获得指向类型 T 的指针 p 的管理权, 同时引用计数置为 1. 这个构造函数要求 Y 类型必须能够转换为 T 类型.
* shared_ptr(shared_ptr const & r) 从另外一个 shared_ptr 获得指针的管理权, 同时引用计数加 1, 结果是两个 shared_ptr 共享一个指针的管理权.
* operator= 赋值操作符可以从另外一个 shared_ptr 获得指针的管理权, 其行为同构造函数.
* shared_ptr(Y * p, D d) 行为类似 shared_ptr(Y * p), 但使用参数 d 指定了析构时的定制删除器, 而不是简单的 delete.
* 别名构造函数(aliasing), 不增加引用计数的特殊用法.

shared_ptr 的 reset() 函数的行为与 scoped_ptr 也不尽相同, 它的作用是将引用计数减 1, 停止对指针的共享, 除非引用计数为 0, 否则不会发生删除操作.

shared_ptr 有两个专门的函数来检查引用计数. unique() 在 shared_ptr 是指针的唯一所有者时返回 true, use_count() 返回当前指针的引用计数. 要小心, use_count() 应该仅仅用于测试或者调试, 它不提供高效率的操作, 而且有的时候可能是不可用的. 而 unique() 则是可靠的, 任何时候都可用, 而且比 use_count()==1 速度更快.

shared_ptr 还支持比较运算, 可以测试两个 shared_ptr 的相等或不相等, 比较基于内部保存的指针, 相当于 a.get()==b.get(). shared_ptr 还可以使用 operator< 比较大小, 同样基于内部保存的指针, 但不提供 operator< 以外的比较操作符, 这使得 shared_ptr 可以被用于标准关联容器(set 和 map).

shared_ptr 还支持流输出操作符 operator<<, 输出内部的指针值, 方便调试.

* shared_array

* weak_ptr

* intrusive_ptr


