# 为什么使用它
设计或效率
# 基本类型
final使数据恒定不变
# 对象引用
final使引用恒定不变，即无法再指向另一个对象。  
对象本身仍然可以修改。
## 空白final
可以定义空白final变量，在构造方法中赋值。一旦初始化完成，就不可改变。   
注意：需要保证final变量初始化。
## final参数
意味着无法在方法中更改其所指向的对象或值。
```
    public FinalData(final int i) {
        this.id = "";
        this.i = i;
    }
```
## final方法
使用它的原因：
1.锁定方法，防止继承类修改它的涵义。
2.效率(这个问题交给编译器和JVM处理就好，不必考虑)
类中所有`private`方法都隐式制定为`final`。
之类如果有方法和父类`private`方法重名，并没有什么问题。因为`private`方法对于之类并不可见，这些方法各属于他们自己。
```
public class WithFinal {
    private void f()
    {
        System.out.println("I'm WithFinal f()");
    }
}

public class OverridingPrivate extends WithFinal {
    public void f() {
        System.out.println("I'm OverridingPrivate f()");
    }

    public static void main(String[] args) {
        new OverridingPrivate().f();
    }
}
```
输出
```
I'm OverridingPrivate f()
```
## final类
将类定义为final意味着，它不可以被继承。
其中方法也默认为final,所以向final类中的final方法添加final关键字没有任何意义。