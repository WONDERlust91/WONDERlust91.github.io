---
title: ORM与ODM
date: 2020-05-03 22:49:35
tags:
- ORM
- ODM
categories: 软件工程
---

## ORM

ORM是Object-Relational-Mapping的缩写。即通过面向对象的语法，完成关系型数据库的操作，这样的对象-关系映射模型就是ORM。通常ORM是作为MVC架构中的Model层。

ORM使用对象封装了数据库操作，不用书写SQL语言。开发者只要面向对象编程，通过数据对象交互，不用关心底层数据库。

ORM优点：

- 数据模型在一个地方定义，易于更新与维护。

- ORM有现成的工具，很多功能可以自动完成，如数据预处理、事务等。

- 迫使你使用MVC架构，ORM是天然的Model。

- 基于ORM的业务代码简单，代码量少，语义性好，易理解。

- 不必编写性能不佳的SQL语句。

ORM缺点：

- ORM库通常都不是轻量级工具，是有一定学习曲线的。

- 对于复杂查询，ORM要么无法表达，要么性能不如原生SQL。

- ORM抽象了数据库层，开发者无法了解底层的数据库操作，也无法定制一些特殊SQL。

## ODM

ORM对应的是关系型数据库，如MySQL，那么ODM就对应的是文档型数据库，如MongoDB。ODM是Object-Document-Mapping缩写，即对象文档映射。MongoDB对应Node的ODM库是Mongoose。
