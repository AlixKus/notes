##Bean的声明周期
1． Spring对bean进行实例化；
2． Spring将值和bean的引用注入到bean对应的属性中；
3． 如果bean实现了BeanNameAware接口， Spring将bean的ID传递给
setBean-Name()方法；
4． 如果bean实现了BeanFactoryAware接口， Spring将调
用setBeanFactory()方法， 将BeanFactory容器实例传入；
5． 如果bean实现了ApplicationContextAware接口， Spring将调
用setApplicationContext()方法， 将bean所在的应用上下文的
引用传入进来；
6． 如果bean实现了BeanPostProcessor接口， Spring将调用它们
的post-ProcessBeforeInitialization()方法；
7． 如果bean实现了InitializingBean接口， Spring将调用它们的
after-PropertiesSet()方法。 类似地， 如果bean使用initmethod声明了初始化方法， 该方法也会被调用；
8． 如果bean实现了BeanPostProcessor接口， Spring将调用它们
的post-ProcessAfterInitialization()方法；
9． 此时， bean已经准备就绪， 可以被应用程序使用了， 它们将一直
驻留在应用上下文中， 直到该应用上下文被销毁；
10． 如果bean实现了DisposableBean接口， Spring将调用它的
destroy()接口方法。 同样， 如果bean使用destroy-method声明
了销毁方法， 该方法也会被调用。

##Bean的作用域
默认是单例模式
可以为以下作用域
原型 ConfigurableBeanFactory#SCOPE_PROTOTYPE
	每次注入或获取的时候都创建一给新的bean对象。
单例 ConfigurableBeanFactory#SCOPE_SINGLETON
请求 org.springframework.web.context.WebApplicationContext#SCOPE_REQUEST
	Web应用中，每个会话创建一个bean实例
会话 org.springframework.web.context.WebApplicationContext#SCOPE_SESSION
	Web应用中，每个会话创建一给bean实例
###作用域声明
@Scope
一个原型bean
```
@Bean
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public class prototypeObj
{
}
```
xml
```
<bean class="xxx" id="xxx" socope="prototype">
```
###web中的要点
当作用域是请求或者会话时，需要指定代理模式。
注入到其他组件，注入的是代理，代理会交给真正的bean处理。
ScopedProxyMode.INTERFACES 代理 几口，基于JDK接口动态代理。
ScopedProxyMode.TARGET_CLASS 代理 类，基于CGLIB,生产目标类扩展方式。
	/**
	 * Create a JDK dynamic proxy implementing <i>all</i> interfaces exposed by
	 * the class of the target object.
	 */
	INTERFACES,

	/**
	 * Create a class-based proxy (uses CGLIB).
	 */


###实例
对于一个购物车来说，会话作用域是最合适的。
每个会话创建一个ShopingCart对象。应用中会有多个ShoppingCart Bean实例。
其中proxyMode解决了将请求或会话作用域的bean注入到单例bean中所遇到的问题。
```
@Component
@Scope(
value=WebApplicationContext.SCOPE_SESSION,
proxyMode=ScopedProxyMode.INTERFACES)
public ShoppingCart cart() { ... }
```

`StoreService`需要注入`ShoppingCart`，但`ShoppingCart`是会话作用域的，当用户进入系统后才会创建。
此外，系统有多个`ShoppingCart`实例，我们希望StoreService处理购物车时，使用的`ShoppingCart`是当前会话对应的哪一个。
Spring不会将实际的购物车注入到`StoreService`，他会注入一个`ShoppingCart`bean的代理，这个购物车方法和购物车一样。
`StoreService`调用`ShoppingCart`的方法时， 代理会对其进行懒解析并将调用委托给会话作用域内真正的`ShoppingCart` bean。
```
@Component
public class StoreService{
	@Autowired
	public void setShoppingCart(ShoppingCart shoppingCart)
	{
		this.shoppingCart = shoppingCart;
	}
	...
}
```
xml方式
默认代理是CGLIB生产类
```
<bean id="cart"
class="com.myapp.ShoppingCart"
scope="session">
<aop:scoped-proxy />
</bean>
```
如果代理的是接口，也可以使用JDK接口动态代理
```
<bean id="cart"
class="com.myapp.ShoppingCart"
scope="session">
<aop:scoped-proxy proxy-target-class="false" />
</bean>
```


##Bean装配方案
1. xml声明
2. java中声明
3. 自动发现机制
推荐自动发现机制，无法修改代码时，用JAVA显式配置或XML配置。

#自动装配
关键在与 自动扫描 和 autowired
##配置自动扫描
###注解
在配置类中声明扫描的包或基类
```
@Configuration
@ComponentScan(basePackageClasses = Config.class)
public class Config {
}
```
###xml声明自动装配
```
<beans>
<context:component-scan base-package="a"></context:component-scan>
</beans>
```
##声明Bean
###注解声明
@Bean
@Component
id默认为小写类名(ObjA类id为objA)
@Named(JAVA DI规范)
```
@Component
public class A
{}
```
##自动注入
###注解
@Autowired
	@Autowired 有required属性，默认为true。
	必须注入的话，注入不了就抛出异常，运行失败。
	`@Target({ElementType.CONSTRUCTOR, ElementType.METHOD, ElementType.PARAMETER, ElementType.FIELD, ElementType.ANNOTATION_TYPE})`
	可以在构造方法，普通方法，参数前，字段上，注解上声明。
	假如有且只有一个bean匹配依赖需求的话， 那么这个bean将会被装配进来。
	如果有多个bean都能满足依赖关系的话， Spring将会抛出一个异常，表明没有明确指定要选择哪个bean进行自动装配。
@Inject(JAVA DI规范)

##无法自动装配下的选择
调用第三方库的时候，无法修改源码的时候，必须显式的装配。
###JAVAexplicitConfig
关键在与配置类上添加`@Configuration`
默认情况下， bean的ID与带有@Bean注解的方法名是一样的。可以通过name属性更改
```
@Configuration
public class explicitConfig {
//Spring 会拦截 getA() ,保证返回的CompentA是 getA() 创建的创建的Bean。
	@Bean
	public BneedA getB() {
		BneedA b = new BneedA();
		b.setA(getA());
		return b;
	}

	@Bean
	public CompentA getA() {
		return new CompentA();
	}
}
```
###XML方式配置
可通过Eclipse给STS工具配置
构造方法注入
set注入，通过property属性
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">
	<bean class="com.mcs.OtherPackage.BneedA">
		<property name="a" ref="compentA"></property>
	</bean>
	<bean id="compentA" class="com.mcs.OtherPackage.CompentA"></bean>
</beans>
```

##混合配置
####Config引入javaConfig和XML
添加@Import和@ImportResource
```
@Configuration
@Import(config1.class)
@ImportResource("\bean.xml")
class config{
}
```
###XML引入
xml只能通过import引入其他的xml,可以声明bean引入javaconfig
```
<beans>
<import resource="bea1.xml"/>
<bean class="com.mcs.config.explicitConfig"></bean>
</beans>
```