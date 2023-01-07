---
title: 关于「关系型数据库」和「SQL」的概念
date: 2023-01-07 17:01:00 +0800
categories: [Backend]
tags: [Database, SQL]
render_with_liquid: false
---

#  1 什么是关系型数据库？

## 定义

关系型数据库(Relational Database, RDB)因建立于[关系模型](https://zh.wikipedia.org/wiki/%E5%85%B3%E7%B3%BB%E6%A8%A1%E5%9E%8B)之上得名，它通过由行&列组成的**表**、以及**表与表之间的联系**来构建信息，从而使得各种数据点之间的关系更加容易被分析。

表的每一列代表一种属性(Attribute)，而每一行是一个值组(Tuple).

RDB的所有表都有一个称为**主键(Primary Key)**的特性，作为行的唯一标识符。且每行都可以通过一个**外键(Foreign Key)**（对另一个表的主键的引用）来创建不同表之间的联系。

## 举例

比如，有一个存储客户信息的表和一个存储订单信息的表。其中：

- <u>客户表</u>包含的客户数据有：客户ID（主键）、名字、地址等
- <u>订单表</u>包含的订单数据由：订单ID（主键）、客户ID（外键）、订单&发货日期、订单状态等

<img src="https://lh3.googleusercontent.com/_j-DAQG6tx5MwOwhdNFkHMou4fWHRuEbzYr3wEaRClkCnC3W2TR8CnMsAvmVX-rgOICpWX-wrBPc=e14-rj-sc0xffffff-h1000-w1000" alt="借助关系型数据库，您可以在相关数据之间建立逻辑关系。" style="zoom:50%;" />

# 2 什么是SQL?

## 历史

SQL(Structural Query Language, 结构化查询语言)是通用的关系型数据库语言，于1974年由IBM的Don Chamberlin 和 Ray Boyce 提出（有这层关系，关系型数据库有时也被称为"SQL Databases"）

在1979年，甲骨文公司首先提供商用的SQL，IBM也实现了SQL。

在1986年，SQL成为美国国家标准协会(ANSI)对关系数据库管理系统的语言标准。

在1987年，SQL成为国际标准化组织(ISO)的标准。

虽然有标准的存在，但大部分的SQL代码在不同的数据库系统中并不具有完全的跨平台性。

## SQL & RDB & RDBMS的关系

在RDBMS(RDB Manage System, 关系型数据库管理系统)中，我们构建RDB并用SQL展开分析

## 常用的RDBMS

主要是PostgresSQL、MySQL、MS SQL、Oracle

逐年使用趋势如下：[图片来源](https://db-engines.com/en/ranking_trend)

![image](https://user-images.githubusercontent.com/84035000/211150615-808f06b4-0828-4809-9d3e-29458aac67d5.png)

关于MySQL和PostgresSQL的battle可以看[拉踩1](https://www.zhihu.com/question/20010554), [拉踩2](https://www.163.com/dy/article/G3IF6JJV0511EBNT.html)



# 参考

- RDB

  - https://www.ibm.com/topics/relational-databases

  - https://zh.wikipedia.org/wiki/%E5%85%B3%E7%B3%BB%E6%95%B0%E6%8D%AE%E5%BA%93

  - https://cloud.google.com/learn/what-is-a-relational-database?hl=zh-cn

  - https://zhuanlan.zhihu.com/p/78619241

- SQL

  - https://zh.wikipedia.org/zh-cn/SQL