#声明
@SessionAttributes()
在类上声明，表示类来设置这些sessionAttribute
model.addAttribute 名字与注解值相同的话会变成sessionAttribute
`@SessionAttributes("t") model.model.addAttribute ("t","123");`
或者底层的HttpSession setAttribute方法设置。

获取
@SessionAttribute
在参数前声明，把session域中的值注入到参数
`@SessionAttribute("username") String username`


```
package com.mcs.web;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.SessionAttribute;
import org.springframework.web.bind.annotation.SessionAttributes;

@Controller
@SessionAttributes("username")
public class HomeController {
	public static String FRIST_VIST = "fristVisit";

	static String homeView = "home";

	@RequestMapping("/")
	public String home(HttpServletRequest req, Model model, @SessionAttribute("username") String username) {
		model.addAttribute("d", "asdf");
		model.addAttribute("u",username);
		return homeView;
	}

	@RequestMapping("/setusername/{username}")
	public String setUsername(@PathVariable String username, HttpSession session) {
		session.setAttribute("username", username);
		return homeView;
	}

}
```