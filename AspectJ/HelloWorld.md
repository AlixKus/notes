##原有类
```
public class HelloWolrd {
	public void fun()
	{
		
	}
}
```
##编写切面
```

public aspect HelloWorldAspect {
	pointcut fun() : execution(* HelloWorld.fun(..));

	after() returning() : fun() {
		System.out.println("Fun is returning");
	}
}

```

##织入
###编译期织入
通过ajc编译器编译
产生的HelloWorld字节码文件已被增强，即织入了"Fun is returning";
###运行时织入
准备好独立编译的类和方面，在加载时织入。
