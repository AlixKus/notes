JSP会变成Servlet，这个过程是容器来完成的。
不要在JSP写Scriptlet，只当成显示层就阔以了。

#标签分类
<% %> scriptlet
<%= %> 等价与<% out.write()%>
<%! %> 声明所生成的servlet类的成员。
<%@ %>指令


隐式对象 | 类型
request | HttpServletRequest
response | HttpServletRe
out | JspWriter
session | HttpSession
config | ServletConfig
application | ServletCOntext
page | Object
pageContext | pageContext
exception | Throwable

JspWriter继承io.Writer，大多数打印方法与PrintWriter相同。
pageContext封装了其它隐式对象

#指令
##page
<%@page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8" import="..." session="false" %>
定义页面特定属性，如字符编码，页面相应内容，是否有隐式session对象。
##taglib
<%@ taglib  tagdir="/WEB-INF/tags/cool" prefix="c"%>
定义JSP可用标签库。
##include
<%@ include file="aa.html" %>
定义转换时增加到当前页面的文本和代码。