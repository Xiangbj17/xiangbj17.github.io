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

```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

## Example

```sql
INSERT INTO Customers (CustomerName, City, Country)
VALUES ('Cardinal', 'Stavanger', 'Norway');
```

这时候，对于新添加的row，没有赋值的那些column会显示NULL

# 5 NULL Values

NULL Value的意思就是没有赋值，NULL不能通过`=,<,>`这写比较操作找出来，比如要用`IS NULL`或者`IS NOT NULL`关键词来查找。

这个有点像pytorch的`nan`，`nan`没有办法和任何值比较，只能用`torch.isnan`来找到。

## Example

- list all customers with a NULL value in the "Address" field:

  ```sql
  SELECT * FROM Customers
  WHERE Address IS NULL;
  ```

# 6 UPDATE

用于修改当前的表格，就是刘雨欣说的“增删改查”里面的改。

<img src="https://user-images.githubusercontent.com/84035000/211187717-a26e9116-cdd8-472a-8380-a8095d655def.png" alt="image" style="zoom:50%;" />

## Syntax

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

## Example

- update the PostalCode to 00000 for all records where country is "Mexico":

  ```sql
  UPDATE Customers
  SET PostalCode = 00000
  WHERE Country = 'Mexico';
  ```

- Update the `City` column as 'Oslo' of all records in the `Customers` table:

  ```sql
  UPDATE Customers
  SET City = 'Oslo';
  ```

# 7 DELETE FROM

用于删除目前存在的一些条目，一般用WHERE字句来确认删除哪几条。

如果不用WHERE，所有的条目都会被删除

## Syntax

```sql
DELETE FROM table_name WHERE condition;
```

## Example

- delete the customer "SLS" from the "Customers" table:

  ```sql
  DELETE FROM Customers WHERE CustomerName = 'SLS'
  ```

# 8 LIMIT

用于指定返回多少条记录

## Syntax

```sql
SELECT column_names(s) FROM table_name
WHERE conditions LIMIT number;
```



# 9 MIN, MAX, COUNT, AVG, SUM

可以直接顾名思义了，用法也差不多。

## Syntax

```sql
SELECT MIN(column_name) FROM table_name WHERE condition;
```



# 10 LIKE

用于匹配一些特定的pattern，像Python的正则表达式。记住句法和两个通配符即可。

## Syntax

```sql
SELECT column1, column2, ...
FROM table_name
WHERE columN LIKE pattern;
```

## Wildcards

- `%` : 可以表示零个，一个或者多个字符
- `_` : 表示一个字符

## Example

| LIKE Operator                  | Description                                                  |
| :----------------------------- | :----------------------------------------------------------- |
| WHERE CustomerName LIKE 'a%'   | Finds any values that start with "a"                         |
| WHERE CustomerName LIKE '%a'   | Finds any values that end with "a"                           |
| WHERE CustomerName LIKE '%or%' | Finds any values that have "or" in any position              |
| WHERE CustomerName LIKE '_r%'  | Finds any values that have "r" in the second position        |
| WHERE CustomerName LIKE 'a_%'  | Finds any values that start with "a" and are at least 2 characters in length |
| WHERE CustomerName LIKE 'a__%' | Finds any values that start with "a" and are at least 3 characters in length |
| WHERE ContactName LIKE 'a%o'   | Finds any values that start with "a" and ends with "o"       |



# 11 IN, BETWEEN AND

## IN Syntax

IN配合WHERE使用，跟Python的in用法差不多。

```sql
SELECT column1, column2, ... FROM table_name
WHERE column_name IN (value1, value2, ...);
```

## BETWEEN Syntax

也是配合WHERE使用，用于挑选在一定范围内的数据，比如数字，字符，日期。

```sql
SELECT column_name(s) FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```

# 12 JOINs

用于合并多个表的行。

## Example

假设有一个 "Websites" table:

```
+----+--------------+---------------------------+-------+---------+
| id | name         | url                       | alexa | country |
+----+--------------+---------------------------+-------+---------+
| 1  | Google       | https://www.google.cm/    | 1     | USA     |
| 2  | 淘宝          | https://www.taobao.com/   | 13    | CN      |
| 3  | 菜鸟教程      | http://www.runoob.com/    | 4689  | CN      |
| 4  | 微博          | http://weibo.com/         | 20    | CN      |
| 5  | Facebook     | https://www.facebook.com/ | 3     | USA     |
| 7  | stackoverflow | http://stackoverflow.com/ |   0 | IND     |
+----+---------------+---------------------------+-------+---------+
```

另外，还有一个 "access_log" table 网站访问记录:

```
+-----+---------+-------+------------+
| id  | site_id | count | date       |
+-----+---------+-------+------------+
|   1 |       1 |    45 | 2016-05-10 |
|   2 |       3 |   100 | 2016-05-13 |
|   3 |       1 |   230 | 2016-05-14 |
|   4 |       2 |    10 | 2016-05-14 |
|   5 |       5 |   205 | 2016-05-14 |
|   6 |       4 |    13 | 2016-05-15 |
|   7 |       3 |   220 | 2016-05-15 |
|   8 |       5 |   545 | 2016-05-16 |
|   9 |       3 |   201 | 2016-05-17 |
+-----+---------+-------+------------+
```

返回所有网站的访问记录，并以浏览次数排序：

```sql
SELECT Websites.name, access_log.count, access_log.date
FROM Websites
INNER JOIN access_log
ON Websites.id = access_log.site_id
ORDER BY access_log.count;
```

上面的指令的结果：

<img src="https://user-images.githubusercontent.com/84035000/211337948-192fa11c-f9fc-43a0-8cb2-2670575e5559.png" alt="image" style="zoom:50%;" />

## Supported Type

- `INNER JOIN`: selects all rows from both tables as long as there is a match between the columns.
- `LEFT JOIN`: 从左表（Websites）返回所有的行，即使右表（access_log）中没有匹配。
- `RIGHT JOIN`: 从右表（access_log）返回所有的行，即使左表（Websites）中没有匹配。
- `CROSS JOIN`:

![image](https://user-images.githubusercontent.com/84035000/211335518-6f2772cc-d508-4fd0-91e0-78806e1a089e.png)



| LEFT JOIN                                                    | RIGHT JOIN                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <img src="https://www.runoob.com/wp-content/uploads/2013/09/left-join1.jpg" alt="img" style="zoom:50%;" /> | ![img](https://www.runoob.com/wp-content/uploads/2013/09/402A662D-3553-449C-B980-942D864412BD.jpg) |



# 13 UNION

用于连接多个SELECT返回的result-set，条件：

- 每个result-set必须有相同的column数量
- column必须有相似的数据类型
- 每个result-set的column必须有相同的顺序

## Syntax

```sql
SELECT column_name(s) from table_1
UNION
SELECT column_name(s) from table_2;
```

##  NOTE: UNION ALL

`UNION`只会选择不同的值，如果要允许重复的值，使用`UNION ALL`

## Example

```sql
# 常规用法
SELECT City FROM Customers
UNION
SELECT City FROM Suppliers
ORDER BY City;

