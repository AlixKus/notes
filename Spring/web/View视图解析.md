#关键接口
##ViewResolver
`ViewResolver`通过视图名返回`View`
```
public interface ViewResolver {
	View resolveViewName(String viewName, Locale locale) throws Exception;
}
```

##View
View 负责将model解析并通过response输出
```
public interface View {
	void render(@Nullable Map<String, ?> model, HttpServletRequest request, HttpServletResponse response)
			throws Exception;

}
```
#Spring提供了一些默认实现
常用
InternalResourceViewResolver 将视图解析为Web应用的内部资源（一般为JSP）
更多可查看`org.springframework.web.servlet.view`包

#配置
```	
	@Bean
	public ViewResolver viewResolver() {
		InternalResourceViewResolver resolver = new InternalResourceViewResolver();
		resolver.setPrefix("/WEB-INF/views/");
		resolver.setSuffix(".jsp");
		return resolver;
	}
```
xml配置
```
	<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver" p:prefix="/WEB-INF/views/" p:suffix=".jsp" />
```

#Springwebmvc 的tag库
包括form和spinrg基础标签
form标签基本用法如下
```
//后台需要注入“xx”
<f:form modelAttribute="xx">
	//同过path属性绑定xx的字段，如果filed字段有错误 cssClass就为Error
	<f:label path="username" cssErrorClass="error">Username:</f:label>
	<f:inputp path="filed" cssErrorClass="error">
	//如果filed字段 没有通过验证就输出一个`<span>注解message内容</span>`的标签
	//如果error 没有path或path为空 就是类级别的错误
	//path="*" 显示所有错误 包括类和字段
	<f:error path="*">
</f:form>
```
JSP视图
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8" session="false"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="f"%>
<html>
<head>
<title>Register</title>
<link rel="stylesheet" type="text/css"
	href="<c:url value="/resources/style.css" />">
</head>
<body>
	<h1>Register</h1>
	<f:form  method="POST"  modelAttribute="registerForm">
		First Name: <f:input path="firstName" cssErrorClass="error" />
		<br>
		Last Name: <f:input path="lastName" />
		<br>
		Email: <f:input path="email" type="email" />
		<br>
		Password <f:password path="password" />
		<br>
		PasswordConfirm: <f:input path="passwordConfirm" />
		<br>
		<input type="submit" value="Register" />
	</f:form>

</body>
</html>
```
后台
```
@GetMapping
	public String registerView(Model model) {
		model.addAttribute(new RegisterForm());
		return registerView;
	}
```

