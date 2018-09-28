`@Controller` 将类声明为Spring应用上下文的bean，被扫描到。
`@RequestMapping` 表示要处理的请求路径

当返回一个`String`时，视图名`home`会被我们配置的视图解析器加上前后缀，然后被解析。
当返回`redirect:anotherurl时，会重定向到anotherurl。
例如`redirect:/`,会重定向到`/`

##实例
为model添加内容并输出
```
package com.mcs.web;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HomeController {
	private static String homeView = "home";

	@RequestMapping("/")
	//自动注入所需对象，向model中添加属性用于前端显示
	public String home(HttpServletRequest req,Model model) {
		req.setAttribute("d", "ddd");
		model.addAttribute("ddd","asdf");
		return homeView;
	}
}
```
jsp可用EL表达式显示属性，若为List可以使用jstl标签来处理
home.jsp
```
${d}
${ddd}
```


##接受客户端数据
Spring可以接受以下参数
查询参数Query Parameter
表单参数Form Parameter
路径变量Path Variable
###查询参数
@RequestParam()与查询参数绑定
下面函数 处理GET请求 /echo?q=xxx 或者POST请求 q=xxx
前端${q}值为xxx
若不设置@RequestParam的值,默认与参数名一样
```
@RequestMapping("/echo")
	public String query(@RequestParam() String q, Model m) {
		m.addAttribute("q", q);
		return echoView;
	}
```
echoView.jsp
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>echoView</title>
</head>
<body>
	<h1>${q}</h1>
	<form action="#" method="POST">
		<input type="text" name="q" /> <input type="submit" />
	</form>
</body>
</html>
```
###路径变量
通过在路径设置占位符的形式，在通过@PathVariable注入到参数q
若不设置值，默认与参数名一样
```
	@RequestMapping("/echo/{q}")
	public String echo(@PathVariable(name = "q") String q, Model model) {
		model.addAttribute("q", q);
		return echoView;
	}
```
路径变量可以使用正则的形式
如下 `filename:.*`
```
@RequestMapping("/down/{filename:.*}")
	public ResponseEntity<Resource> downloadByPath(@PathVariable String filename, HttpServletResponse res)
			throws Exception {
		Resource file = fileService.loadAsResource(filename);
		return ResponseEntity.ok().header(HttpHeaders.CONTENT_DISPOSITION,
				"attachment; filename=\"" + URLEncoder.encode(file.getFilename(), "UTF-8") + "\"").body(file);

	}

```

##获取表单
通过POST方法获取类,参数会注入form。
form的属性会被同名参数填充。
```
@PostMapping
	public String processRegister(RegisterForm form) {
		form.toUser(user);
		return redirectProfile;
	}
```
