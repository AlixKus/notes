##配置
配置tiles，可以和JSP共存，优先使用tiels解析视图
```
	@Bean
	public TilesConfigurer TilesConfigurer() {
		TilesConfigurer tc = new TilesConfigurer();
		tc.setDefinitions(new String[] { "/WEB-INF/layout/tiles.xml", "/WEB-INF/views/**/tiles.xml" });
		tc.setCheckRefresh(true);
		return tc;

	}

	@Bean
	public ViewResolver tiels() {
		TilesViewResolver r = new TilesViewResolver();
		return r;
	}
```

##tiles使用方法
tiles.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE tiles-definitions PUBLIC    
	   "-//Apache Software Foundation//DTD Tiles Configuration 3.0//EN"
       "http://tiles.apache.org/dtds/tiles-config_3_0.dtd">
<tiles-definitions>
	<definition name="base" template="/WEB-INF/layout/page.jsp">
		<put-attribute name="header"
			value="/WEB-INF/layout/header.jsp" />
		<put-attribute name="footer"
			value="/WEB-INF/layout/footer.jsp" />
	</definition>

	
	<definition name="home" extends="base">
		<put-attribute name="body"
			value="/WEB-INF/views/home.jsp"></put-attribute>
	</definition>
</tiles-definitions>
```