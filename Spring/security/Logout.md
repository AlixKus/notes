Spring Security默认开启了csrf
所以退出的时候默认要POST
可以设置get方式退出
#具体方式
```
	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http
			.logout()
				.logoutRequestMatcher(new AntPathRequestMatcher("/logout"));
	}
```