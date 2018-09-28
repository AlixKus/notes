Expression Language
JSP2.0规范一部分。
用来取代scriptlet和表达式。

#形式
`${something}`
例如
`Email:${applictionScope.mail}`等价`Email:<%= appliction.getAttribute("mail") %>`
第一个命名变量要么是隐式对象，要么是某个作用域的一个属性。
#隐式对象
//属性域
pageScope
sessionScope
requestScope
applictionScope
//请求参数
param
paramValues

header
headerValues

cookie

initParam

pageContext 
除了pageContext是真正的对象，其他都是Map。

#[]表达式
可以访问数组和List，或属性。
[]内没有加引号的话，就会计算其中的值。
加引号就把他看作性质。
宽容对待，例如一个ArrayList，${list[0]}或${list["0"]}都表示访问第一个值

$(person.name}'s dog %{person.dog.name} toys are:${person.dog.toys}

#禁用脚本
web.xml中放一个`<scripting-invalid>`标记，可以让JSP禁用脚本元素。
```
<web-app ...>
  <jsp-config>
  	<jsp-property-group>
  		<url-pattern>*.jsp</url-pattern>
		<scripting-invalid>true</scripting-invalid>
  	</jsp-property-group>
  </jsp-config>
 </web-app>
 ```