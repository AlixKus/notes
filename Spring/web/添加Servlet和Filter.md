##额外配置DispatcherServlet
Spring将DispatcherServlet注册到Servlet容器后，会调用`customizeRegistration()`
将Servlet注册后的Dynamic传递过来,可以对DispatcherServlet进行额外配置
```

public class WebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
		@Override
		protected void customizeRegistration(Dynamic registration) {
			// TODO Auto-generated method stub
			super.customizeRegistration(registration);
		}
}
```

##添加其他Servlet和Filter
重载其OnStrtup方法，通过传入的`servletContex`注册Filter和Servlet
```
public class WebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

	//添加UTF-8编码过滤器
	@Override
	public void onStartup(ServletContext servletContext) throws ServletException {
		super.onStartup(servletContext);
		CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter("UTF-8", true);
		registerServletFilter(servletContext, characterEncodingFilter);
	}


}

```