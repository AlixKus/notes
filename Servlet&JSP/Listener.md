##What
监听事件，当发生某些事件后，调用我们的函数。

##声明
###xml
```
<listener>
	<listener-class>...</listener-class>
</listener>
```
###注解
```
@WebListener
public class myListener implements XXXListener{
	...
}
```

##分类
场景 | 接口 
应用上下文状态 | ServletContextListener
应用上下文属性改动 | ServletContextAttributeListener

想知道多少给并发用户 | HttpSessionListener
会话属性 | HttpSessionAttributeListener

每次请求状态 | ServletRequestListener
请求属性 | ServletRequestAttributeListener

属性类在Session绑定或删除通知 | HttpSessionBindingListener
属性类迁移到另一个JVM时通知 | HttpSessionActivationListener

##通用事件
属性监听器 | `attributeAdded(); attributeRemoved(); attributeReplace();`
生命周期 | `requestInitialized(); contextInitialized(); sessionCreated();`
		 | `requestDestroyed(); contextDestroyed(); sessionDestroyed();`
##HttpSessionBindingListener
```
public class Dog implements HttpSessionBindingListener{
	
	public void valueBound(HttpSessionBindingEvent event)
	{
		//绑定到Session时运行
	}
	
	public void valueUnBound(HttpSessionBindingEvent event)
	{
		//解除绑定时运行
	}
}

```
##示例
ServletContextListener
当初上下文初始化或销毁时，它的监听函数能得到运行。
只要实现这个接口就可以了
```
public class MyServletContextListener implements ServletContextListener{
	public void contextInitialized(ServletContextEcent event)
	{
		//获取Context
		event.getContext();
	}
}