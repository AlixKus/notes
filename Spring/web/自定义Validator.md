核心点
自定义注解加上`@Constraint(validatedBy{})`
满足Constraint的条件
编写验证处理类

##实例
类级别 
类中两个字段是否相等,可用于密码判断
```
package com.mcs.web.Register;

import static java.lang.annotation.ElementType.ANNOTATION_TYPE;
import static java.lang.annotation.ElementType.TYPE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import javax.validation.Constraint;

@Retention(RUNTIME)
@Target({ TYPE, ANNOTATION_TYPE })
@Constraint(validatedBy = { FiedldMatchValidator.class })
public @interface FieldMatch {

	String message() default "Not Equals";

	Class<?>[] groups() default {};

	Class<?>[] payload() default {};

	String src();

	String want();

	@Target({ TYPE, ANNOTATION_TYPE })
	@Retention(RUNTIME)
	@interface List {
		FieldMatch[] value();
	}
}

```

```
package com.mcs.web.Register;

import java.lang.reflect.InvocationTargetException;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

import org.apache.commons.beanutils.BeanUtils;
import org.apache.commons.lang3.StringUtils;

public class FiedldMatchValidator implements ConstraintValidator<FieldMatch, Object> {

	private String one;
	private String two;

	@Override
	public void initialize(FieldMatch constraintAnnotation) {
		one = constraintAnnotation.src();
		two = constraintAnnotation.want();
	}

	@Override
	public boolean isValid(Object value, ConstraintValidatorContext context) {

		try {
			String src = BeanUtils.getProperty(value, one);
			String want = BeanUtils.getProperty(value, two);
			return StringUtils.equals(src, want);

		} catch (IllegalAccessException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (InvocationTargetException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (NoSuchMethodException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		return false;
	}
}

```