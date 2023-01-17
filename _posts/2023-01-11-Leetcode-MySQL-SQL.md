---
title: Leetcode MySQL SQL 指令
date: 2023-01-11 17:29:00 +0800
categories: [Backend, SQL]
tags: [Database, SQL, MySQL]
render_with_liquid: false
---

感觉SQL指令也学的差不多了，在Leetcode刷一些题，通过题目顺便学一点规范。

之前在YouTube上面看说Leetcode按数字顺序做就行，感觉把下面截图的这些题做完就差不多了。

<img src="https://user-images.githubusercontent.com/84035000/211770619-cfea1a5f-04e9-43bc-8bda-17c58d0222f2.png" alt="image" style="zoom:67%;" />

# 175. 组合两个表（简单）

## 题目

有下面两个表

```
表： Person
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
personId 是该表的主键列。
该表包含一些人的 ID 和他们的姓和名的信息。
```

```
表: Address：
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
addressId 是该表的主键列。
该表的每一行都包含一个 ID = PersonId 的人的城市和州的信息。
```

要求：编写一个SQL查询来报告 `Person` 表中每个人的姓、名、城市和州。如果 `personId` 的地址不在 `Address` 表中，则报告为空  `null` 。

## 分析

其实就是把表2的州弄到表1对应的人上，两个表的关联是`PersonID`。

如果在Address表找不到人，就不返回，说明需要Person的全部条件和Address的部分条件，据此使用JOIN

## 解答

```sql
SELECT Person.FirstName, Person.LastName, Address.City, Address.State
FROM Person LEFT JOIN Address
ON Person.PersonID = Address.PersonID;
```



# 176. 第二高的薪水 (中等)

## 题目

```
Employee 表：
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
id 是这个表的主键。
表的每一行包含员工的工资信息。
```


编写一个 SQL 查询，获取并返回 Employee 表中第二高的薪水 。如果不存在第二高的薪水查询应该返回 null 。

## 分析

就是从小于Max(salary) 的 salary里面再选一个最大的，结构反正就是`select max(s) from Table where cond`

那么condition就是 ` salary <  最大的salary`

最大的salary通过`select max(salary) from employee`来获得，代入即可得到答案。

## 解答

```sql
select max(salary) SecondHighestSalary
from employee
where salary < (select max(salary) from employee);
```



# 177. 第N高的薪水（中等）

