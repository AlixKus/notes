#ResultMap
`ResultMap`设计思想是，简单的语句不需要明确的结果映射，复杂的语句只需描述他们的关系。

#resultType
完美的方案
通过resultType指定映射到的JAVA类型。
java类型要符合JavaBean或POJO。
```
package com.mcs.model;

public class Person {
	private Integer id;
	private String name;
	
	public Person()
	{
		
	}
	
	public Person(Integer id, String name) {
		super();
		this.id = id;
		this.name = name;
	}
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	
}
```
```
<select id="selectPerson" resultType="com.mcs.model.Person">
		SELECT * FROM PERSON
		WHERE
		ID = #{id}
</select>
```
##使用类型别名
在mybatis-config.xml设置扫描的包,`com.mcs.model.Person`别名为`Person`
```	
<typeAliases>
		<package name="com.mcs.model" />
</typeAliases>
```

#高级结果映射
比较有趣的时extends属性，嗯继承嘛。。。
通过`resultMap`,ORM手动映射
constructor 用于实例化类，注入结果到构造方法
	idArg ID参数，标记作为ID的结果可提高整体性能
	arg 注入构造方法的普通结果
id ID结果标记，可提高性能
result 注入到字段或JavaBean属性的普通结果
association 一个复杂类型的关联，将结果包装成这种类型
collection 复杂类型的集合

##id result属性
	property 映射到的字段或属性
	colum 数据库中列名或别名
	javaType Java类的完全限定名或别名
	jdbcType JDBC类型
	typeHandler 指定类型处理器，覆盖默认的处理器。
支持的JDBC类型
支持的 JDBC 类型
为了未来的参考,MyBatis 通过包含的 jdbcType 枚举型,支持下面的 JDBC 类型。
BIT	FLOAT	CHAR	TIMESTAMP	OTHER	UNDEFINED
TINYINT	REAL	VARCHAR	BINARY	BLOB	NVARCHAR
SMALLINT	DOUBLE	LONGVARCHAR	VARBINARY	CLOB	NCHAR
INTEGER	NUMERIC	DATE	LONGVARBINARY	BOOLEAN	NCLOB
BIGINT	DECIMAL	TIME	NULL	CURSOR	ARRAY

##构造方法
<constructor>
	<idArg>
	<arg>
通过声明参数类型和顺序确定构造方法。
一般用于不可变类
例如
```
public class User{
	public User(Integer id,String username){
	...
	}
	...
}
```
为了将结果注入构造方法,MyBatis需要声明形参
```
<constructor>
	<idArg column="id" javaType="int"/>
	<arg colum="username" javaType="String"/>
</constructor>
```

##关联
<association>
	<id>
	<result>
声明关联的元素，比如我们的类有一个Author类型的属性，需要为Author赋值，就需要加载关联属性。
MyBatis有两种方式
	嵌套查询：通过执行另一个SQL语句来返回预期结果。
	嵌套结果: 通过一次查询得到的结果映射到关联的属性。
考虑性能，一般使用嵌套结果。
###嵌套查询
要对author字段赋值需要经行进行 selectAuthor 操作,参数为author_id，会导致两次查询，通常性能很低。
```
<resultMap id="blogResult" type="Blog">
	<association property="author" column="author_id" javaType="Author" select="selectAuthor"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  SELECT * FROM BLOG WHERE ID = #{id}
</select>

<select id="selectAuthor" resultType="Author">
  SELECT * FROM AUTHOR WHERE ID = #{id}
</select>
```
###嵌套结果
属性
	resultMap 可以映射关联的嵌套结果到一个合适的对象图中。
	columnPrefix 当连接夺标，指定前缀
	notNullColum 默认情况下，子对象仅在至少一个列映射到其属性为空才创建，通过指定此属性，Mybatis仅在指定列非空时才创建子对象。逗号分隔多个列名。
```
<association property="author" column="blog_author_id" javaType="Author">
  <id property="id" column="author_id"/>
  <result property="username" column="author_username"/>
</association>
```

##集合
<collection oftype="">
通过此属性映射集合到实体的一个属性。
同样有嵌套查询和嵌套结果，注意要使用oftype指定集合的泛型对象类型(Posts,domain)，jdbcType指定集合类型(ArrayList，LinkedList)
```
<collection property="posts" ofType="domain.blog.Post">
  <id property="id" column="post_id"/>
  <result property="subject" column="post_subject"/>
  <result property="body" column="post_body"/>
</collection>
```


##实例
```
<resultMap type="Article" id="articleWithCatalog">

		<constructor>
			<idArg column="article_id" javaType="Integer"/>
		</constructor>
	
		<result property="title" column="article_title" />
		<result property="context" column="article_context" />
		<result property="posttime" column="article_posttime"
			javaType="java.util.Date" />
		<result property="updatetime" column="article_updatetime"
			javaType="java.util.Date" />
		<association property="catalog" javaType="Catalog">
			<id property="id" column="catalog_id" />
			<result property="name" column="catalog_name" />
		</association>
	</resultMap>
```
```
<select id="selectArticle" resultMap="articleWithCatalog"
		parameterType="int">
		SELECT A.id as article_id,
		A.title as article_title,
		A.context as article_context,
		A.posttime as
		article_posttime,
		A.updatetime as article_updatetime,
		A.catalogid as
		catalog_id ,
		C.id as
		catalog_id,
		C.name as catalog_name
		FROM article A left outer join
		catalog C on C.id = A.id
		WHERE A.id = #{id}
</select>
```
