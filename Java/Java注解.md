---
title: Java注解
date: 2021-10-13 14:31:08
tags: Java
---
# Java注解
注解，就是对某些事物进行标注

>Java提供了一套机制，使我们可以对方法、类、参数、包、域以及变量等添加标准，并且通过某些定义好的实际出发此段信息

# 使用注解
- @Override：表明用于覆盖父类方法
- @Deprecated：表明这个类、方法..是弃用的

# 自定义注解
1. 通过`@interface`关键字声明注解名称，并声明注解的成员属性。
2. 使用java内置的四个元注解对这个自定义标注的范围、功能做出一定限制。

# 元注解
元注解是构建注解的基础组成，元注解定义了衍生的注解，包括上述提到的三个注解也是由元注解定义的。

元注解有哪些呢，全部的注解位于java.lang.annotation包下，主要有四个

@Target,@Retention,@Documented,@Inherited

- @Target：表明注解用于什么地方，由枚举类ElemenetType指定，详细说明如下
  ```
  ElemenetType.FIELD 域声明
  ElemenetType.CONSTRUCTOR 构造器声明
  ElemenetType.LOCAL_VARIABLE 局部变量声明
  ElemenetType.METHOD 方法声明
  ElemenetType.PACKAGE 包声明
  ElemenetType.PARAMETER 参数声明
  ElemenetType.TYPE 类、接口或enum声明
  ```
- @Retention：表示在什么级别保存该注解信息。可选的RetentionPolicy参数包括：
  ```
  RetentionPolicy.SOURCE 注解江北编译器丢弃，即编译时忽略
  RetentionPolicy.CLASS 注解将在class文件中可用，但会被VM丢弃，这是默认级别
  RetentionPolicy.RUNTIME VM将在运行期也保留注释，因此可以通过反射机制读取注解信息
  ```
- @Document：带有此元注解的注解，将会在javadoc或类似工具中生成对应文档记录
- @Inherited：表示自动继承注解类型，这个元注释只会导致从超类继承注解；已实现接口上的注解无效