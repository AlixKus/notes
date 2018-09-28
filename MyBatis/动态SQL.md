#<if test="">
如果测试成立，插入其中内容
```
<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG 
  WHERE state = ‘ACTIVE’ 
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

#<choose> <when> <otherwise>
if else替代版本和JSTL中用法一致,只能有一个语句插入
如果tiltle不为null按title查找，
如果title为null，判断author
如果author为null，插入默认语句。
```
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

#<trim> <where> <set>
##<where>
<where>解决了不能动态插入WHERE的问题
例如<if>会形成
```
SELECT * FROM BLOG
WHERE
```
```
SELECT * FROM BLOG
WHERE 
AND title like ‘someTitle’
```
这样的错误语句
通过将<if>包在<where>里，MyBatis会自动处理此类问题
```
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG 
  <where> 
    <if test="state != null">
         state = #{state}
    </if> 
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```
##<trim>
trim主要功能时在自己包含的内容前加上某些前缀或后缀 prefix 和 suffix
可以把包含内容的首部某些内容覆盖，即忽略，也可以把尾部的某些内容覆盖，对应的属性是 prefixOverrides 和 suffixOverrides；正因为 trim 有这样的功能，所以我们也可以非常简单的利用 trim 来代替 where 元素的功能。
如下实现了where的功能
```
	<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG 
  <trim prefix="where" prefixOverrides="AND"> 
    <if test="state != null">
         state = #{state}
    </if> 
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </trim>
</select>
```

##<set>
用于update
与<trim prefix="set" suffixOverrides=",">功能一样
```
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

#<bing>
从OGNL表达式创建一个变量并绑定到上下文，用于`"%#{name}%"`形式的绑定
`_parameter`是传入的参数引用，可以调用其方法。
```
<select id="selectCatalogLike" resultType="Catalog">
		<bind name="name" value="'%' + _parameter + '%'" />
		SELECT
		*
		FROM
		catalog
		<trim prefix="where">
			name LIKE #{name}
			
		</trim>
</select>
```

#<foreach>
通常用于构建IN语句
集合项（item）和索引（index）变量 开闭开关 分隔符 类型
```
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```


#多数据库支持
通过_databaseId 变量来分辨
一个配置了“_databaseId”变量的 databaseIdProvider 可用于动态代码中，这样就可以根据不同的数据库厂商构建特定的语句。比如下面的例子：
```
<insert id="insert">
  <selectKey keyProperty="id" resultType="int" order="BEFORE">
    <if test="_databaseId == 'oracle'">
      select seq_users.nextval from dual
    </if>
    <if test="_databaseId == 'db2'">
      select nextval for seq_users from sysibm.sysdummy1"
    </if>
  </selectKey>
  insert into users values (#{id}, #{name})
</insert>
```