# 搭配 WHERE 使用
SELECT City, Country FROM Customers
WHERE Country='Germany'
UNION
SELECT City, Country FROM Suppliers
WHERE Country='Germany'
ORDER BY City;
```



# 14 GROUP BY

用于统计某一列相同值的个数，通常会搭配Aggregate Function使用，比如`MEAN(), AVG(), MAX()...`

## Syntax

```sql
SELECT column_names(s) FROM table_name WHERE condition
GROUP BY column_name(s)
ORDER BY column_names(s)；
```

## Example

```sql
# 统计每个城市有多少人
SELECT COUNT(CustomerID), Country FROM Customers
GROUP BY Country
ORDER BY COUNT(CustomerID) DESC;
```

<img src="https://user-images.githubusercontent.com/84035000/211463740-7292c766-6088-4168-bea4-f11a86112b99.png" alt="image" style="zoom:67%;" />

# 15 HAVING

`HAVING`字句可以和Aggregate Function配合使用，弥补了`WHERE`的不足。

## Syntax

```sql
SELECT column_names(s) FROM table_name
WHERE condition GROUP BY column_name(s)
HAVING condition ORDER BY column_name(s);
```

## Example

- list the **number of customers** in each country. Only include countries with more than 5 customers:

```sql
SELECT COUNT(CustomerID), Country From Customers
GROUP BY Country 
HAVING COUNT(CustomerID) > 5;
```

# 16 EXISTS

用于判断subquery是否存在record

## Syntax

```sql
SELECT column_name(s) FROM table_name
WHERE EXISTS (SELECT column_name FROM table_name WHERE condition)
```

## Example

```sql
# 查询Product的价格小于20的Suppliers
SELECT SupplierName
FROM Suppliers
WHERE EXISTS (SELECT ProductName FROM Products WHERE Products.SupplierID = Suppliers.supplierID AND Price < 20);
```













