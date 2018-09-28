##基本原理
`request.setCharacterEncoding(characterEncoding);`

##天坑
###ServletRequestListener
不要再requestInitialized裏讀取參數，否則會亂碼。
可以在requestDestroyed裏操作。
getParameter will bom。

##使用Tomcat提供的Filter
```
<filter>
  	<filter-name>EncodingFilter</filter-name>
  	<filter-class>org.apache.catalina.filters.SetCharacterEncodingFilter</filter-class>
  	<init-param>
  		<param-name>encoding</param-name>
  		<param-value>utf-8</param-value>
  	</init-param>
  </filter>
  <filter-mapping>
  	<filter-name>EncodingFilter</filter-name>
  	<url-pattern>/*</url-pattern>
  </filter-mapping>
```