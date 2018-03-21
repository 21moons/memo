Dependency Injection in .NET
 
## DI 不仅仅是 late binding 或便于进行单元测试，软件在生命周期内的可维护性才是主要目的，项目越复杂，使用DI的收益越大

## DI 注入方式
* Constructor Injection 构造器注入 

## DI(依赖注入)包括两个反面：
1. 类之间的互相依赖转变为类对接口的依赖
2. 使用外部配置文件指定具体的接口实现类。

## 从三个角度认识 DI
* OBJECT COMPOSITION
* OBJECT LIFETIME
* INTERCEPTION
 
## 依赖注入引入的问题是，类无法控制依赖类的创建和销毁(生命周期)，一般这种问题由垃圾回收机制来解决

## 当我们使用第三方来实现依赖注入时，第三方也有了拦截/修改注入类的能力

## 控制反转(IoC)意味着有一个总体框架或运行时来控制程序运行, DI 是 IOC控制反转的子集, 虽然在某些场景下他们可以互换


page 53