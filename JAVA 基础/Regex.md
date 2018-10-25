# 关键类
## Pattern
Pattern类封装了正则表达式
### 静态方法
##### 匹配
`public static boolean matches(String regex, CharSequence input)` 根据所给regex是否匹配input返回一个boolean值.  
内部实现是构造pattern对象并进行匹配操作。
#### 构造方法
Pattern没有公共构造方法，只有静态工厂方法构建。
- `public static Pattern compile (String regex)`
- `public static Pattern compile (String regex, int flags)`

如果提供的正则表达式异常，会抛出`PatternSyntaxException`.  
`flags`是一个位掩码。
例如`Pattern pattern = Pattern.compile ("[A-Z][a-zA-Z]*",
Pattern.CASE_INSENSITIVE | Pattern.UNIX_LINES);`默认情况下，flags为0.
Pattern类实例是不可变的，也是线程安全的。
### 关键操作
####  matcher(CharSequence input)
调用`matcher`方法，给定要匹配的输入，返回一个`Matcher`对象。
#### split(CharSequence input, int limit)
根据正则分割输入字符串，返回分割后的String数组，limit是可选参数，
意义是拆分的最大次数，即返回String数组的最大长度。
#### pattern()
返回源正则表达式字符串

## Matcher
Matcher类封装了与特定输入字符序列匹配的具体正则表达式。Matcher对象并不是线程安全的。  
### 核心操作
#### reset
Matcher可以被复用，因为它监控状态属性，为了重新开始匹配他们必须处于已知状态。使用`rest()`来实现。无参的`rest()`使用上次为Matcher设置的input，有参的就是新传入的input。
#### find
输入字符串是否含由匹配字串，返回一个boolean值。
若找到，可以使用start，end，group获取更多信息。
#### matches
尝试整个输入与正则进行匹配，返回一个boolean值。
这个操作与Pattern.matches结果一致。
#### lookingAt
与matches相似，当只匹配给定输入的开头。
#### star end
一旦匹配，可以使用`start`和`end`确定匹配位于字符串什么位置。  
可以传入一个group参数，来确定子序列是否匹配已知的捕获组子表达式。  
例如
```
CharSequence subseq;
if (matcher.find( )) {
  subseq = input.subSequence (matcher.start(), matcher.end( ));
}
```
#### groupCount
分组是指，正则表达式中被括号括起来的地方。  
例如  
(x).*(y)
(x)为分组1(y)为分组2，分组0是整个表达式。  
(x).*(y) 分组数量为2.  
`groupCount`返回正则中定义的分组数量，是不可变的。

#### group
匹配后，group会返回指定分组编号的值。  
例如  
Pattern (x).*(y)  
Matcher xasdxzy  
group() xasdxzy
group(0) xasdxzy  
group(1) x  
group(2)y
#### replaceAll replaceFirst
替换操作，捕获分组可以先后引用，即替换串可以为匹配的分组。
`$g`g表示分组号。  
replaceFirst 只替换匹配的第一个
replaceAll 只要匹配就换掉
