# 关键字

## const

* 若一个变量声明为 const 类型, 则试图修改该变量的值的操作都被视编译错误
* 在 C++ 中, 只有被声明为 const 的成员函数才能被一个 const 类对象调用
* 要声明一个 const 类型的类成员函数, 只需要在成员函数参数列表后加上关键字 const
* 如果类成员是指针, 则 const 成员函数并不能保证不修改指针指向的对象, 编译器不会把这种修改检测为错误
* const 成员函数可以被具有相同参数列表的非 const 成员函数重载,  在这种情况下, 类对象的常量性决定调用哪个函数
* **const 成员函数可以访问非 const 对象的非 const 数据成员,  const 数据成员,  也可以访问 const 对象内的所有数据成员**
* **非 const 成员函数可以访问非 const 对象的非 const 数据成员,  const 数据成员,  但不可以访问 const 对象的任意数据成员**
* **作为一种良好的编程风格, 在声明一个成员函数时, 若该成员函数并不对数据成员进行修改操作, 应尽可能将该成员函数声明为 const 成员函数**
* const 关键字不能与 static 关键字同时使用, 因为 static 关键字修饰静态成员函数, 静态成员函数不含有 this 指针, 即不能实例化, const 成员函数必须具体到某一实例.

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


