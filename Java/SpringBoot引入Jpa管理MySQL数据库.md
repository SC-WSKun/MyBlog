---
title: SpringBoot引入Jpa管理MySQL数据库
date: 2021-10-11 15:50:33
tags: [Java, SpringBoot]
categories: Java
---

# 新建 test 数据库

进入 mysql 命令行模式，创建名为 test 的数据库

```
create database test default character set utf8 collate utf8_general_ci;
```

# 创建 SpringBoot 项目

在创建时添加 Spring Web 模块、JPA 驱动以及 MySQL 驱动

# 配置数据库相关信息

```
# 数据库驱动
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# 数据库用户名
spring.datasource.username=root
# 数据库密码
spring.datasource.password=123456
# 链接本地数据库(这里可能会报错，如果报错请看另一篇blog)
spring.datasource.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf-8&useSSL=false
# 每次启动项目时更新数据库
spring.jpa.hibernate.ddl-auto=update
```

# 编写一个实体类和数据库表一一对应

```
@Entity
public class Test{
    @Id //主码
    @GeneratedValue //自增
    private int id;
    private String name;
    public int getId(){
        return id;
    }
    public void setId(int id){
        this.id = id;
    }
    public String getName(){
        return name;
    }
    public void setName(String name){
        this.name = name
    }
}
```
# 实现持久层来操作数据库
```
@Repository
public interface TestDao extends JpaRepository<Test,Integer> {
}
```
# 实现一个controller来供浏览器操作数据库
```
@RestController
public class TestController{
    @Resource
    TestDao dao;

    @GetMapping("/add")
    public Test add() {
        Test test = new Test();
        test.setName("Kunkun");
        Test save = dao.save(test);
        return save;
    }
    @GetMapping("/list")
    public List<Test> list(){
        List<Test> all = dao.findAll();
        return all;
    }
}
```
# 可以运行项目了
运行后，在浏览器打开`localhost:8080/add`，即添加了一个记录

然后后，在浏览器打开`localhost:8080/list`，可以查询到记录
