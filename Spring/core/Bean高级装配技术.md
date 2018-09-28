#通过Profile激活
在特定环境下，使用特定Bean
这些Bean的id可以一样,但要保证一个id只能由一个Bean。

##声明环境
通过指定`spring.profiles.active`的值
作为DispatcherServlet的初始化参数；
作为Web应用的上下文参数；
作为JNDI条目；
作为环境变量；
作为JVM的系统属性；
在集成测试类上， 使用@ActiveProfiles注解设置

##具体操作
通过指定`spring.profiles.active`的值
1.JVM环境下
	Eclipse中修改Run Configurations，添加环境变量`spring.profiles.active`并设置值。
2.Servlet环境下是Servlet context parameter，在xml中设置
```
<web-app>
	<context-param>
		<param-name>spring.profiles.active</param-name>
		<param-value>dev</param-value>
	</context-param>
</web-app>
```
3.测试环境
通过`@ActiveProfiles`指定
```
package com.mcs.AdvancedWiring;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;

@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = ProfileConfig.class)
@ActiveProfiles("nd2")
class ProfileConfigTest {

	@Autowired
	NeedObject nd;

	@Test
	void test() {
		nd.fun();
	}

}

```

##注解 `@Profile("nd1")`
实例
```
package com.mcs.AdvancedWiring;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration
public class ProfileConfig {
	@Bean
	@Profile("nd1")
	public NeedObject nd1() {
		return new NeedObject() {
			@Override
			public void fun() {
				System.out.println("nd1");
			}
		};
	}

	@Bean
	@Profile("nd2")
	public NeedObject nd2() {
		return new NeedObject() {
			@Override
			public void fun() {
				System.out.println("nd2");
			}
		};
	}

}
```
##xml配置
通过beans的profile属性修改
beans.xsd 3.2版本后才有此属性
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd"
		profile="dev">
	<bean class="com.mcs.config.explicitConfig"></bean>
	<context:component-scan base-package="a"></context:component-scan>

</beans>
```

#根据条件激活
通过@Conditional，给定类的matches返回值为真则激活这个Bean
##实例
MyCondition实现了Codition接口
```
package com.mcs.AdvancedWiring;

import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;

public class MyCondition implements Condition {

	@Override
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
		return false;
	}

}
```
配置类 当@MyCondition的matches返回为真激活这个Bean。
```
package com.mcs.AdvancedWiring;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Conditional;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ConditionalConfig {
	@Bean
	@Conditional(MyCondition.class)
	public NeedObject needObject() {
		return new NeedObject() {

			@Override
			public void fun() {

			}
		};
	}
}
```

#处理自动装配的歧义
当且仅当只有一个bean匹配所需，自动装配才是有效的。
当我们需要一个接口，这个接口有多个实现时，会出现歧义，无法完成自动装配。
解决方案
1.设首选Bean
@Primary
xml 中<bean primary="true"/>
如果多余一个的Primary，那么还是会有问题。
2.使用限定符
@Autowired下的@Qualifier(val) 其中val是想要注入的bean的ID(类默认是首字母小写的类名，方法返回默认是方法名)
@Bean下的@Qualifier(val)中的val是指定bean的ID
实例
```
public interface I
{
	void fun();
}
//声明
//可以同name属性或者@Qualifier声明
@Compent(name="ND")
public Myobject() implements I
{
	void fun()
	{
	...}
	
}
@Compent
@Qualifier("ND2")
public MyOject()implements I
{
	void fun()
	{
	...}
	
}

//自动注入
@Autowired
@Qualifier("ND")
I obk;
@Autowired
@Qualifier("ND2")
I o2;
```
高端操作
自定义注解上加上@Qualifier(val)
然后使用自定义的注解，完成装配操作。
```

@Retention(RUNTIME)
@Target({ TYPE, FIELD, METHOD, CONSTRUCTOR })
@Qualifier("ND1")
public @interface ND1 {

}

```

>Java 8允许出现重复的注解， 只要这个注解本身在定义的时候带
有@Repeatable注解就可以。 不过， Spring的@Qualifier注解并
没有在定义时添加@Repeatable注解