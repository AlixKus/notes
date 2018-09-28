#Servlet认证体系
servlet规范没有指定容器如何实现对认证护具的支持。但，几乎所有开发商都提供了某些方法维护，通常存在数据库.。

##解决了什么问题
验证 
授权 
数据完整性CONFIDENTIAL
数据不可更改性INTEGRAL
(大部分基于SSL,HTTPS即HTTP SSL)

##配置
web.xml 中必须有一个`<login-config>`元素和多余一个的`<security-constraint>`
`<security-role>`容器会将角色信息映射到这里·
```
	<login-config>
		<auth-method>BASIC</auth-method>
	</login-config>
	<security-role>
		<role-name>admin</role-name>
	</security-role>	
	<security-constraint>
		<web-resource-collection>
			<web-resource-name>adminResource</web-resource-name>
			<url-pattern>/admin</url-pattern>
		</web-resource-collection>
		<auth-constraint>
			<role-name>admin</role-name>
		</auth-constraint>
	</security-constraint>
```

##使用SSL
配置数据完整性或不可更都可。
```	
	<security-constraint>
		<user-data-constraint>
			<transport-guarantee>INTEGRAL</transport-guarantee>
		</user-data-constraint>
	</security-constraint>
```

##realm
servlet中的realm是存储认证信息的地方。Tomcat中，默认通过tomcat-users.xml查找用户名密码。

###BASIC认证例子
要想访问/admin，必须通过认证。
```
	<login-config>
		<auth-method>BASIC</auth-method>
	</login-config>

	<security-constraint>
		<web-resource-collection>
			<web-resource-name>adminResource</web-resource-name>
			<url-pattern>/admin</url-pattern>
		</web-resource-collection>
		<auth-constraint>
			<role-name>admin</role-name>
		</auth-constraint>
	</security-constraint>
```

###代码可查看`org.catalina.authenticator`下文件。
要面向接口编程。。。

##获取用户信息
HttpServletRequest提供了以下方法
`isUserInRole(xx)` 是否有xx授权
`getUserPrincipal()` 获取用户主体
`getRemoteUser()` 检查认证状态