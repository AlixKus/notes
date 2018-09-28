典型xml文件
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mcs.BolggerMap">
	<select id="selectBlog" resultType="Blog">
		select * from Blog where id =
		#{id}
	</select>
</mapper>
```
#namespace与类名相同

#select
查询 
线面这个语句成为selectPerson,接受一个int参数，返回一个HashMap对象，键是列明，值是结果行对应值。
符号参数`#{id}`会告诉MyBatis创建一个预处理参数，通过JDBC，会由"?"来表示，并传到预处理语句中。
```
	<select id="selectPerson" parameterType="int">
		SELECT * FROM PERSON WHERE ID = #{id}
	</select>
```
##可配置属性
| 属性 | 描述 |
| ----- | -----|
| id | 命名空间唯一标识符，用来引用这条语句。 |
| parameterType | 闯入参数类的完全限定名或别名，可选属性，因为MyBatis可以通过TypeHandler推断参数。|
| ~~parameterMap~~ | ~~已过时~~ |
| resultType | 期待返回类型的类完全限定名或别名。如果集合，应是结合包含的类型而不是集合本身。不能和resultMap同时使用 |
| resultMap | 外部 resultMap 的命名引用。结果集映射是MyBatis最强大的地方。不能和resultType同时使用 |
| flushCache |	将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：false。|
| useCache	| 将其设置为 true，将会导致本条语句的结果被二级缓存，默认值：对 select 元素为 true。 |
| timeout	| 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。 |
| fetchSize	| 这是尝试影响驱动程序每次批量返回的结果行数和这个设置值相等。默认值为 unset（依赖驱动）。 |
参照http://www.mybatis.org/mybatis-3/zh/sqlmap-xml.html#
```
<select
  id="selectPerson"
  parameterType="int"
  parameterMap="deprecated"
  resultType="hashmap"
  resultMap="personResultMap"
  flushCache="false"
  useCache="true"
  timeout="10000"
  fetchSize="256"
  statementType="PREPARED"
  resultSetType="FORWARD_ONLY">
 ```
 
#insert, update 和 delete
数据变更语句 insert，update 和 delete 的实现非常接近：
|属性 | 描述 |
| ----- | ---- |
| flushCache | 将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：true（对应插入、更新和删除语句）。 |
| useGeneratedKeys | （仅对 insert 和 update 有用）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系数据库管理系统的自动递增字段），默认值：false。 |
| keyProperty | （仅对 insert 和 update 有用）唯一标记一个属性，MyBatis 会通过 getGeneratedKeys 的返回值或者通过 insert 语句的 selectKey 子元素设置它的键值，默认：unset。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表 |
| keyColumn | （仅对 insert 和 update 有用）通过生成的键值设置表中的列名，这个设置仅在某些数据库（像 PostgreSQL）是必须的，当主键列不是表中的第一列的时候需要设置。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。 |
```
<insert
  id="insertAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  keyProperty=""
  keyColumn=""
  useGeneratedKeys=""
  timeout="20">

<update
  id="updateAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">

<delete
  id="deleteAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">
```
##插入并获取生成的主键
插入一个Person对象,执行完后会将Person的id属性赋值。
```
<insert id="insertPerson" useGeneratedKeys="true" keyProperty="id">
		INSERT INTO PERSON(name) VALUES (#{name})
</insert>
```
##多行插入
传入一个Authors数组或集合，并返回自动生成的主键
```
<insert id="insertPersonList" useGeneratedKeys="true" keyProperty="id">
	INSERT INTO PERSON(name) VALUES
	<foreach collection="list" item="item" separator=",">
		(#{item.name})
	</foreach>
</insert>
```

#sql
这个元素用来定义可重用SQL片段,被其他语句使用。
<sql> <include>
```
	<sql id="INPnV">INSERT INTO PERSON(name) VALUES</sql>
	<insert id="insertPerson" useGeneratedKeys="true"
		keyProperty="id">
		<include refid="INPnV" />(#{name})
	</insert>
```
##可以使用属性占位符 ${}
###例子
````
<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>
<select id="selectUsers" resultType="map">
  select
    <include refid="userColumns"><property name="alias" value="t1"/></include>,
    <include refid="userColumns"><property name="alias" value="t2"/></include>
  from some_table t1
    cross join some_table t2
</select>
```
##属性值也可以用在include里的refid属性中或include内部

```
<sql id="sometable">
		${table}
	</sql>
	<sql id="someInclude">
		SELECT * FROM
		<include refid="${include_target}" />
	</sql>
	<select id="selectPerson" resultType="Person">
		<include refid="someInclude">
			<property name="include_target" value="sometable" />
			<property name="table" value="PERSON" />
		</include>
		WHERE
		ID = #{id}
	</select>
```

# #和$的区别
#转义,$不转义会导致SQL注入