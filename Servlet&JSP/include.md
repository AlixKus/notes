两种方式
#指令`<%@ include file="header.jsp" %>`
简单的复制粘贴，JSP生产的servlet文件直接是吧`header.jsp`内容粘贴过来。

#jsp标准动作 `<jsp:include page="header.jsp" />`
会调用`org.apache.jasper.runtime.JspRuntimeLibrary.include(request, response, "header.jsp", out, false);`
相当于创建一个`RequestDispatcher`,并调用`include()`
##参数
```
	<jsp:include page="header.jsp">
		<jsp:param value="JSTL" name="title" />
	</jsp:include>
```

#建议
尽量用指令，这样会提高性能。