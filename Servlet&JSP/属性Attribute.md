##分类
类型：ServletContext Servlet HttpSession
作用域：上下文 请求 会话
线程安全：除了局部变量和请求属性是安全的。

##通用方法
`setAttribute(String,Object);`
`getAttribute(String);` 需要强制类型转换取回我们要的类。、

##如何做好同步
上下文属性和HttpSession线程不安全。
如果需要同步，比如要的是个上下文变量，那就对`ServletContext`同步
```
synchronized(servletContext)
{
	servletContext.setAttribute("aa","aa");
	...
}
```
