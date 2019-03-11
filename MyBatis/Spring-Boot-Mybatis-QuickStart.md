# yml 配置xml文件未知和数据库连接
```yml
mybatis:
  mapper-locations: classpath:mapper/*.xml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mall?useUnicode=true&characterEncoding=utf-8
    username: root
    password: root
    driver-class-name: com.mysql.jdbc.Driver
```
# 配置 Mapper类位置
```java
package com.mcs.mall.admin;

import com.mcs.mall.mapper.MapperStub;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;


@SpringBootApplication
@MapperScan(basePackageClasses = MapperStub.class)
public class AdminApplication {

    public static void main(String[] args) {
        SpringApplication.run(AdminApplication.class, args);
    }

}
```