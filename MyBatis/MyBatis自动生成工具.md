#创建Maven项目配置pom


#创建映射文件
generatorConfig.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN" "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
	<context id="context1" targetRuntime="MyBatis3">
		<commentGenerator>
			<!-- 是否去除自动生成的注释 -->
			<property name="suppressAllComments" value="true" />
		</commentGenerator>

		<jdbcConnection
			connectionURL="jdbc:mysql://localhost/test"
			driverClass="com.mysql.cj.jdbc.Driver" password="root" userId="root" />

		<!-- 在当前Maven工程下的com.mcs.model包生成model类 -->
		<javaModelGenerator targetPackage="com.mcs.model"
			targetProject=".\src\main\java">
			<!-- 是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
			<!-- 从数据库返回的值清理前后的空格 -->
			<property name="trimStrings" value="true" />
		</javaModelGenerator>

		<!-- mapper文件生成位置 -->
		<sqlMapGenerator targetPackage="com.mcs.mapper"
			targetProject=".\src\main\resources" />

		<!-- mapper接口生成位置 -->
		<javaClientGenerator targetPackage="com.mcs.dao"
			targetProject=".\src\main\java" type="XMLMAPPER" />

		<!-- 指定数据表 -->
		<table schema="" tableName="person">
		</table>
	</context>
</generatorConfiguration>
```