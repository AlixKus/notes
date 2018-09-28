#XMLp配置文件属性解析
##properties
properties可以外部配置并动态替换
```
	<properties resource="mybatis.config">
		<property name="username" value="dev_user" />
		<property name="password" value="F2Fa3!33TYyg" />
	</properties>
```
然后其中的属性就可以在整个配置文件中被用来替换需要动态配置的属性值。比如:
```
<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```

##settings
Mybatis极为重要的设置
一个完整的配置
```
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="multipleResultSetsEnabled" value="true"/>
  <setting name="useColumnLabel" value="true"/>
  <setting name="useGeneratedKeys" value="false"/>
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <!-- 指定数据库下划线命名法JAVA骆驼峰命名法 -->
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```
具体参照 http://www.mybatis.org/mybatis-3/zh/configuration.html


#typeAliases
为了减少冗余
指定具体类
```
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```
扫描包 例如domain.blog.Author别名为author
在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。 比如 domain.blog.Author 的别名为 author；若有注解，则别名为其注解值。
```
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```
注解后别名为`aaa`
```
@Alias("aaa")
public class Author {
    ...
}
```


##typeHandlers
无论Mybatis预处理设置参数或从结果中取值，都要用类型处理器将所取值以合适方式转换成JAVA类型
内置了一些默认类型处理器。
 
| JAVA类型 | JDBC类型 | 
| ------ | ------ | 
| java.lang.Boolean, boolean | BOOLEAN | 
| java.lang.Byte, byte | NUMERIC 或 BYTE |
| java.lang.Short, short | NUMERIC 或 SHORT INTEGER |
| java.lang.Integer, int | NUMERIC 或 INTEGER |
| java.lang.Long, long | NUMERIC 或 INTEGER |
| java.lang.Float, float | NUMERIC 或 FLOAT |
| java.lang.Double, double | NUMERIC 或 DOUBLE |
| java.math.BigDecimal | NUMERIC 或 DECIMAL |
| java.lang.String	| CHAR, VARCHAR, NVARCHAR, NCHAR, CLOB, LONGVARCHAR |
| byte[] | BLOB, LONGVARBINARY |
| java.util.Date | TIMESTAMP, DATE, TIME|
。。。

#Enviroments
配置环境
默认环境ID，每个environmentid，事务管理配置，数据源配置
```
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```
###事务管理器transactionManage
Mybatis提供了两种事务管理器type=[JDBC|MANAGED]
JDBC：直接使用JDBC的提交和回滚设置，依赖数据源得到的连接管理事务作用域
MANAGED：从来不提交或回滚一个连接，让容器来管理事务的整个生命周期。
默认情况下它会关闭连接，然而一些容器并不希望这样，因此需要将 closeConnection 属性设置为 false 来阻止它默认的关闭行为。例如:
```
<transactionManager type="MANAGED">
  <property name="closeConnection" value="false"/>
</transactionManager>
```
###数据源
####不用数据池
UNPOOLED– 这个数据源的实现只是每次被请求时打开和关闭连接。
driver – 这是 JDBC 驱动的 Java 类的完全限定名（并不是 JDBC 驱动中可能包含的数据源类）。
url – 这是数据库的 JDBC URL 地址。
username – 登录数据库的用户名。
password – 登录数据库的密码。
defaultTransactionIsolationLevel – 默认的连接事务隔离级别。
作为可选项，你也可以传递属性给数据库驱动。要这样做，属性的前缀为“driver.”，例如：
driver.encoding=UTF8
####使用数据池
除了以上属性还有
poolMaximumActiveConnections – 在任意时间可以存在的活动（也就是正在使用）连接数量，默认值：10
poolMaximumIdleConnections – 任意时间可能存在的空闲连接数。
poolMaximumCheckoutTime – 在被强制返回之前，池中连接被检出（checked out）时间，默认值：20000 毫秒（即 20 秒）
poolTimeToWait – 这是一个底层设置，如果获取连接花费了相当长的时间，连接池会打印状态日志并重新尝试获取一个连接（避免在误配置的情况下一直安静的失败），默认值：20000 毫秒（即 20 秒）。
poolMaximumLocalBadConnectionTolerance – 这是一个关于坏连接容忍度的底层设置， 作用于每一个尝试从缓存池获取连接的线程. 如果这个线程获取到的是一个坏的连接，那么这个数据源允许这个线程尝试重新获取一个新的连接，但是这个重新尝试的次数不应该超过 poolMaximumIdleConnections 与 poolMaximumLocalBadConnectionTolerance 之和。 默认值：3 (新增于 3.4.5)
poolPingQuery – 发送到数据库的侦测查询，用来检验连接是否正常工作并准备接受请求。默认是“NO PING QUERY SET”，这会导致多数数据库驱动失败时带有一个恰当的错误消息。
poolPingEnabled – 是否启用侦测查询。若开启，需要设置 poolPingQuery 属性为一个可执行的 SQL 语句（最好是一个速度非常快的 SQL 语句），默认值：false。
poolPingConnectionsNotUsedFor – 配置 poolPingQuery 的频率。可以被设置为和数据库连接超时时间一样，来避免不必要的侦测，默认值：0（即所有连接每一时刻都被侦测 — 当然仅当 poolPingEnabled 为 true 时适用）。
JNDI – 这个数据源的实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的引用。这种数据源配置只需要两个属性：

initial_context – 这个属性用来在 InitialContext 中寻找上下文（即，initialContext.lookup(initial_context)）。这是个可选属性，如果忽略，那么 data_source 属性将会直接从 InitialContext 中寻找。
data_source – 这是引用数据源实例位置的上下文的路径。提供了 initial_context 配置时会在其返回的上下文中进行查找，没有提供时则直接在 InitialContext 中查找。
和其他数据源配置类似，可以通过添加前缀“env.”直接把属性传递给初始上下文。比如：
env.encoding=UTF8

#映射器（mappers）
不需要注册接口了，命名空间已声明接口
```<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```
不需要注册接口了
```
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
````
接口必须和mapper文件在同一个包下才有效
```
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```
接口必须和mapper文件在同一个包下才有效
```
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```