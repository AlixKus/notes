#JSP Standard Tag Library
JSP标准标签库，JSTL1.1不是JSP2.0规范的一部分。需要引入`jstl.jar`和`standard.jar`

#定义文件
c.tld

#核心操作

##out
###基操
打印value值，若为null，打印默认值
`<c:out value="ddd"> default="Nothing"`
###HTML转义
默认是转义的，把`<,>,&,',"`转义为HTML语法 (<转为&lt;)
通过escapeXml属性可设置不转义，即原样输出
`<c:out value="<b>dd</b>">`输出为`&lt;b&gt;dd&lt;/b&gt;`
`<c:out value="<b>dd</b>"> escapeXml="flase"`输出为`<b>dd</b>`
###定义
```
   <tag>
        <description>
            Like &lt;%= ... &gt;, but for expressions.
        </description>
        <name>out</name>
        <tag-class>org.apache.taglibs.standard.tag.rt.core.OutTag</tag-class>
        <body-content>JSP</body-content>
        <attribute>
            <description>
                Expression to be evaluated.
            </description>
            <name>value</name>
            <required>true</required>
            <rtexprvalue>true</rtexprvalue>
        </attribute>
        <attribute>
            <description>
                Default value if the resulting value is null.
            </description>
            <name>default</name>
            <required>false</required>
            <rtexprvalue>true</rtexprvalue>
        </attribute>
        <attribute>
            <description>
                Determines whether characters &lt;,&gt;,&amp;,'," in the
                resulting string should be converted to their
                corresponding character entity codes. Default value is
                true.
            </description>
            <name>escapeXml</name>
            <required>false</required>
            <rtexprvalue>true</rtexprvalue>
        </attribute>
    </tag>
```

##forEach
###基操
```
<c:forEach  var="some" items="${somelist}">
	<div>${some}</div>
</c:forEach>
```
items属性是一个数组，集合，Map或逗号分割的String，var是迭代值。
可选属性`varStatus`可以提供当前值 属性可查看`javax.servlet.jsp.jstl.core.LoopTagStatus`
```
<c:forEach  var="some" varStatus="status" items="${somelist}">
	<div>${status.count}${some}</div>
</c:forEach>
```
###定义
```
 <tag>
        <description>
            The basic iteration tag, accepting many different
            collection types and supporting subsetting and other
            functionality
        </description>
        <name>forEach</name>
        <tag-class>org.apache.taglibs.standard.tag.rt.core.ForEachTag</tag-class>
        <tei-class>org.apache.taglibs.standard.tei.ForEachTEI</tei-class>
        <body-content>JSP</body-content>
        <attribute>
            <description>
                Collection of items to iterate over.
            </description>
            <name>items</name>
            <required>false</required>
            <rtexprvalue>true</rtexprvalue>
            <type>java.lang.Object</type>
            <deferred-value>
                <type>java.lang.Object</type>
            </deferred-value>
        </attribute>
        <attribute>
            <description>
                If items specified:
                Iteration begins at the item located at the
                specified index. First item of the collection has
                index 0.
                If items not specified:
                Iteration begins with index set at the value
                specified.
            </description>
            <name>begin</name>
            <required>false</required>
            <rtexprvalue>true</rtexprvalue>
            <type>int</type>
        </attribute>
        <attribute>
            <description>
                If items specified:
                Iteration ends at the item located at the
                specified index (inclusive).
                If items not specified:
                Iteration ends when index reaches the value
                specified.
            </description>
            <name>end</name>
            <required>false</required>
            <rtexprvalue>true</rtexprvalue>
            <type>int</type>
        </attribute>
        <attribute>
            <description>
                Iteration will only process every step items of
                the collection, starting with the first one.
            </description>
            <name>step</name>
            <required>false</required>
            <rtexprvalue>true</rtexprvalue>
            <type>int</type>
        </attribute>
        <attribute>
            <description>
                Name of the exported scoped variable for the
                current item of the iteration. This scoped
                variable has nested visibility. Its type depends
                on the object of the underlying collection.
            </description>
            <name>var</name>
            <required>false</required>
            <rtexprvalue>false</rtexprvalue>
        </attribute>
        <attribute>
            <description>
                Name of the exported scoped variable for the
                status of the iteration. Object exported is of type
                javax.servlet.jsp.jstl.core.LoopTagStatus. This scoped variable has nested
                visibility.
            </description>
            <name>varStatus</name>
            <required>false</required>
            <rtexprvalue>false</rtexprvalue>
        </attribute>
    </tag>
```

