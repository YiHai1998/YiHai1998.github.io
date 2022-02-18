博客：https://blog.csdn.net/guorui_后端/java/article/details/104229009

### maven依赖

```xml
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>2.1.1</version>
</dependency>
```

### application.yaml

```yaml
spring:
  datasource:
    username: root
    password: 000000
    url: jdbc:oracle:thin:@127.0.0.1:1521:orcl
    driver-class-name: oracle.jdbc.driver.OracleDriver
    type: com.alibaba.druid.pool.DruidDataSource
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

### mapper

在启动类中添加对 mapper 包`扫描`@MapperScan，另一种方式是在mapper类中添加注解@Mapper（不推荐，每个都添加，比较麻烦）

```java
@MapperScan(value="com.demo.mapper")
@SpringBootApplication
public class DemoApplication {
   public static void main(String[] args) {
      SpringApplication.run(DemoApplication.class, args);
   }
}
```

开发mapper

```java
package com.demo.mapper;
 
import com.demo.bean.Department;
import org.apache.ibatis.annotations.*;
 
public interface DepartmentMapper {
    @Select("select * from SSH_DEPARTMENT where id=#{id}")
    public Department getDeptById(Integer id);
 
    @Delete("delete from SSH_DEPARTMENT where id=#{id}")
    public int deleteDeptById(Integer id);
    
    @Options(useGeneratedKeys = true,keyProperty = "id")
    @Insert("insert into SSH_DEPARTMENT(department_name) values(#{departmentName})")
    public int insertDept(Department department);
    
    @Update("update SSH_DEPARTMENT set departmentName=#{DEPARTMENT_NAME} where id=#{id}")
    public int updateDept(Department department);
}
```



>- [@Select](https://my.oschina.net/u/1999259) 是查询类的注解，所有的查询均使用这个
>- [@Result](https://my.oschina.net/u/230619) 修饰返回的结果集，关联实体类属性和数据库字段一一对应，如果实体类属性和数据库属性名保持一致，就不需要这个属性来修饰。
>- [@Insert](https://my.oschina.net/u/3157219) 插入数据库使用，直接传入实体类会自动解析属性到对应的值
>- [@Update](https://my.oschina.net/gdszvip) 负责修改，也可以直接传入对象
>- [@delete](https://my.oschina.net/u/160154) 负责删除

### controller

```java
@RestController
public class DeptController {
    @Autowired
    DepartmentMapper departmentMapper;
 
    @GetMapping("/dept/{id}")
    public Department getDepartment(@PathVariable("id") Integer id){
        return departmentMapper.getDeptById(id);
    }
 
    @GetMapping("/dept")
    public int insertDeptById(@PathVariable("id") Integer id,@PathVariable("departmentName") String departmentName){
        int ret = departmentMapper.insertDept(id,departmentName);
        return ret;
    }
}
```

