```markdown
1. 新建项目
2. 改pom
3. 改yml
4. 主启动
5. 业务类
	controller
	service
	dao,dao.xml
	mysql
```

使用ry-vue脚手架

```yaml
# mysql
spring:
  datasource:
    #MySQL配置
    driverClassName: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mas?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=UTC
    username: root
    password: root

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.example.demo.model
server:
  port: 9000
```



建数据库

建表

根据表写实体类

写dao层

写dao.xml，加入增删改查