##if
###基操
```
<c:if test="true">
c:if is true
</c:if>
```
当test为真时，打印其中的内容
###定义
```
    <tag>
        <description>
            Simple conditional tag, which evalutes its body if the
            supplied condition is true and optionally exposes a Boolean
            scripting variable representing the evaluation of this condition
        </description>
        <name>if</name>
        <tag-class>org.apache.taglibs.standard.tag.rt.core.IfTag</tag-class>
        <body-content>JSP</body-content>
        <attribute>
            <description>
                The test condition that determines whether or
                not the body content should be processed.
            </description>
            <name>test</name>
            <required>true</required>
            <rtexprvalue>true</rtexprvalue>
            <type>boolean</type>
        </attribute>
        <attribute>
            <description>
                Name of the exported scoped variable for the
                resulting value of the test condition. The type
                of the scoped variable is Boolean.
            </description>
            <name>var</name>
            <required>false</required>
            <rtexprvalue>false</rtexprvalue>
        </attribute>
        <attribute>
            <description>
                Scope for var.
            </description>
            <name>scope</name>
            <required>false</required>
            <rtexprvalue>false</rtexprvalue>
        </attribute>
    </tag>
```

##choose when otherwise
###基本用法
if else 结构，当第一个test为真时，就不在执行，即只有一个能成立。
```
<c:choose>
	<c:when test="${choose==1}">111</c:when>
	<c:when test="${choose==2}">222</c:when>
	<c:otherwise>333</c:otherwise>
</c:choose>
```

##set
设置属性变量的值
###基本用法
```
//value 可以是对象，不必为String
//如果 计算值为null 变量会被删除
	<c:set var="pet" value="${persion.dog} />

	//设置目标属性或值 以下两种方法都行
	//目标是个Map
	<c:set target="${petMap}" property="dogName" value="MaoMao" />
	//目标是个bean
	<c:set target="${persion}" property="name">
		${foo.name}
	</c:set>
	
	<c:set var="level" value="333"/>
	${level}
```
如果没有"level"属性，就会创建一个。
scope可选。如不指定会一次在页面，请求。。。依此查找
###要点
target须为计算一个对象`${...}`;
var是一个id名;
不能同时有var和target;
scope可选，如果没有这个属性，默认为page作用域;
value为null的话，var制定的属性会被删除;
target为null，会抛异常.
###定义
```
  <tag>
        <description>
            Sets the result of an expression evaluation in a 'scope'
        </description>
        <name>set</name>
        <tag-class>org.apache.taglibs.standard.tag.rt.core.SetTag</tag-class>
        <body-content>JSP</body-content>
        <attribute>
            <description>
                Name of the exported scoped variable to hold the value
                specified in the action. The type of the scoped variable is
                whatever type the value expression evaluates to.
            </description>
            <name>var</name>
            <required>false</required>
            <rtexprvalue>false</rtexprvalue>
        </attribute>
        <attribute>
            <description>
                Expression to be evaluated.
            </description>
            <name>value</name>
            <required>false</required>
            <rtexprvalue>true</rtexprvalue>
            <deferred-value>
                <type>java.lang.Object</type>
            </deferred-value>
        </attribute>
        <attribute>
            <description>
                Target object whose property will be set. Must evaluate to
                a JavaBeans object with setter property property, or to a
                java.util.Map object.
            </description>
            <name>target</name>
            <required>false</required>
            <rtexprvalue>true</rtexprvalue>
        </attribute>
        <attribute>
            <description>
                Name of the property to be set in the target object.
            </description>
            <name>property</name>
            <required>false</required>
            <rtexprvalue>true</rtexprvalue>
        </attribute>
        <attribute>
            <description>
                Scope for var.
            </description>
            <name>scope</name>
            <required>false</required>
            <rtexprvalue>false</rtexprvalue>
        </attribute>
    </tag>
```

##remove
用来删除一个变量
###基操
```
//scope可选，如果不指定，从所有作用域删除这个属性
<c:remove var="level" scope="request">
//什么也不打印，因为已被删除
${level}
```

##import
###基操
```
<c:import url="http://xxx.com/test.html" />
```
带参数的操作
```
	<c:import url="header.jsp">
		<c:param name="title">JSTL</c:param>
	</c:import>
```
可以包含容器之外的页面。

##url
用来重写URL，在cookie无法保存会话的时候重写URL。
var可以用来在其他地方访问这个url
不会自动对URL编码，可以使用c:param
```
url:<c:url value="test.jsp?key=在这里" var="url1"></c:url>
	<br>
	urlEncoding:<c:url value="test.jsp">
		<c:param name="key">在这里</c:param>
	</c:url>
```
输出为
```
url:test.jsp?key=在这里 
urlEncoding:test.jsp?key=%E5%9C%A8%E8%BF%99%E9%87%8C
```

##catch
尝试在块中操作，异常当地处理，不会调用错误页面。
没法通过`exception`直接访问,需要一个var属性。
```
<c:catch var="myException">
		<%= 10/0 %>
		这里不会执行了
</c:catch>
	you will See it.${myException.message}
```