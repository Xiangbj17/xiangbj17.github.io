---
title: MySQL SQL 指令
date: 2023-01-08 15:02:00 +0800
categories: [Backend, SQL]
tags: [Database, SQL, MySQL]
render_with_liquid: false
---

Tutorial From [w3schools](https://www.w3schools.com/MySQL/)

注：SQL不区分关键词大小写，但因为教程里大写而且养成良好的coding习惯，这里也大写吧。

# 1 SELECT

用于<u>从DB中挑一些数据出来</u>，还是以Table的形式返回，称作"the result-set".

另：使用`SELECT DISTINCT`可以只返回有区别的值

## Sytax

```sql
SELECT (DISTINCT) column1, column2, ... 
FROM table_name;
```

## Example

| CODE                                       | FUNCTION                                     |
| ------------------------------------------ | -------------------------------------------- |
| `SELECT * from Customers;`                 | 查看Customers这张表里所有的内容              |
| `SELECT Conuntry from Customers;`          | 查看Customers这张表里**所有的**Country数据   |
| `SELECT DISTINCT Conuntry from Customers;` | 查看Customers这张表里**不重复的**Country数据 |

## 进阶用法

```sql
SELECT COUNT(DISTINCT Country) FROM Customers;
```

返回的仍旧是一个表，但是列变成了"COUNT(DISTINCT Country)"

![image](https://user-images.githubusercontent.com/84035000/211185350-8d9d2def-b735-49db-88a0-8f5eb4dfc17e.png)



# 2 WHERE / Condition

用于筛选条目(filter records)，只有满足WHERE条件的那些项目才会被返回。

注：WHERE属于一种clause(分句)，不仅仅在SELECT里使用，UPDATE、DELETE等都可以使用。

## Syntax

```sql
SELECT coloumn1, column2, ...
FROM table_name
WHERE condition;
```

## Operators in the WHERE Clause

| Operator | Description                                                  |
| :------- | :----------------------------------------------------------- |
| =        | Equal                                                        |
| >        | Greater than                                                 |
| <        | Less than                                                    |
| >=       | Greater than or equal                                        |
| <=       | Less than or equal                                           |
| <>       | Not equal. **Note:** In some versions of SQL this operator may be written as != |
| BETWEEN  | Between a certain range                                      |
| LIKE     | Search for a pattern                                         |
| IN       | To specify multiple possible values for a column             |
| AND      | 当所有条件都成立时才为真                                     |
| OR       | 一个条件为真即可                                             |
| NOT      | 求反                                                         |

## Example

假设有如下的"Customers"表格（图中只显示了部分，下面还有更多条目）:

![image](https://user-images.githubusercontent.com/84035000/211185783-3658a169-c59d-4d33-94e2-7316e52942d8.png)

- Select all the customers from "Mexico":

  ```sql
  SELECT * FROM Custormers
  WHERE City = "Mexico";
  ```

-  Select all fields from "Customers" where country is "Germany" AND city is "Berlin":

  ```sql
  SELECT * from Custormers
  WHERE country = "Germany" AND city = "Berlin";
  ```

- Select all fields from "Customers" where country is NOT "Germany" and NOT "USA":

  ```sql
  SELECT * from Customers
  WHERE NOT country = "Germany" and NOT "USA";
  ```



# 3 ORDER BY

用于将返回的表进行升序(Ascending)或者降序(Descending)排列，默认是升序，用`DESC`关键词降序。

## Syntax

```sql
SELECT column1, column2, ...
FROM table_name
ORDER BY column_A, column_B, ... ASC | DESC;
```

## Example

- select all customers from the "Customers" table, sorted DESCENDING by the "Country" column:

  ```sql
  SELECT * From Customers 
  ORDER BY Country DESC;
  ```

- selects all customers from the "Customers" table, sorted ascending by the "Country" and descending by the "CustomerName" column: 

  （先按照国家排序，如果国家相同，则按照顾客的姓名降序排列）

  ```sql
  SELECT * From Customers 
  ORDER BY Country ASC, CustomerName DESC;
  ```

  

# 4 INSERT INTO

用于向Table里添加新的records.

注：如果新添加的数据只包含部分column，需要声明出来；如果包含全部，不需要声明。

## Syntax

```mysql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

## Example

```mysql
INSERT INTO Customers (CustomerName, City, Country)
VALUES ('Cardinal', 'Stavanger', 'Norway');
```

这时候，对于新添加的row，没有赋值的那些column会显示NULL

# 5 NULL Values

NULL Value的意思就是没有赋值，NULL不能通过`=,<,>`这写比较操作找出来，比如要用`IS NULL`或者`IS NOT NULL`关键词来查找。

这个有点像pytorch的`nan`，`nan`没有办法和任何值比较，只能用`torch.isnan`来找到。

## Example

- list all customers with a NULL value in the "Address" field:

  ```mysql
  SELECT * FROM Customers
  WHERE Address IS NULL;
  ```

# 6 UPDATE

用于修改当前的表格，就是刘雨欣说的“增删改查”里面的改。

<img src="https://user-images.githubusercontent.com/84035000/211187717-a26e9116-cdd8-472a-8380-a8095d655def.png" alt="image" style="zoom:50%;" />

## Syntax

```mysql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

## Example

- update the PostalCode to 00000 for all records where country is "Mexico":

  ```mysql
  UPDATE Customers
  SET PostalCode = 00000
  WHERE Country = 'Mexico';
  ```

- Update the `City` column as 'Oslo' of all records in the `Customers` table:

  ```mysql
  UPDATE Customers
  SET City = 'Oslo';
  ```

# 7 DELETE FROM

用于删除目前存在的一些条目，一般用WHERE字句来确认删除哪几条。

如果不用WHERE，所有的条目都会被删除

## Syntax

```mysql
DELETE FROM table_name WHERE condition;
```

## Example

- delete the customer "SLS" from the "Customers" table:

  ```mysql
  DELETE FROM Customers WHERE CustomerName = 'SLS'
  ```

# 8 LIMIT

用于指定返回多少条记录

## Syntax

```mysql
SELECT column_names(s) FROM table_name
WHERE conditions LIMIT number;
```



# 9 MIN, MAX, COUNT, AVG, SUM

可以直接顾名思义了，用法也差不多。

## Syntax

```mysql
SELECT MIN(column_name) FROM table_name WHERE condition;
```







