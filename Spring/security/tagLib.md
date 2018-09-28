Spring Security
标签库只有以下标签
<%@ taglib uri="http://www.springframework.org/security/tags"  prefix="ss"%>
<ss:csrfInput/> 开启csrf隐藏域，不要在sf:form里使用 `<input type="hidden" name="_csrf" value="8e6bd4f4-144a-4b57-88c6-5934ad0a3049">`
<ss:authentication property=""/> 渲染当前认证用户信息


#示例
##如果登陆显示用户名
```
	<ss:authorize access="isAuthenticated()">
		<ss:authentication property="principal.Username" />
	</ss:authorize>
```
SpringSecurityEL https://docs.spring.io/spring-security/site/docs/5.0.7.RELEASE/reference/htmlsingle/#el-access