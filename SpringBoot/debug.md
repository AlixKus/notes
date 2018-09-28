#配置
debug=true

#positive matches
匹配上的
##`DispatcherServletAutoConfiguration`
`@Order`声明优先级
`@ConditionalOnClass`当类路径下含有指定类,它启动起来
##'WebMvcAutoConfigurationAdapter`
指定了视图渲染，通过EnableConfigurationProperties查找配置的属性
```
@Configuration
	@Import(EnableWebMvcConfiguration.class)
	@EnableConfigurationProperties({ WebMvcProperties.class, ResourceProperties.class })
	@Order(0)
	public static class WebMvcAutoConfigurationAdapter
			implements WebMvcConfigurer, ResourceLoaderAware {
				...
			}

@ConfigurationProperties(prefix = "spring.mvc")
public class WebMvcProperties {
	....
}
```


#negative matches
没有匹配的