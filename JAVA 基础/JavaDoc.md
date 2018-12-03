# 用途
从源代码中抽取注释形成一个和源码配到的API的文档。

# 命令
`javadoc filename.java`

# 语法
所有命令只能在`/**`注释中出现.  
可是使用`嵌入HTML`或`@`开头的命令  
javadoc只能为`pulibc`和`protected`成员注释，其他成员注释会被忽略掉。
(可以使用-private进行标记，把private成员注释包括进来)

## 嵌入HTML
例如
```
/**
* <pre>
* System.out.println(new Date());
* </pre>
*/
```

## @标签
### @see 引用其他类
`@see System`
### {@link package.class#member label}
`{@link java.security.SecurityPermission sps}`
### @version
`@version 1.1`
### @author
`@author mcs`
### @since
指定代码最早使用的版本  
`@since 1.0`
### @param
此标签用于方法文档
`@param parameter-name description`
### @return
`@return description`
### @throws
`@throws fully-qualified-class-name description`
### @deprecated
`@deprecated description`
表示已过时，配合`@Deprecated`注解使用。
