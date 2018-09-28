#属性占位符
 ${} 的形式,类似与JSP里的EL表达式。
##XML中使用
```
<bean id="x" class="x">
<property name="nm" value="${nm}" />
</bean>
```


#属性表达式
#{}
实例
#{1}  值1
#{T{System}.currentTimeMillis()} 计算表达式当前事件毫秒数。T()表示将java.lang.System视为Java中的类型，因此可调用静态方法currentTimeMillis();
#{Obj.field1} 得到ID为Obj的bean的field1属性
#{systemProperties['title']} 通过systemProperties对象引用系统属性

##JAVA代码中使用
通过@Value
```
public Person(@Value("${name}")String name)
{
	this.name = name;
}
```