##校验POST表单
Spring通过java validata API来标注
`@Valid` 需要验证的参数 ,其后`Errors`参数会自动注入。

##错误绑定到前台
通过form:error 标签 如果有错误会显示验证注解的message属性
字段错误 通过path属性
类错误 就不加path

##完整例子
后台
```
package com.mcs.web.Register;

import javax.validation.Valid;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import com.mcs.model.User;

@Controller
@RequestMapping("register")
public class RegisterController {

	static String registerView = "register";
	static String redirectProfile = "redirect:/profile";

	@Autowired
	User user;

	@GetMapping
	public String registerView(Model model) {
		model.addAttribute(new RegisterForm());
		return registerView;
	}

	@PostMapping
	public String processRegister(@Valid RegisterForm form, BindingResult errors, Model model) {
		if (errors.hasErrors()) {
			return registerView;
		}
		form.toUser(user);
		return redirectProfile;
	}
}
```
前台
```<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8" session="false"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib uri="http://www.springframework.org/tags" prefix="s"%>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="f"%>
<html>
<head>
<title>Register</title>
<link rel="stylesheet" type="text/css"
	href="<c:url value="/resources/style.css" />">
<style type="text/css">
.error {
	border-color: #ff5722;
}
</style>
</head>
<body>

	<h1>Register</h1>

	<f:form method="POST" modelAttribute="registerForm">
		<h1>
			<f:errors path="" />
		</h1>
		<f:label path="username">Username:</f:label>
		<f:input path="username" cssErrorClass="error" />
		<f:errors path="username" />
		<br>

		<f:label path="firstName">First Name: :</f:label>
		<f:input path="firstName" cssErrorClass="error" />
		<f:errors path="firstName" />
		<br>

		<f:label path="lastName">Last Name:</f:label>
		<f:input path="lastName" cssErrorClass="error" />
		<f:errors path="lastName" />
		<br>

		<f:label path="email">Email: </f:label>
		<f:input path="email" type="email" cssErrorClass="error" />
		<f:errors path="email" />
		<br>

		<f:label path="password">Password:</f:label>
		<f:password path="password" cssErrorClass="error" />
		<f:errors path="password" />
		<br>

		<f:label path="passwordConfirm">PasswordConfirm:</f:label>
		<f:password path="passwordConfirm" cssErrorClass="error" />
		<f:errors path="passwordConfirm" />
		<br>

		<input type="submit" value="Register" />

	</f:form>


</body>
</html>
```
DTO
```
package com.mcs.web.Register;

import java.lang.reflect.InvocationTargetException;

import javax.validation.constraints.Email;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

import org.apache.commons.beanutils.BeanUtils;

import com.mcs.model.User;

@PasswordMatcher
public class RegisterForm {
	@NotNull
	@Size(min = 1, max = 16)
	private String firstName;
	@NotNull
	@Size(min = 1, max = 16)
	private String lastName;
	@NotNull
	@Size(min = 1, max = 16)
	private String username;
	@NotNull
	@Size(min = 1, max = 16)
	private String password;
	@NotNull
	@Size(min = 1, max = 16)
	private String passwordConfirm;
	@NotNull
	@Email
	@Size(min = 5, max = 30)
	private String email;

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	public String getPasswordConfirm() {
		return passwordConfirm;
	}

	public void setPasswordConfirm(String passwordConfirm) {
		this.passwordConfirm = passwordConfirm;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public void toUser(User user) {
		try {
			BeanUtils.copyProperties(user, this);
		} catch (IllegalAccessException e) {
			e.printStackTrace();
		} catch (InvocationTargetException e) {
			e.printStackTrace();
		}
	}

}
```
自定义的验证注解
```
package com.mcs.web.Register;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

import org.apache.commons.lang3.StringUtils;

public class PasswordMatcherValidator implements ConstraintValidator<PasswordMatcher, RegisterForm> {

	@Override
	public boolean isValid(RegisterForm form, ConstraintValidatorContext context) {
		return StringUtils.equals(form.getPassword(), form.getPasswordConfirm());
	}
}
```
判断的底层实现
```
package com.mcs.web.Register;

import static java.lang.annotation.ElementType.ANNOTATION_TYPE;
import static java.lang.annotation.ElementType.TYPE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import javax.validation.Constraint;

@Retention(RUNTIME)
@Target({ TYPE, ANNOTATION_TYPE })
@Constraint(validatedBy = { PasswordMatcherValidator.class })
public @interface PasswordMatcher {

	String message() default "两次输入的密码不一致";

	Class<?>[] groups() default {};

	Class<?>[] payload() default {};

}
```