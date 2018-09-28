#AOP
aspect-oriented programming
面向切面编程
##作用
关注点集中，服务模块简洁。

##AOP术语
###Aspect
由Advice和Pointcut组成。

###Advice
定义切面 what 和 when
通知定义了切面是什么以及何时使用。
切面的工作被称为通知。
Spring aspects是针对方法的aspects，有以下通知类型-
1.Before 目标方法被调用去前执行通知
2.After 执行后调用，不关心执行结果
3.After-returning 成功执行后调用
4.After-throwing 抛出一场后调用
5.Around 环绕目标方法，在前后都可做一些事情。

###Pointcut
定义切面 where
匹配一个或多个连接点

###Join point
可以插入advice的时机成为连接点。
可以是调用方法时，抛出异常，或者修改一个字段等。

##Introduction
表示向现有类添加新方法或属性。比如方法执行完记录到日志，就是为原来的类引入了日志功能。

##Weaving
把切面应用到目标对象并创建新的代理对象的过程。
可以在目标对象周期某个阶段注入：
编译期 AspectJ的Weaving编译器就是这种方式。
类加载期 需要特殊的ClassLoader。
运行期 AOP容器为目标对象创建代理对象，SpringAOP的工作方式。


##流程
在一个地方定义通用功能，以声明的方式定义这个功能何时调用。
这样可以不修改原有类。
