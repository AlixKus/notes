为了让服务器知道用户是谁,是一个会话ID信息。
##如何交换
1.Cookies
第一次请求，服务器响应头添加`Set-Cookie`
例如'Set-Cookie:JESSIONID=....'
然后客户端每次请求都有`Cookie:JESSIONID=...`头了。
2.URL重写
若客户端不接受Cookie，可以用URL重写。
`URL+;jessionid=xxx`
需要对URL进行编码，容器首先尝试cookie完成会话管理，失败后才会用URL重写
`response.encodeURL("/Test")`

##获取
`HttpSession session = request.getSession()`
###工作流程
IF(请求包含会话ID cookie)
	找到与改ID匹配的会话。
ELSE IF(没有会话ID cookie OR 没有与此会话ID匹配的当前会话 )
	创建一个新会话。

##死亡
1.超时
	在web.xml中配饰会话超时
	设置15分钟超时。
```
<web-app ...>
	<session-config>
		<session-timeout>15</session-timeout>
	</session-config>
</web-app>
```
	对特定会话设置 20分钟超时
	`session.setMaxInactiiveInterval(20*60)`
2.调用`invalidate();`
3.应用结束