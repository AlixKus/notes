每个对话持有一个bean,Spring基于代理实现。
会在HttpSession 设置一个属性
实验下面测得为`scopedTarget.user`
##定义一个会话作用域的bean
```
@Component
@SessionScope
public class User {

	private Long id;

	@NotNull
	@Size(min = 5, max = 16)
	private String username;

	@NotNull
	@Size(min = 5, max = 25)
	private String password;

	@NotNull
	@Size(min = 2, max = 30)
	private String firstName;

	@NotNull
	@Size(min = 2, max = 30)
	private String lastName;

	@NotNull
	@Email
	private String email;

	public User() {
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	...setter and getter
}
```

##在Controller里注入并赋值
```
@Controller
@RequestMapping("register")
public class RegisterController {

	static String registerView = "register";
	static String redirectProfile = "redirect:/profile";
	@Autowired
	User user;

	@GetMapping
	public String registerView() {
		return registerView;
	}

	@PostMapping
	public String processRegister(RegisterForm form) {
		System.out.println(user.toString());
		user.setEmail(form.getEmail());
		user.setFirstName(form.getFirstName());
		user.setLastName(form.getLastName());
		user.setPassword(form.getPassword());
		user.setUsername(form.getUsername());
		return redirectProfile;
	}
}

```

##取值并传到前台
```
package com.mcs.web;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import com.mcs.model.User;

@Controller
public class ProfileController {
	static String profileView = "profile";

	@Autowired
	User user;

	@RequestMapping("/profile")
	public String profile(Model model) {
		model.addAttribute(user);
		return profileView;
	}

}
```

profile.jsp
```
Hello${user.username}
```