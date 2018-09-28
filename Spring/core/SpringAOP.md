##Spring提供了4种类型的AOP支持：
1.基于代理的经典Spring AOP；
2.纯POJO切面；
3.@AspectJ注解驱动的切面；
4.注入式AspectJ切面（适用于Spring各版本）

##SpringAOP只支持方法级别的注入
即方法拦截

##SpringAOP支持的AspectJ切点指示器
指示器 | 描述
Spring uses AspectJ’s pointcut expression language to define Spring aspects.
AspectJ designator Description
args() Limits join-point matches to the execution of methods whose arguments are instances of the given types
@args() Limits join-point matches to the execution of methods whose arguments are annotated with the given annotation types
execution() Matches join points that are method executions
this() Limits join-point matches to those where the bean reference of the AOP proxy is of a given type
target() Limits join-point matches to those where the target object is of a given type
@target() Limits matching to join points where the class of the executing object has an annotation of the given type
within() Limits matching to join points within certain types
@within() Limits matching to join points within types that have the given annotation (the execution of methods declared in types with the given annotation when using Spring AOP)
@annotation Limits join-point matches to those where the subject of the join point has the given annotation
这些指示器支持与或非操作
&& || !
在xml&有特殊含义 要用`and`替代
or not可以替代`||` `!`

##SpringAOP特有的切点指示器
bean() 使用beanId来限制切点
`execution(* *(..)) and bean('mb')` 匹配mb的所有方法
`execution(* *(..)) and !bean('mb')` 匹配beanid部位mb的类的所有方法

##切点表达式
execution(<optional modifer> <return type> <class>.<method>(<paramater type>))
表达式可以包含通配符，以选择不同的类和方法的一系列连接点。
举例
* void myClass.foo(int,int) 任意修饰符 返回值为void myClass上的foo 参数为int int 的方法在执行。
* *(..) 所有方法执行连接点
* mypackage..*.*(..) mypackage包和子包任何方法执行连接点
* myClass+.*(..) myClass和任何子类的任何方法的执行

##@AspectJ注解驱动的切面
开启扫描注解
@EnableAspectJAutoProxy() 在配置类上声明`@EnableAspectJAutoProxy()` 
xml配置`<aop:aspectj-autoproxy></aop:aspectj-autoproxy>`
切面必须要注册成为一个bean!
```
package com.mcs.AOP;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class MyObjectAspect {
	@Pointcut("execution(* com.mcs.AOP.MyObject.fun(..))")
	public void myObjectfun() {
	}

	@Before("myObjectfun()")
	public void before() {
		System.out.println("Before");
	}
}
```

##xml配置
当无法修改源码的时候，可是使用xml配置
利用AOP命名空间
```
<aop:config>
<aop:aspect ref="audience">
<aop:before
pointcut="execution(** concert.Performance.perform(..))"
method="silenceCellPhones"/>
<aop:before
pointcut="execution(** concert.Performance.perform(..))"
method="takeSeats"/>
<aop:after-returning
pointcut="execution(** concert.Performance.perform(..))"
method="applause"/>
<aop:after-throwing
pointcut="execution(** concert.Performance.perform(..))"
method="demandRefund"/>
</aop:aspect>
</aop:config>
```
