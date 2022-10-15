---
title: SQL学习笔记
date: 2019-07-18 08:51:08
tags: SQL
categories: SQL
---

## 基本概念

SQL 是 Structured Query Language 的缩写。SQL 不是某个特定数据库供应商专有的语言。几乎所有重要的 DMBMS 都支持 SQL。标准 SQL 由 ANSI 标准委员会管理，称为 ANSI SQL。所有主要的 DBMS 即使有自己的扩展，也都支持 ANSI SQL。

### 发音

1. S-Q-L

2. see·kwl

### 数据库 database：保存有组织数据的容器

人们常用数据库这个术语来代表他们使用的数据库软件，这是不正确的。数据库软件应称为数据库管理系统（DBMS），数据库是通过 DBMS 创建和操纵的容器。

### 表 table：一种用于存储特定类型数据的结构化的文件

表名是唯一的，用来标识表本身。

### 模式 schema：关于数据库和表的布局及特性的信息

模式定义了数据在表中如何存储以及数据库中表的关系，也包括对特定表的描述。

### 列 column：表中的一个字段

所有表都是由一个或多个列组成。

### 数据类型 datatype：定义了列可以存储哪些数据种类，每个列都有相应的数据类型

数据类型及其名称是 SQL 不兼容的一个主要原因。

注意：**字符串类型值在 SQL 中都使用单引号''**

### 行 row：表中的一个记录 record

### 主键 primary key：能够唯一标识表中每一行的列或一组列

每个表都应该具有一个主键，以便后续的数据操作与管理。

表中任何列都可以做为主键，只要满足下列条件：

1. 任意两行都不具有相同的主键值；
2. 每一行都必须具有一个主键值（主键列不允许 NULL 值）；
3. **主键列中的值不允许修改或更新**；
4. **主键值不能重用（如果某行从表中删除，它的主键不能不能赋给以后的新行）**。

在使用多列做为主键时，上述条件必须应用到所有列，所有列值的组合必须是唯一的（但是单个列的值可以不唯一）。

<!-- more -->

## SELECT 语句

关键字 keyword 是 SQL 保留字，不能用作表或列的名字。

### 检索单列

```sql
SELECT column1 FROM table1;
```

注意：

1. 没有明确排序查询结果，则返回的数据没有特定的顺序。

2. 多条 SQL 语句必须以分号(;)分隔，虽然多数 DBMS 不需要加分号，但加上分号是更好的做法。

3. SQL 语句不区分大小写，但对 SQL 关键字大写可以提升代码可读性。

4. 在处理 SQL 语句时，所有的换行都会被忽略。多数 SQL 开发人员认为，将 SQL 语句分成多行更易阅读和调试。

### 检索多列

在 SELECT 关键字后给出多个列名，列名间以逗号分隔，最后一列的列名后不可加逗号。

```sql
SELECT column1, column2, column3 FROM table1;
```

### 检索所有列

在列名位置使用星号(\*)通配符可以检索所有列。

```sql
SELECT * FROM table1;
```

### DISTINCT 关键字

SELECT 关键字返回该列所有值，当有重复值但我们只想检索不同的值时，可以使用 DISTINCT 关键字。

```sql
SELECT DISTINCT column1 FROM table1;
```

注意：

1. DISTINCT 关键字必需放在列名前面；

2. DISTINCT 作用于所有列，而不是 DISTINCT 后的第一列，多列使用 DISTINCT 时，只有多列的值全都相同时才会被当作相同值舍弃，哪怕有一列的值不同，都会做为不同值返回。因此如果多列中有一列是主键，那么必然会返回所有行的数据，与不使用 DISTINCT 关键字得到的结果一致。

### 限制检索结果

要返回一定数量的行，不同的 DBMS 对 SQL 的实现不同。

SQL Server 和 Access 中使用 TOP 关键字决定返回多少行。

```sql
SELECT TOP 5 column1 FROM table1;
```

DB2

```sql
SELECT column1 FROM table1 FETCH FIRST 5 ROWS ONLY;
```

Oracle，基于 ROWNUM 计算行

```sql
SELECT column1 FROM table1 WHERE ROWNUM <= 5;
```

MySQL、MariaDB、PostgreSQL、SQLite，使用 LIMIT 子句

```sql
SELECT column1 FROM table1 LIMIT 5;
```

OFFSET 关键字指定了起始行，下面的代码表示显示从第三行起的五行数据

```sql
SELECT column1 FROM table1 LIMIT 5 OFFSET 3;
```

注意：

1. 起始行为第 0 行，而不是第一行；

2. MySQL、MariaDB、SQLite 支持简化写法，LIMIT 5 OFFSET 3 可以写成 LIMIT 5,3;

### 注释

行内注释：-- 或 #，多行注释：/\* \*/

```sql
-- 行内注释1
# 行内注释2，尽量不使用，支持这种形式注释的DBMS很少。
/*
 多
 行
 注
 释
 */
```

## 排序，ORDER BY 子句

若不明确指定排序顺序，则不应该假定检索出的数据顺序有任何意义

SQL 语句由子句构成，有些子句是必需的，有些则是可选的。一个子句通常由关键字加上所提供的数据组成。

ORDER BY 子句取一列或多列的名字，据此对输出排序。

注意：

1. ORDER BY 子句应该是 SELECT 语句中最后一条子句；

2. 用非检索列排序数据是完全合法的；

3. 按多列排序，用逗号分隔所有列名即可，排序完全按照多列的书写顺序进行。即先按 ORDER BY 子句第一列排序，有相同值时按第二列排序，若按第一列排序后都是唯一值，则不会按后面的列排序。

我们还可以按 SELECT 出的列表中列的位置排序，当列名不在 SELECT 出的列表中时，则不能使用此方法，但必要情况下，可以混合使用列名与列的位置排序。

```sql
-- 按SELECT出的第二列、第三列及column1排序
SELECT column1, column2, column3 FROM table1 ORDER BY 2, 3, column1
```

指定排序方向，默认为升序方向（关键字为 ASC 或 ASCENDING）。通过 DESC 关键字（DESC 是 DESCENDING 的缩写，两个都可用）指定降序排序，DESC 关键字只应用在直接位于其前方的列名，若要在多个列上进行降序排序，必须对每一列都指定 DESC 关键字。

```sql
SELECT column1, column2 FROM table1 ORDER BY column1 DESC, column 2 DESC
```

排序是否区分大小写则取决于数据库设置方式

## 过滤数据，WHERE 子句

SQL 过滤与应用过滤。数据既能在数据库中过滤，也可以在应用层过滤，若在应用层中过滤，则 SELECT 语句检索出超出实际需求的数据，客户端对返回的数据循环判断，提取出需要的行。通常情况下，应用层过滤是不可取的，首先应用层过滤影响应用性能，且使创建的应用不具备可伸缩性；其次发送冗余的数据给客户端会占用额外的带宽。因此尽可能在数据库层面去过滤数据。

### WHERE 子句操作符

| 操作符  | 说明               |
| ------- | ------------------ |
| =       | 等于               |
| <>      | 不等于             |
| !=      | 不等于             |
| <       | 小于               |
| <=      | 小于等于           |
| !<      | 不小于             |
| >       | 大于               |
| >=      | 大于等于           |
| !>      | 不大于             |
| BETWEEN | 在指定的两个值之间 |
| IS NULL | 为 NULL 值         |

表中列出的操作符是冗余的，并非所有 DBMS 都支持全部这些操作符，具体请参阅 DBMS 的文档。

```sql
SELECT column1, column2 FROM table1 WHERE column1 <= 10;
SELECT column1, column2 FROM table1 WHERE column2 != 'foo';
```

使用 BETWEEN 关键字时，必须指定两个值，两个值必须用 AND 关键字分隔。注意，BETWEEN 匹配范围中所有值，**包括两个边界值**

```sql
SELECT column1, column2 FROM table1 WHERE column1 BETWEEN 5 AND 10;
```

空值检查：创建表时，表设计人员可以指定其中的列能否不包含值。当一个列下某行不包含值时，称为包含空值 NULL。注意：NULL 与字段包含数字 0，空字符串''，或空格' '不同。判断值是否为 NULL 不能简单检查= NULL，而要使用 WHERE 字句的 IS NULL 子句。这将返回所有包含 NULL 值的行。

```sql
SELECT column1, column2 FROM table1 WHERE column1 IS NULL;
```

注意：通过过滤选择不包含指定值的所有行时，可能希望返回含 NULL 值的行，但是无法做到。因为未知值有特殊含义，数据库无法知晓是否匹配，所以进行过滤时不会返回这些结果。

## 高级过滤

逻辑操作符：用来联结或改变 WHERE 子句的关键字。

### 组合 WHERE 子句

SQL 允许给出多个 WHERE 子句，以 AND 子句或 OR 子句联结。AND 表示与，用来表示检索满足所有给定条件的行；OR 表示或，用来表示检索匹配任一给定条件的行。通常情况下，许多 DBMS 在 OR 子句第一个条件得到满足的情况下，就不再计算第二个条件了。

```sql
-- 检索column1为'foo'且column2小于等于10的行
SELECT column1, column2 FROM table1 WHERE column1 = 'foo' AND column2 <= 10;
-- 检索column1为'foo'或column2小于等于10的行；注意：这会检索出column1为foo但column2大于10的行，因为第一条件满足时，第二条件被短路了
SELECT column1, column2 FROM table1 WHERE column1 = 'foo' OR column2 <= 10;
```

求值顺序

AND 操作符在求值过程中优先级更高，因此需要改变操作符优先级时应该使用圆括号对操作符进行明确分组。尽可能使用括号进行分组，依赖默认的求值顺序可读性较差，易造成歧义。

```sql
-- 若要选取column2大于10且column1为'foo'或为'bar'的行时，下面这种写法是错误的，这会选出所有column1为'foo'的行，或column1为'bar'且column2大于10的行
SELECT column1, column2 FROM table1 WHERE column1 = 'foo' OR column1 = 'bar' AND column2 > 10;
-- 正确写法应使用括号分组
SELECT column1, column2 FROM table1 WHERE (column1 = 'foo' OR column1 = 'bar') AND column2 > 10;
```

### IN 操作符

IN 操作符用来指定条件范围，范围中每个条件都可以进行匹配。IN 取一组由逗号分隔、括在圆括号中的合法值。

```sql
SELECT column1, column2 FROM table1 WHERE column1 IN ('foo', 'bar') ORDER BY column1;
```

注意：IN 操作符与 OR 操作符有相同的功能，IN 操作符语法更清晰，**IN 操作符一般要比 OR 操作符执行得更快**，IN 最大的优点是可以包含其他 SELECT 语句，能更动态地建立 WHERE 子句，后面会讲到。

### NOT 操作符

WHERE 子句中用来否定其后所跟条件的关键字，NOT 关键字从不单独使用。

```sql
SELECT column1, column2 FROM table1 WHERE NOT column1 = 'foo';
```

简单的 WHERE 子句，使用 NOT 没有什么优势，复杂子句中则很有用，如与 IN 操作符联用，NOT 就可以找出所有非匹配的行。

注意：MariaDB 支持使用 NOT 否定 IN、BETWEEN 和 EXISTS 子句。大多数 DBMS 允许使用 NOT 否定任何条件。

## 通配符

通配符（wildcard）：用来匹配值的一部分的特殊字符

搜索模式（search pattern）：由字面值、通配符或两者组合构成的搜索条件。

为了在搜索子句中使用通配符，必须使用 LIKE 操作符。LIKE 告诉 DBMS，后面跟的搜索模式利用通配符匹配而不是相等匹配。严格来说，LIKE 是谓词（predicate），而不是操作符。

注意：通配符搜索只能用于字符串类型，非文本数据类型字段不能使用通配符搜索。

### 百分号（%）通配符

百分号表示任意字符出现任意次数

```sql
-- 找到column1列所有以Foo开头的值
SELECT column1, column2 FROM table1 WHERE column1 LIKE 'Foo%';
```

注意：

1. 如果使用微软的 Access 数据库应使用\*通配符而不是%；

2. 根据 DBMS 的不同与配置，搜索是可以区分大小写的；

3. 通配符可以在搜索模式中的任意位置使用，并且可以使用多个通配符；

4. %还能匹配 0 个字符，所以%代表的其实是搜索模式中给定的 0 个、1 个或多个字符；

5. 包括 Access 在内的许多 DBMS 都用空格来填补字段的内容，如果某列有 50 个字符，而存储的文本仅 17 个字符，为填满该列需要在文本后加 33 个空格，这样做对数据及其使用没有影响，但对通配符的 SQL 子句有影响，例如 WHERE column1 LIKE 'Foo%Bar'只能匹配以 Foo 开头以 Bar 结尾的 column1，但补了空格后就不再是以 Bar 结尾，而是以空格结尾，就无法通过前面的子句搜索出'Foo hello Bar'这样的数据了。解决办法是给 Bar 后再加一个%匹配，匹配 Bar 后的空格。更好的方法是用函数去掉空格，后面会讲；

6. 通配符%看起来可以匹配任何东西，但是 null 不会被匹配。WHERE column1 LIKE '%'不会匹配 column1 为 null 的行。

### 下划线（\_）通配符

下划线通配符 (\_) 与 (%) 用途一样，但只匹配单个字符。

```sql
-- 找到column1列所有以Foo开头且Foo后只有一个字符的值
SELECT column1, column2 FROM table1 WHERE column1 LIKE 'Foo_';
```

注意：

1. DB2 不支持 (\_)；

2. Microsoft Access 使用 (?) 而不是 (\_)。

### 方括号 ([]) 通配符

方括号通配符 ([]) 指定一个字符集合，匹配该通配符所在位置的单个字符。

```sql
-- 找到column1列所有以F或B开头的值
SELECT column1 FROM table1 WHERE column1 LIKE '[FB]%';
```

注意：

1. 只有微软的 Access 和 SQL Server 支持集合通配符；

2. ([]) 通配符内可以用 (^) 来否定，这有点像正则表达式的语法，如[^fb]表示匹配所有不是 F 或 B 的值；

3. 在 Microsoft Access 中要用 (!) 而不是 (^) 来表示否定，如[!FB]；

4. 也可以使用 NOT 操作符得到类似的效果，但 (^) 的优点是使用多个 WHERE 子句时能简化语法，如下：

```sql
-- 使用NOT操作符实现 (^) 相同的效果
SELECT column1 FROM table1 WHERE NOT column1 LIKE '[FB]%' ORDER BY column1;
```

通配符使用技巧：

通配符很有用，但是有代价的，因为通配符的搜索通常比其他搜索耗费更长的处理时间。

1. 不过度使用通配符，其他操作符能达到目的，应尽量使用其他操作符；

2. 确实需要使用通配符时，尽量不要用在搜索模式的开始，因为这样搜索是最慢的。

## 创建计算字段

字段 Field 基本上与列 column 的意思相同，经常互换使用。一般数据库列称为列，字段则与计算字段一起使用。

只有数据库知道 SELECT 语句中哪些列是实际的表列，哪些列是计算字段，对于客户端来看计算字段与其他表列的数据返回方式相同。

### 拼接字段

拼接 (concatenate) 是指将多个值联结到一起形成一个单值。在 Access 与 SQL Server 中使用 (+) 作为拼接操作符，DB2、Oracle、PostgreSQL、SQLite、Open Office Base 使用 (||) 作为拼接操作符，MySQL、MariaDB 中则必需使用特殊的函数。

```sql
-- Access、SQL Server
SELECT column1 + '(' + column2 + ')' FROM table1 ORDER BY column1;
-- DB2、Oracle、PostgreSQL、SQLite、Open Office Base
SELECT column1 || '(' || column2 || ')' FROM table1 ORDER BY column1;
-- MySQL、MariaDB
SELECT Concat(column1, '(', column2, ')') FROM table1 ORDER BY column1;
```

由于数据库中通常会用空格填充字段剩余长度，因此上面的 SQL 语句执行结果通常如下：

FOO BAR (FOO BAR )

此时应该使用 SQL 中的 RTRIM()函数，去除字段右侧的多余空格

```sql
-- Access、SQL Server
SELECT RTRIM(column1) + ' (' + RTRIM(column2) + ')' FROM table1 ORDER BY column1;
-- DB2、Oracle、PostgreSQL、SQLite、Open Office Base
SELECT RTRIM(column1) || ' (' || RTRIM(column2) || ')' FROM table1 ORDER BY column1;
-- MySQL、MariaDB
SELECT Concat(column1, ' (', column2, ')') FROM table1 ORDER BY column1;
```

> TRIM 函数  
> LTRIM() 去除字符串左边的空格  
> RTRIM() 去除字符串右边的空格  
> TRIM() 去除字符 串两边的空格

现在我们得到了新的计算列，但它现在还没有名字，它只是一个值，若我们仅在 SQL 查询工具中查看已经没有问题了，但是未命名的列不能用于客户端应用中，因为客户端无法引用它。此时我们要使用别名来使客户端能够引用。

别名（alias）是一个字段或值的替换名，别名用关键字 AS 赋予。

```sql
-- Access、SQL Server
SELECT RTRIM(column1) + ' (' + RTRIM(column2) + ')' AS column1_column2 FROM table1 ORDER BY column1;
-- DB2、Oracle、PostgreSQL、SQLite、Open Office Base
SELECT RTRIM(column1) || ' (' || RTRIM(column2) || ')' AS column1_column2 FROM table1 ORDER BY column1;
-- MySQL、MariaDB
SELECT Concat(column1, ' (', column2, ')') AS column1_column2 FROM table1 ORDER BY column1;
```

注意：别名可以是单词也可以是字符串，若是字符串则应该括在引号中，虽然使用字符串是合法的，但不建议使用字符串。别名有时也称为导出列（derived column）。

### 执行算术计算

SQL 支持基本的操作符

| 操作符 | 说明 |
| ------ | ---- |
| +      | 加   |
| -      | 减   |
| \*     | 乘   |
| /      | 除   |

```sql
SELECT column1, column2, column1*column2 AS column3 FROM table1 WHERE column1 = 'foo';
```

> 测试计算  
> SELECT 语句为测试、检验函数、计算提供了很好的方法。
> 虽然 SELECT 通常用于从表中检索数据，但是省略了 FROM 子句后就是简单的访问和处理表达式。
> 例如 SELECT 3 \* 2 返回 6，SELECT TRIM(' abc ')返回 abc，SELECT Now()，返回当前日期和时间。

## 使用函数处理数据

### SQL 函数的优点与局限

SQL 中的函数为数据的转换和处理提供了方便，但是只有少数几个函数被所有主要的 DBMS 等同地支持。虽然所有类型的函数一般都可以在每个 DBMS 中使用，但各个函数的名称和语法可能不尽相同。

| 函数           | 语法                                                                                                                                                        |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 提取部分字符串 | Access 使用 MID()；DB2、Oracle、PostgreSQL 和 SQLite 使用 SUBSTR()；MySQL 和 SQL Server 使用 SUBSTRING()                                                    |
| 数据类型转换   | Access 和 Oracle 使用多个函数，每种类型的转换有一个函数；DB2 和 PostgreSQL 使用 CAST()；MariaDB、MySQL 和 SQL Server 使用 CONVERT()                         |
| 获取当前日期   | Access 使用 NOW()；DB2 和 PostgreSQL 使用 CURRENT_DATE；MariaDB 和 MySQL 使用 CURDATE()；Oracle 使用 SYSDATE；SQL Server 使用 GETDATE()；SQLite 使用 DATE() |

可见，SQL 函数与 SQL 语句不一样，SQL 函数不是可移植的，这意味着为特定 SQL 实现编写的代码在其他实现中可能不正常。

### 函数类型

大多数 SQL 实现支持以下类型函数

- 用于处理文本字符串（如删除或填充值，转换值为大写或小写）的文本函数

- 用于在数值数据上进行算数操作（如返回绝对值，进行代数运算）的数值函数

- 用于处理日期和时间值并从这些值中提取特定成分（如返回两个日期之差，检查日期有效性）的日期和时间函数。

- 返回 DBMS 正使用的特殊信息（如返回用户登录信息）的系统函数

#### 文本处理函数

UPPER() 函数将文本转换为大写

```sql
SELECT column1, UPPER(column1) AS column1_upcase FROM table1 ORDER BY column1;
```

常用文本处理函数

| 函数                                     | 说明                    |
| ---------------------------------------- | ----------------------- |
| LEFT() （或使用子字符串函数）            | 返回字符串左边的字符    |
| LENGTH() （也使用 DATALENGTH()或 LEN()） | 返回字符串的长度        |
| LOWER() （Access 使用 LCASE()）          | 将字符串转为小写        |
| LTRIM()                                  | 去掉字符串左边的空格    |
| RIGHT() （或使用子字符串函数）           | 返回字符串右边的字符    |
| RTRIM()                                  | 去掉字符串右边的空格    |
| SOUNDEX()                                | 返回字符串的 SOUNDEX 值 |
| UPPER() （Access 使用 UCASE()）          | 将字符串转换为大写      |

SOUNDEX 是一个将任何文本字符串转换为描述其语音表示的字母数字模式的算法。SOUNDEX 考虑了类似的发音和音节，使得能对字符串进行发音比较而不是字母比较。多数 DBMS 提供对 SOUNDEX 的支持（Access 和 PostgreSQL 不支持 SOUNDEX()，SQLite 需要在编译时选择支持 SOUNDEX 否则默认不支持）

若数据库中有个人名为 Michelle Green 但这是错误的输入，正确的名字为 Michael Green，此时用 Michael Green 就不会返回数据

```sql
SELECT column1 FROM table1 WHERE column1 = 'Michael Green';
```

此时使用 SOUNDEX 函数搜索，它将匹配所有发音类似于 Michael Green 的名字，就能检索出 Michelle Green

```sql
SELECT column1 FROM table1 WHERE SOUNDEX(column1) = SOUNDEX('Michael Green');
```

#### 日期和时间处理函数

日期时间函数在 SQL 中具有重要作用，但它们很不一致，可移植性最差。

找出 2012 年的所有数据

```sql
-- SQL Server
-- DATEPART() 返回日期的某一部分，参数为要返回的部分与日期
SELECT column1 FROM table1 WHERE DATEPART(yy, column_date) = 2012;
-- Access
SELECT column1 FROM table1 WHERE DATEPART('yyyy', column_date) = 2012;
-- PostgreSQL
SELECT column1 FROM table1 WHERE DATE_PART('year', column_date) = 2012;
-- Oracle
-- to_char函数提取部分日期，to_number函数将值转换为数字类型
SELECT column1 FROM table1 WHERE to_number(to_char(column_date, 'YYYY')) = 2012;
-- 也可使用BETWEEN操作符
-- to_date函数将字符串转为日期
-- SQL Server中使用CONVERT函数替换to_date函数
SELECT column1 FROM table1 WHERE column_date BETWEEN to_date('01-01-2012') AND to_date('12-31-2012');
-- MySQL、MariaDB
SELECT column1 FROM table1 WHERE YEAR(column_date) = 2012;
-- SQLite
SELECT column1 FROM table1 WHERE strftime('%Y', column_date) = '2012';
```

#### 数值处理函数

数值处理函数仅处理数据，一般主要用于代数、三角或几何运算，数值函数在主要 DBMS 函数中最统一。

| 函数   | 说明               |
| ------ | ------------------ |
| ABS()  | 返回一个数的绝对值 |
| COS()  | 返回一个角度的余弦 |
| EXP()  | 返回一个数的指数值 |
| PI()   | 返回圆周率         |
| SIN()  | 返回一个角度的正弦 |
| SQRT() | 返回一个数的平方根 |
| TAN()  | 返回一个角度的正切 |

## 汇总数据

### 聚集函数

我们常需要汇总数据而不需要把它们实际检索出来，例如：

- 确定表中行数（或者满足某个条件或包含某个特定值的行数）

- 获得表中某些行的和

- 找出表列（所有行或某些特定行）的最大值、最小值、平均值

SQL 给出五个聚集函数，SQL 的聚集函数在各主要 SQL 实现中得到了相当一致的支持。这些函数很高效，返回结果要比自己在客户端中自己实现要快得多。

| 函数    | 说明             |
| ------- | ---------------- |
| AVG()   | 返回某列的平均值 |
| COUNT() | 返回某列的行数   |
| MAX()   | 返回某列的最大值 |
| MIN()   | 返回某列的最小值 |
| SUM()   | 返回某列值之和   |

#### AVG 函数

AVG 通过对表中行数计数并计算其列值之和，求得该列的平均值。AVG 可以用来返回所有列的平均值，也可以用来返回特定列或行的平均值。

```sql
-- 返回所有行的平均值
SELECT AVG(column1) AS avg_column1 FROM table1;
-- 返回特定行的平均值
SELECT AVG(column1) AS avg_column1 FROM table1 WHERE column2 = 'foo';
```

> 注意：**AVG 只用于固定列**  
> AVG 函数只能用来确定特定数值列的平均值，而且列名必需作为函数参数给出。为了获得多个列的平均值，必须使用多个 AVG 函数
>
> 说明：**NULL 值**  
> AVG 函数忽略列值为 NULL 的行

#### COUNT 函数

COUNT 函数进行计数，确定表中行的数目或符合特定条件的行的数目。

COUNT 函数有两种使用方式：

1. 使用 COUNT(\*)对表中行的数目进行计数，不管表列中包含的是空值（NULL）还是非空值。
2. 使用 COUNT(column)对特定列中具有值的行进行计数，忽略 NULL 值的行。

```sql
-- 所有行，包括空值
SELECT COUNT(*) AS num_table1 FROM table1;
-- 特定列，不含空值
SELECT COUNT(column1) AS num_column1 FROM table1;
```

#### MAX 函数

MAX 函数返回指定列中的最大值，**MAX 要求指定列名**。

```sql
SELECT MAX(column1) AS max_column1 FROM table1;
```

> 提示：**对非数值数据使用 MAX**  
> 虽然 MAX 函数一般用来找出最大的数值或日期值，但许多（并非所有）DBMS 允许将它用来返回任意列中的最大值，包括返回文本列中的最大值。在用于文本数据时，MAX 函数返回按列排序后的最后一行
>
> 说明： **NULL 值**  
> MAX 函数总是忽略值为 NULL 的行

#### MIN 函数

MIN 函数返回指定列的最小值，**MIN 要求指定列名**。

```sql
SELECT MIN(column1) AS min_column1 FROM table1;
```

> 提示：**对非数值数据使用 MIN**  
> 虽然 MIN 函数一般用来找出最小的数值或日期值，但许多（并非所有）DBMS 允许将它用来返回任意列中的最小值，包括返回文本列中的最小值。在用于文本数据时，MIN 函数返回按列排序后的第一行
>
> 说明： **NULL 值**  
> MIN 函数总是忽略值为 NULL 的行

#### SUM 函数

SUM 函数用来返回指定列值的和。

```sql
-- 合计值
SELECT SUM(column1) AS sum_column1 FROM table1;
-- 合计计算值
SELECT SUM(column1 * column2) AS multi_column FROM table1;
```

> 提示：**在多个列上进行计算**  
> 所有聚集函数都可以用来执行多个列上的计算
>
> 说明：**NULL 值**  
> SUM 函数忽略值为 NULL 的行

### 聚集不重复值

以上五个函数都可以如下使用：

1. 对所有行执行计算，指定 ALL 参数或不指定参数（因为 ALL 是默认行为）

2. 只包含不重复值，指定 DISTINCT 参数

说明：**不要在 Access 中使用**

Microsoft Access 在聚集函数中不支持 DISTINCT（下面的例子不适用），要在 Access 中得到类似结果，需要使用子查询把 DISTINCT 数据返回到外部的 SELECT COUNT(\*)语句

```sql
-- 使用DISTINCT后得到的平均值可能偏高或偏低，因为当多个行有相同数据时，排除重复数据时会改变平均值
SELECT AVG(DISTINCT column1) AS avg_column1 FROM table1;
```

注意：

1. **DISTINCT 不能用于 COUNT(\*)**，DISTINCT 在 COUNT 中必须使用列名，不能用于计算或表达式

2. **将 DISTINCT 用于 MAX 与 MIN 函数没有意义**，DISTINCT 从技术上可以用于 MIN 和 MAX 函数，但最大最小值不会考虑重复值，结果都是相同的

3. **其他聚集参数**，有的 DBMS 还支持其他参数，如支持对查询结果的子集进行计算的 TOP 和 TOP PERCENT。

### 组合聚集函数

上述例子都只涉及单个函数，实际上，SELECT 语句可根据需要包括多个聚集函数。

```sql
SELECT COUNT(*) AS num_column, MIN(column1) AS min_column1, MAX(column1) AS max_column1, AVG(column1) AS avg_column1 FROM table1;
```

注意：指定聚集函数结果的别名时，不应该使用表中实际的列名。这样做虽然合法，但许多 SQL 实现不支持，可能会产生错误信息。

## 分组数据

如何对数据进行分组，以便汇总表内容的子集。这涉及两个新的 SELECT 子句：GROUP BY 子句和 HAVING 子句。

之前我们所有计算都是在表的所有数据或匹配特定的 WHERE 子句的数据上进行的，这里我们可以使用分组将数据分为多个逻辑组，对每个组进行聚集计算

```sql
-- GROUP BY 按column1排序并分组
SELECT column1, COUNT(*) AS num_column FROM table1 GROUP BY column1;
```

因为使用了 GROUP BY，就不必指定要计算和估值的每个组了，系统会自动对每个组而不是整个结果进行聚集。

GROUP BY 的重要规定：

- GROUP BY 子句可以包含任意数目的列，因而可以对分组进行嵌套，更细致地进行数据分组
- 如果在 GROUP BY 子句中嵌套了分组，数据将在最后指定的分组上进行汇总
- GROUP BY 子句中列出的每一列都必须是检索列或有效的表达式（不能是聚集函数）。如果在 SELECT 中使用表达式，则必须在 GROUP BY 子句中指定相同的表达式，不能使用别名
- 大多数 SQL 实现不允许 GROUP BY 带有长度可变的数据类型的列（如文本或备注型字段
- 除聚集计算语句外，SELECT 语句中的每一列都必须在 GROUP BY 子句中给出
- 如果分组列中包含具有 NULL 值的行，则 NULL 将作为一个分组返回，如果列中有多行 NULL 值，它们将分为一组
  -GROUP BY 子句必须出现在 WHERE 子句后，ORDER BY 子句前

> 提示：**ALL 子句**  
> SQL Server 等有些 SQL 实现在 GROUP BY 中支持可选的 ALL 子句。这个子句可以用来返回所有分组，即使是没有匹配行的分组也返回（此时，聚集将返回 NULL）。
>
> 注意：**通过相对位置指定列**  
> 有的 SQL 实现允许根据 SELECT 列表中的位置指定 GROUP BY 的列。例如 GROUP BY 2, 1 可表示按 SELECT 的第二个列分组，然后再按第一个列分组。虽然这种速记语法方便，但并非所有 SQL 实现都支持，且使用它易在编辑 SQL 语句时出错。

### 过滤分组

WHERE 过滤指定的是行而不是分组，WHERE 没有分组的概念。此时使用 HAVING 子句过滤分组。所有类型的 WHERE 子句都可以用 HAVING 替代，唯一的差别是，WHERE 过滤行，而 HAVING 过滤分组。

```sql
SELECT column1, COUNT(*) AS num_column FROM table1 GROUP BY column1 HAVING COUNT(*) >= 2;
```

WHERE 在数据分组前进行过滤，HAVING 在数据分组后进行过滤。WHERE 排除的行不包括在分组中，可能会改变计算值，从而影响 HAVING 子句中基于这些值过滤掉的分组。

```sql
SELECT column1, COUNT(*) AS num_column FROM table1 WHERE column2 >= 4 GROUP BY column1 HAVING COUNT(*) >= 2;
```

> 说明：使用 HAVING 和 WHERE  
> HAVING 和 WHERE 非常类似，若不指定 GROUP BY，则大多数 DBMS 会同等对待它们。使用 HAVING 时应该结合 GROUP BY 子句，而 WHERE 子句用于标准的行级过滤。

### 分组和排序

GROUP BY 和 ORDER BY 经常完成相同的工作，但它们非常不同。

| ORDER BY                                     | GROUP BY                                                 |
| -------------------------------------------- | -------------------------------------------------------- |
| 对输出的数据进行排序                         | 对行分组，但输出可能不是分组的顺序                       |
| 任意列都可以使用（甚至非选择的列也可以使用） | 只可能使用选择列或表达式列，而且必需使用每个选择列表达式 |
| 不一定需要                                   | 如果与聚集函数一起使用列（或表达式），则必需使用         |

我们经常发现用 GROUP BY 分组数据确实是以分组顺序输出的，但这不是 SQL 规范所要求的。**因此一般在使用 GROUP BY 子句时， 也应该给出 ORDER BY 子句，这是保证数据正确排序的唯一方法。**千万不要仅依赖 GROUP BY 排序数据。

```sql
SELECT column1, COUNT(*) AS num_column FROM table1 GROUP BY column1 HAVING COUNT(*) >= 3 ORDER BY num_column, column1;
```

Access 不允许按别名排序，因此在 Access 中应使用实际的计算或字段位置替换 items（在 ORDER BY 子句中），即 ORDER BY COUNT(\*), column1 或 ORDER BY 2, column1。

### SELECT 子句顺序

| 子句     | 说明               | 是否必须使用             |
| -------- | ------------------ | ------------------------ |
| SELECT   | 要返回的列或表达式 | 是                       |
| FROM     | 从中检索数据的表   | 仅在从表中选择数据时使用 |
| WHERE    | 行级过滤           | 否                       |
| GROUP BY | 分组说明           | 仅在按组计算聚集时使用   |
| HAVING   | 组级过滤           | 否                       |
| ORDER BY | 输出排序顺序       | 否                       |

## 使用子查询

> **查询（query）**
>
> 任何 SQL 语句都是查询，但查询术语一般是指 SELECT 语句。

SQL 还允许创建子查询（subquery），即嵌套在其他查询中的查询

由于这里我们使用的都是关系型数 据库，客户端最终要使用的数据通常不是来自于单一的一张表，而是通过表的关联关系从多个表中获取数据。从每个表中获取数据都可以单独作为一个查询来执行，可以把一条 SELECT 语句返回的结果用于另一条 SELECT 语句的 WHERE 子句。我们也可以使用子查询把 3 个查询组合成一条语句。

```sql
SELECT column1, column2 FROM table1 WHERE column1_2 IN (
  SELECT column1_2 FROM table2 WHERE column2_3 IN (
    SELECT column2_3 FROM table3 WHERE column3 = 1;
  )
)
```

在 WHERE 子句中使用子查询能够编写出功能很强且灵活的 SQL 语句。对于能嵌套的子查询的数目没有限制，不过在实际使用时由于性能的限制，不能嵌套太多的子查询。

> 注意：**只能是单列**  
> 作为子查询的 SELECT 语句只能查询单个列，企图检索多个列将返回错误。
>
> 注意：**子查询和性能**  
> 使用子查询并不总是执行这类数据检索的最有效方法

### 作为计算字段使用子查询

```sql
SELECT column1, column2, (
  SELECT COUNT(*) FROM table2 WHERE table2.column1_2 = table1.column1_2
) AS column3 FROM table1 ORDER BY column1;
```

这里 table2.column1_2 是完全限定列名，用一个句点分隔表名和列名，在有可能混淆列名时必须使用这种语法。如果在 SELECT 语句中操作多个表，就应使用完全限定列名来避免歧义。

## 联结表

SQL 最强大的功能之一就是能在数据查询的执行中联结（join）表。

相同的数据出现多次并不好，这是关系数据库设计的基础。关系表的设计就是要把信息分解成多个表，一类数据一个表。各表通过某些共同的值互相关联（所以才叫关系数据库）。

将数据分解为多个表能更有效地存储，更方便地处理，且可伸缩性更好，但这也是有代价的。如果数据存储在多个表中，怎样用一条 SELECT 语句就检索出数据呢？答案是使用联结。简单说，联结是一种在一条 SELECT 语句中关联表的机制。使用特殊语法可以联结多个表返回一组输出，联结在**运行时**关联表中正确的行。

```sql
SELECT column1, column2 FROM table1, table2 WHERE table1.column1_2 = table2.column1_2;
```

### WHERE 子句重要性（叉联结）

在联结两个表时，实际要做的是将第一个表中的每一行与第二个表中的每一行配对。WHERE 子句作为过滤条件，只包含那些匹配条件的行。没有 WHERE 子句，第一个表中每一行将与第二个表中的每一行配对，而不管它们逻辑上是否能配在一起。

> **笛卡尔积（cartesian product）**  
> 由没有联结条件的表关系返回的结果为笛卡尔积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。返回笛卡尔积的联结也称叉联结（cross join）

### 内联结

我们常用的联结称为等值联结（equijoin），它基于两个表之间的相等测试，这种联结也称为内联结（innerjoin）。

```sql
SELECT column1, column2 FROM table1 INNER JOIN table2 ON table1.column1_2 = table2.column1_2;
```

此查询语句与前面的查询语句结果完全相同，只是用 INNER JOIN 子句明确了内联结，并用特定的 ON 子句作为联结条件而不是使用 WHERE 子句，但 ON 子句实际传递的条件与 WHERE 相同。

> 说明：**“正确的”语法**  
> ANSI SQL 规范首选 INNER JOIN 语法，WHERE 子句则是简单的等值语法。DBMS 通常都支持简单格式与标准格式的内联结语法。

### 联结多个表

```sql
-- 简单格式
SELECT column1, column2, column3 FROM table1, table2, table3 WHERE table1.column1_2 = table2.column1_2 AND table2.column2_3 = table3.column2_3;
-- 标准格式
SELECT column1, column2, column3 FROM (table1 INNER JOIN table2 ON table1.column1_2 = table2.column1_2) INNER JOIN table3 ON table2.column2_3 = table3.column2_3;
```

DBMS 在运行时关联指定的每个表，以处理联结。这种处理非常耗费资源，因此不要联结不必要的表。联结的表越多，性能越差。虽然 SQL 本身不限制每个联结约束中表的数目，但实际上许多 DBMS 都有限制。

前一章中的嵌套子查询就可以用联结来连接表。

## 创建高级联结

### 使用表别名

```sql
SELECT column1, column2 FROM table1 AS A, table2 AS B WHERE A.column1_2 = B.column1_2;
```

表别名不仅能用于 WHERE 子句，还可以用于 SELECT 的列表、ORDER BY 子句以及其他语句部分。

> 注意：**Oracle 中没有 AS**  
> Oracle 不支持 AS 关键字。要在 Oracle 中使用别名，可以省略 AS，直接指定列名即可。（table1 AS A 直接写为 table1 A）  
> 表别名只在查询执行中使用，与列别名不一样，表别名不返回到客户端

### 自联结

使用表别名的一个主要原因是能在一条 SELECT 语句中不止一次引用相同的表。

```sql
SELECT column1, column2, column3 FROM table1 WHERE column2 = (
  SELECT column2 FROM table1 WHERE column3 = 'foo'
);
```

这里我们要找到 table1 中 column2 与 column3 为 foo 时的 column2 相同的所有数据。第一种方案使用了子查询，我们再来看看使用自联结的方案。

```sql
SELECT t1.column1, t1.column2, t2.column3 FROM table1 AS t1, table1 AS t2 WHERE t1.column2 = t2.column2 AND t2.column3 = 'foo';
```

此查询中使用的两个表其实是相同的两个表，虽然 table1 直接出现两次是完全合法的，但是对 table1 的引用是有歧义的，因为 DBMS 不知道你引用的是哪个 table1 表，会返回错误，因此要使用表别名。

> 提示：**用自联结而不用子查询**  
> 自联结通常作为外部语句，用来替代从相同表中检索数据的子查询语句。虽然最终结果是相同的，但许多 DBMS 处理联结远比处理子查询快得多。

### 自然联结

自然连结（natural join）是一种特殊的等值连结，它要求两个关系中进行比较的分量必须是相同的属性组，并且在结果中把重复的属性列去掉。而等值连结并不去掉重复的属性列。

```sql
-- 内联接（等值联接）
SELECT t1.*, t2.* FROM table1 AS t1 INNER JOIN table2 AS t2 ON t1.column1_2 = t2.column1_2;
-- 自然联接
SELECT t1.*, t2.* FROM table1 AS t1 NATURAL JOIN table2 AS t2;
```

- 等值联结不要求属性值完全相同，自然联结属性值必须完全相同

- 等值联结不把重复属性去掉，自然联结去除了重复属性

- 一般建立的内联结都是自然联结，几乎不会用到非自然联结的内联结

### 外联结

外联结指包含了那些在相关表中没有关联的行的联接。

```sql
SELECT column1, column2 FROM table1 LEFT OUTER JOIN table2 ON table1.column1_2 = table2.column1_2;
```

必须使用 RIGHT 或 LEFT 关键字指定包括所有行的表，LEFT 指左边的表包含所有行，RIGHT 指右边的表包含所有行。

> 注意：**SQLite 仅支持左外联结**  
> 左外联结与右外联结唯一的区别就是表的顺序，可以通过调整表的顺序用左外联结实现右外联结的功能。

全外联接（full outer join），它检索两个表中所有行并关联那些可以关联的行。全外联结包含两个表的不关联的行。

> 注意：**FULL OUTER JOIN 并非所有 SQL 实现支持**  
> Access、MariaDB、MySQL、Open Office Base 和 SQLite 都不支持全外联结

### 使用带聚集函数的联结

```sql
SELECT table1.column1, COUNT(table2.column2) AS num_column FROM table1 LEFT OUTER JOIN table2 ON table1.column1 = table2.column1 GROUP BY table1.column1;
```

- 应该总是提供联结条件，否则会得出笛卡尔积

- 在一个联结中可以包含多个表，甚至可以对每个联结采用不同的联结类型

## 组合查询

SQL 允许执行多个查询（多条 SELECT 语句），并将结果作为一个查询结果返回。这些组合查询通常称为并（union）或复合查询（compound query）。

一般有两种情况需要使用组合查询：

- 在一个查询中从不同的表返回结构数据

- 对一个表执行多个查询，按一个查询返回数据

> 提示：**组合查询和多个 WHERE 条件**  
> 任何具有多个 WHERE 子句的 SELECT 语句都可以作为一个组合查询

```sql
-- 组合查询
SELECT column1, column2, column3 FROM table1 WHERE column4 IN ('foo', 'bar')
UNION
SELECT column1, column2, column3 FROM table1 WHERE column1 = 1;
-- 多WHERE条件
SELECT column1, column2, column3 FROM table1 WHERE column4 IN ('foo', 'bar') OR column1 = 1;
```

在这个简单的例子中，使用 UNION 可能比使用 WHERE 子句更复杂。但对于较复杂的过滤条件，或者从多个表中检索数据的情形，使用 UNION 可能会使处理更简单。

> 提示：**UNION 的限制**  
> SQL 标准没有限制 UNION 组合 SELECT 语句的数目，但是不同的 SQL 实现可能会有不同
>
> 注意：**性能问题**  
> 多数 DBMS 使用内部查询优化程序，在处理各条 SELECT 语句前组合它们。理论上，使用多条 WHERE 子句条件与使用 UNION 组合查询没有实质上的性能差别。

组合查询规则

- UNION 必须由两条或两条以上的 SELECT 语句组成，语句间用关键字 UNION 分隔

- UNION 中的每个查询必须包含相同的列、表达式或聚集函数（次序可不同）

- 列数据类型必须兼容：类型不必完全相同，但是必须是 DBMS 可以隐式转换的类型

### 包含或取消重复的行

UNION 从查询结果集中自动去除了重复的行。若想返回所有匹配行，可使 UNION ALL 替换 UNION

### 对组合查询结果排序

使用 UNION 组合查询时，只能使用一条 ORDER BY 子句，它必须位于最后一条 SELECT 语句之后，对于结果集，不存在用一种方式排序一部分，而又用另一种方式排序另一部分的情况，因此不允许使用多条 ORDER BY 子句。

```sql
SELECT column1, column2, column3 FROM table1 WHERE column4 IN ('foo', 'bar')
UNION
SELECT column1, column2, column3 FROM table1 WHERE column1 = 1
ORDER BY column1, column2;
```

虽然看起来 ORDER BY 只是最后一条 SELECT 语句的组成部分，但实际上 DBMS 将用它来排序所有 SELECT 语句返回的所有结果。

> 说明：**其他类型的 UNION**  
> 某些 DBMS 还支持另外两种 UNION：EXCEPT（有时称为 MINUS）可以用来检索只在第一个表中存在而在第二个表中不存在的行；而 INTERSECT 可用来检索两个表中都存在的行。这些 UNION 比较少用，因为相同的结果可以使用联结得到。
>
> 提示：**操作多个表**  
> UNION 在组合多个表的数据时也很有用，即使是有不匹配列名的表，在这种情况下，可以将 UNION 与别名组合，检索一个结果集

## 插入数据

```sql
INSERT INTO table1 VALUES('foo','bar', 'zoo', NULL);
```

使用这种方式插入数据时必须给每一列提供一个值，如果某列没有值，则应该使用 NULL 值（假定表允许对该列指定空值），各列必须以它们在表定义中出现的次序填充。

> 提示：**INTO 关键字**  
> 在某些 SQL 实现中，跟在 INSERT 后的 INTO 关键字是可选的。但最好还是提供这个关键字以保证代码的可移植性。

虽然这种语法简单，但高度依赖表中列的定义次序，一旦表结构改变，代码就会失效，因此应该尽量避免这种简单语法。

```sql
INSERT INTO table1(column1, column2, column3, column4) VALUES('foo', 'bar', 'zoo', NULL);
```

这个例子与前一个例子结果完全相同，但好处是即使表结构改变，这条语句仍然能生效。

```sql
INSERT INTO table1(column2, column1, column3) VALUES('bar', 'foo', 'zoo');
```

注意：

1. 不要使用没有明确给出列的简单 INSERT 语句，给出列能使 SQL 代码在表结构变化时依然有效

2. 如不提供列名，则必须给每个列提供一个值；若提供列名，则必须给列出的每个列一个值

3. 如果表定义允许，则可以在 INSERT 操作中省略某些列，省略的列必须满足以下某个条件

   - 该列定义允许 NULL 值
   - 在表定义中给出默认值

4. 若表中不允许有 NULL 值或默认值，这时却省略了表中的值，就会产生错误消息，插入失败

### 插入检索出的数据

```sql
INSERT INTO table1(column1, column2, column3) SELECT column1, column2, column3 FROM table2;
```

> 提示：**INSERT SELECT 中的列名**  
> INSERT SELECT 不要求列名匹配，DBMS 不关心 SELECT 返回的列名。它使用的是列的位置，因此 SELECT 中的第一列将用来填充表列中指定的第一列，第二列将用来填充表列中指定的第二列，依次类推。
>
> 提示：**插入多行**
> INSERT 通常只插入一行。要插入多行，必须执行多个 INSERT 语句。INSERT SELECT 是个例外，他可以用一条 INSERT 插入多行，不管 SELECT 语句返回多少行，都将被 INSERT 插入。

### 从一个表复制到另一个表

要将一个表的内容复制到一个全新的表（运行中创建的表），可以使用 SELECT INTO 语句。

> 注意：**DB2 不支持 SELECT INTO**
>
> 注意：**有的 DBMS 可以覆盖已存在的表**

```sql
SELECT * INTO table2 FROM table1;
```

MariaDB、MySQL、Oracle、PostgreSQL 和 SQLite 语法稍有不同：

```sql
CREATE TABLE table2 AS SELECT * FROM table1;
```

SELECT INTO 注意事项

- 任何 SELECT 选项和子句都可以使用，包括 WHERE 和 GROUP BY

- 可以利用联结从多个表插入数据

- 不管从多少个表中检索数据，都只能插入到一个表中

> **进行表的复制**  
> SELECT INTO 是试验新 SQL 语句前进行表复制的好方法，在复制的数据上测试新的 SQL 代码，而不会影响实际的数据。

## 更新和删除数据

### 更新数据

更新（修改）表中的数据，可以使用 UPDATE 语句。有两种使用 UPDATE 的方式：

- 更新表中特定行

- 更新表中所有行

> 注意：**不要省略 WHERE 子句**  
> 在使用 UPDATE 时一定要细心，因为稍不注意就会更新表中的所有行。

UPDATE 语句组成部分：

- 要更新的表

- 列名和它们的新值

- 确定要更新哪些行的过滤条件

```sql
UPDATE table1 SET column1 = 'foo', column2 = 'bar' WHERE column3 = 1;
```

UPDATE 语句以 WHERE 子句结束，它告诉 DBMS 更新哪一行，没有 WHERE 子句，DBMS 将会用新值更新所有行。

> 提示：**在 UPDATE 语句中使用子查询**  
> UPDATE 语句中可以使用子查询，使得能用 SELECT 语句检索出的数据更新列数据。
>
> 提示：**FROM 关键字**  
> 有的 SQL 实现支持在 UPDATE 语句中使用 FROM 子句，用一个表的数据更新另一个表的行。

要删除某个列的值，可以设置为 NULL（假定表定义允许 NULL 值）。这与保存空字符串不同（空字符串用''表示，是一种值），而 NULL 表示没有值。

### 删除数据

从一个表中删除数据，使用 DELETE 语句。有两种使用 DELETE 的方式：

- 从表中删除特定的行

- 从表中删除所有行

> 注意：**不要省略 WHERE 子句**  
> 在使用 DELETE 时一定要细心，因为稍不注意就会更新表中的所有行。

```sql
DELETE FROM table1 WHERE column1 = 'foo';
```

若省略 WHERE 子句，将删除所有行。

> 提示：**友好的外键**  
> 简单的联结两个表，只需要这两个表中有公用字段。我们也可以让 DBMS 通过外键来严格定义关系。存在外键时，DBMS 使用它们判断引用的完整性。这样做的一个好处就是 DBMS 通常可以防止删除某个关系需要用到的行。
>
> 提示：**FROM 关键字**  
> 在某些 SQL 实现中，跟在 DELETE 后的关键字 FROM 是可选的。但是即使不需要也最好提供这个关键字。这样做将保证 SQL 代码在 DBMS 之间的可移植性。

DELETE 不需要列名或通配符。DELETE 删除整行而不是删除列。要删除指定的列，请使用 UPDATE 语句。

> 说明：**删除表的内容而不是表**  
> DELETE 语句从表中删除行，甚至是删除表中所有行。但是，DELETE 不删除表本身。
>
> 提示：**更快的删除**  
> 如果想从表中删除所有行，不要使用 DELETE。可以使用 TRUNCATE TABLE 语句，它完成相同的工作，而速度更快（因为不记录数据的变动）。

使用 UPDATE 与 DELETE 语句时要遵循的重要原则：

1. 除非确实需要更新和删除每一行，否则绝对不要使用不带 WHERE 子句的 UPDATE 或 DELETE 语句

2. 保证每个表都有主键

3. 在使用带有 WHERE 的 UPDATE 或 DELETE 语句前，应该先用 SELECT 进行测试，保证它通过的是正确的记录，以防编写的 WHERE 子句不正确

4. 使用强制实施完整性的数据库，这样 DBMS 将不允许删除其数据与其他表相关联的行

5. 有的 DBMS 允许数据库管理员施加约束，防止执行不带 WHERE 子句的 UPDATE 或 DELETE 语句。若所采用的 DBMS 有这个特性，应该使用它。

若 SQL 没有撤消按钮，应该非常小心地使用 UPDATE 和 DELETE，否则你会发现自己更新或删除了错误的数据。

## 创建和操纵表

### 创建表

SQL 不仅用于表数据操纵，还用来执行数据库和表的所有操作，包括表本身的创建和处理。

一般有两种创建表的方法：

- 多数 DBMS 都有交互式创建和管理数据库表的工具

- 也可以直接使用 SQL 语句操纵

用 SQL 语句创建表，可以使用 SQL 的 CREATE TABLE 语句。使用交互工具实际上也是使用 SQL 语句，这些语句不是用户编写的，交互工具会根据用户的选择自动生成并执行相应的 SQL 语句。

> 提示：**语法差别**  
> 在不同的 SQL 实现中，CREATE TABLE 语句在语法上可能有所不同。

利用 CREATE TABLE 创建表，必须给出下列信息：

1. 新表的名字，在关键字 CREATE TABLE 之后给出

2. 表列的名字和定义，用逗号分隔

3. 有的 DBMS 还要求指定表的位置

```sql
CREATE TABLE table1 (
  column1 CHAR(10) NOT NULL,
  column2 CHAR(10) NOT NULL,
  column3 DECIMAL(8, 2) NOT NULL,
  column4 VARCHAR(1000) NULL，
  column5 INTEGER NOT NULL DEFAULT 1
);
```

这条语句在 Oracle、PostgreSQL、SQL Server、SQLite 中有效，而对于 MySQL，varchar 应替换为 text，对于 DB2，必须从最后一行去掉 NULL。

> 提示：**替换现有的表**  
> 在创建新的表时，指定的表名必须不存在，否则会出错。防止意外覆盖已有的表，SQL 要求先手工删除该表，然后再重建它，而不是简单地用创建表语句覆盖它。

#### 使用 NULL 值

NULL 值就是没有值或缺值。允许 NULL 值的列也允许在插入行时不给出该列的值。不允许 NULL 值的列不接受没有列值的行，也就是说在插入或更新行时，该列必须有值。

每个表列要么是 NULL 列，要么是 NOT NULL 列，这种状态在创建时由表的定义规定。

NULL 为默认设置，如果不指定 NOT NULL，就认为指定的是 NULL。

> 注意：**指定 NULL**  
> 在不指定 NOT NULL 时，多数 DBMS 认为指定的是 NULL，但不是所有 DBMS 都这样。有些 DBMS 要求指定关键字 NULL，如果不指定将出错。
>
> 提示：**主键和 NULL 值**  
> 主键是其值唯一标识表中每一行的列。只有不允许 NULL 值的列可作为主键，允许 NULL 值的列不能作为唯一标识。
>
> 注意：**理解 NULL**  
> 不要把 NULL 与空字符串混淆。NULL 值是没有值，不是空字符串。如果指定''，这在 NOT NULL 列中是允许的。

#### 指定默认值

SQL 允许指定默认值，在插入行时如果不给出值，DBMS 将自动采用默认值。默认值在 CREATE TABLE 语句的列定义中用关键字 DEFAULT 指定。

默认值经常用于日期或时间戳列。例如，通过指定引用系统日期的函数或变量，将系统日期作为默认日期。

许多数据库开发人员喜欢使用 DEFAULT 值而不是 NULL 列，对于用于计算或数据分组的列更是如此。

### 更新表

更新表定义，可以使用 ALTER TABLE 语句。虽然所有的 DBMS 都支持 ALTER TABLE，但它们所允许更新的内容差别很大。以下是使用 ALTER TABLE 时需要考虑的点：

- 理想情况下，不要在表中包含数据时对其进行更新。应该在表的设计过程中充分考虑未来可能的需求，避免今后对表的结构做大改动

- 所有的 DBMS 都允许给现有的表增加列，不过对所增加列的数据类型（以及 NULL 和 DEFAULT 的使用）有所限制

- 许多 DBMS 不允许删除或更改表中的列

- 多数 DBMS 允许重新命名表中的列

- 许多 DBMS 限制对已经填有数据的列进行更改，对未填有数据的列几乎没有限制

使用 ALTER TABLE 更改表结构，必须给出下面的信息：

- 在 ALTER TABLE 之后给出要更改的表名（该表必须存在，否则将出错）

- 列要做出哪些更改

```sql
-- 新增列
ALTER TABLE table1 ADD column6 CHAR(20);
-- 删除列
ALTER TABLE table1 DROP COLUMN column6;
```

复杂的表结构更改一般需要手动删除过程，它有以下几个步骤：

1. 用新的列布局创建一个新表

2. 使用 INSERT SELECT 语句从旧表复制数据到新表。有必要的话可以使用转换函数和计算字段

3. 检验包含所需数据的新表

4. 重命名旧表（如果确定旧表没用，可以删除它）

5. 用旧表原来的名称重命名新表

6. 根据需要，重新创建触发器、存储过程、索引和外键

> 说明：**ALTER TABLE 和 SQLite**  
> SQLite 对使用 ALTER TABLE 执行的操作有所限制。最重要的一个限制是，它不支持使用 ALTER TABLE 定义主键和外键，这些必需在最初创建表时指定。
>
> 注意：**小心使用 ALTER TABLE**  
> 使用 ALTER TABLE 要极为小心，应该在进行改动前做完整的备份（表结构和数据的备份）。数据库表的更改不能撤消，如果增加了不需要的列，也许无法删除它们。类似的，如果删除了不该删除的列，可能会丢失该列中的所有数据。

### 删除表

删除表（删除整个表，而不是其内容）非常简单，使用 DROP TABLE 语句即可：

```sql
DROP TABLE table1;
```

删除表没有确认，也不能撤消，执行这条语句将永久删除该表。

> 提示：**使用关系规则防止意外删除**  
> 许多 DBMS 允许强制实施有关规则，防止删除与其他表相关联的表。在实施这些规则时，如果对某个表发布一条 DROP TABLE 语句，且该表是某个关系的组成部分，则 DBMS 将阻止这条语句执行，直到该关系被删除为止。如果允许，应该启用这些选项，它能防止意外删除有用的表。

### 重命名表

每个 DBMS 对重命名的支持有所不同。DB2、MariaDB、MySQL、Oracle 和 PostgreSQL 用户使用 RENAME 语句，SQL Server 用户使用 sp_rename 存储过程，SQLite 用户使用 ALTER TABLE 语句。所有重命名操作的基本语法都要求指定旧表名和新表名，不过各 DBMS 实现存在差异。

## 使用视图

视图是虚拟的表。与包含数据的表不一样，视图只包含使用时动态检索数据的查询。

> 说明：**DBMS 支持**  
> Access 不支持视图  
> MySQL 从版本 5 起支持视图  
> SQLite 仅支持只读视图，所以视图可以创建，可以读，但其内容不能更改  
> 所有 DBMS 非常一致地支持视图创建语法

视图的常见应用：

- 重用 SQL 语句

- 简化复杂的 SQL 操作。在编写查询后，可以方便地重用它而不用知道其查询细节

- 使用表的一部分而不是整个表

- 保护数据。可以授予用户访问表的特定部分的权限，而不是整个表的访问权限

- 更改数据格式和表示。视图可以返回和底层表的表示和格式不同的数据

创建视图后，可以使用与基本表相同的方式使用它们。重要的是，要知道视图仅仅是用来查看存储在别处数据的一种设施。视图本身不包含数据，因此返回的数据是从其他表中检索出来的。在添加或更改这些表中的数据时，视图将返回改变过的数据。

> 注意：**性能问题**  
> 因为视图不包含数据，所以每次使用视图时，都必须处理查询执行时需要的所有检索。如果你用多个联结和过滤创建了复杂的视图或者嵌套了视图，性能可能会下降得很厉害。因此，在部署使用了大量视图的应用前，应该进行性能测试。

视图的规则和限制：

- 与表一样，视图必须唯一命名（不能给视图取与别的视图或表相同的名字）

- 对于可以创建的视图数目没有限制

- 创建视图必须具有足够的访问权限。这些权限通常由数据库管理人员授予

- 视图可以嵌套。即可以利用从其他视图中检索数据的查询来构造视图。所允许的嵌套层数在不同的 DBMS 中有所不同（嵌套视图可能会严重降低查询性能，因此在生产环境使用前，应该对其进行全面测试）

- 许多 DBMS 禁止在视图查询使用 ORDER BY 语句

- 有些 DBMS 要求对返回的所有列进行命名，如果列是计算字段，则需要使用别名

- 视图不能索引，也不能有关联的触发器或默认值

- 有些 DBMS 把视图作为只读的查询，这表示可以从视图检索数据，但不能将数据写回底层表

- 有些 DBMS 允许创建这样的视图，它不能进行导致行不再属于视图的插入或更新。例如有一个视图，只检索带有电子邮件地址的顾客。如果更新某个顾客，删除他的电子邮件地址，将使该顾客不再属于视图。这是默认行为，而且是允许的，但有的 DBMS 可能会防止这种情况发生。

> 提示：**参阅具体的 DBMS 文档**  
> 上面规则不少，而具体的 DBMS 文档很可能还包含其他规则。因此在创建视图前，有必要了解必须遵守的规定

小结：**视图提供了一种封装 SELECT 语句的层次，可用来简化数据处理，重新格式化或保护基础数据。**

### 创建视图

视图使用 CREATE VIEW 语句创建

> 说明：**视图重命名**  
> 删除视图，可以使用 DROP 语句，其语法为 DROP VIEW viewname；覆盖或更新视图，必须先删除它，然后再重新创建。

#### 利用视图简化复杂的联结

一个最常见的视图应用是隐藏复杂的 SQL，这通常涉及联结。

```sql
CREATE VIEW view1 AS SELECT column1, column2, column3 FROM table1, table2, table3 WHERE table1.column1_2 = table2.column1_2 AND table2.column2_3 = table3.column2_3;
```

从这个视图中获取数据：

```sql
SELECT column1, column3 FROM view1 WHERE column2 = 'foo';
```

当 DBMS 处理此查询时，它将此 WHERE 子句添加到视图查询中已有的 WHERE 子句中，以便正确过滤数据。

视图查询极大地简化了复杂 SQL 语句的使用。利用视图，可以一次性编写基础的 SQL，然后根据需要多次使用。

> 提示：**创建可重用的视图**  
> 创建不绑定特定数据的视图是一种好办法。扩展视图的范围不仅使得它能被重用，而且可能更有用。这样做不需要创建和维护多个类似视图。

#### 用视图重新格式化检索出的数据

```sql
-- 使用+号
CREATE VIEW view1 AS SELECT RTRIM(column1) + ' (' + RTRIM(column2) + ')' AS column1_2 FROM table1;
-- 使用||符号
CREATE VIEW view1 AS SELECT RTRIM(column1) || ' (' || RTRIM(column2) || ')' AS column1_2 FROM table1;
```

#### 用视图过滤不想要的数据

视图对于应用普通的 WHERE 子句也很有用。

```sql
CREATE VIEW view1 AS SELECT column1, column2, column3 FROM table1 WHERE column3 IS NOT NULL;
```

> 说明：**WHERE 子句与 WHERE 子句**  
> 从视图检索数据时如果使用了一条 WHERE 子句，则两组子句（一组在视图中，另一组是传递给视图的）将自动组合。

#### 使用视图与计算字段

```sql
CREATE VIEW view1 AS SELECT column1, column2, column3, column4 * column5 AS column4_5 FROM table1;
```

## 使用存储过程

存储过程就是为以后使用而保存的一条或多条 SQL 语句。可将其视为批文件，虽然它们的作用不仅限于批处理。

> 说明：**具体 DBMS 的支持**  
> Access 和 SQLite 不支持存储过程，MySQL5 前的版本不支持存储过程。这里仅提供 Oracle 和 SQL Server 语法。

### 为什么使用存储过程

- 通过把处理封装在一个易用单元中，可以简化复杂操作。

- 由于不要求反复建立一系列处理步骤，因而保证了数据一致性。如果所有开发人员和应用程序都使用同一存储过程，则所使用的代码都是相同的。这一点的延伸就是防止错误。需要执行的步骤越多，出错的可能性就越大。防止错误保证了数据的一致性。

- 简化对变动的管理。如果表名、列名或业务逻辑有变化，那么只需要更改存储过程的代码。使用它的人员甚至不需要知道这些变化。这一点的延伸就是安全性。通过存储过程限制对基础数据的访问，减少了数据讹误的机会。

- 因为存储过程通常以编译过的形式存储，所以 DBMS 处理命令所需的工作量少，提高了性能。

- 存在一些只能用在单个请求中的 SQL 元素和特性，存储过程可以使用它们来编写更灵活、功能更强大的代码。

存储过程总结下来有三个好处：简单、安全、高性能。不过也有一些缺陷：

- 不同 DBMS 中语法有所不同。编写真正可移植的存储过程几乎不可能，不过存储过程的自我调用（名字与如何传参）可以保持相对可移植。因此，如果需要移植到别的 DBMS，至少客户端应用代码不需要变动。

- 编写存储过程比编写基本的 SQL 语句更复杂，需要更高的技能与更丰富的经验。因此，许多数据库管理员把限制存储过程的创建作为安全措施。

### 执行存储过程

执行存储过程使用 EXECUTE 语句。EXECUTE 接受存储过程名与需要传递给它的任何参数。

```sql
EXECUTE procedure1('param1', 'param2');
```

对于具体 DBMS，可能还包括以下执行选择：

- 参数可选，具有不提供参数时的默认值

- 不按次序给出参数，以'参数=值'的方式给出参数

- 输出参数，允许在存储过程正在执行的应用程序中更新所用的参数

- 用 SELECT 语句检索数据

- 返回代码，允许存储过程返回一个值到正在执行的应用程序

### 创建存储过程

Oracle 示例：

```sql
-- 创建
CREATE PROCEDURE MailingListCount(
  ListCount OUT INTEGER
)
IS
v_rows INTEGER;
BEGIN
  SELECT COUNT(*) INTO v_rows
  FROM Customers
  WHERE NOT cust_email IS NULL;
  ListCount := v_rows;
END;
-- ListCount参数从存储过程中返回一个值，而不是传递一个值给存储过程，关键字OUT指示这种行为。存储过程代码包括在BEGIN、END关键字中
-- 调用
var ReturnValue NUMBER
EXEC MailingListCount(:ReturnValue);
SELECT ReturnValue;
```

SQL Server 示例：

```sql
-- 创建
CREATE PROCEDURE MailingListCount
AS
DECLARE @cnt INTEGER
SELECT @cnt = COUNT(*)
FROM Customers
WHERE NOT cust_email IS NULL;
RETURN @cnt;
-- DECLARE声明名为@cnt的局部变量（局部变量以@开头）
-- 调用
DECLARE @ReturnValue INT
EXECUTE @ReturnValue=MailingListCount;
SELECT @ReturnValue;
```

```sql
CREATE PROCEDURE NewOrder @cust_id CHAR(10)
AS
-- Declare variable for order number
DECLARE @order_num INTEGER
-- Get current highest order number
SELECT @order_num=MAX(order_num)
FROM Orders
-- Determine next order number
SELECT @order_num=@order_num+1
-- Insert new order
INSERT INTO Orders(order_num, order_date, cust_id)
VALUES(@order_num, GETDATE(), @cust_id)
-- Return order number
RETURN @order_num;
```

> 说明：**注释代码**  
> 对代码注释的标准方式是在之前放置两个连字符--。有的 DBMS 支持其他注释语法，不过所有 DBMS 都支持--，因此在注释代码时最好使用这种语法。

## 管理事务处理

使用事务处理（transaction processing），通过确保成批的 SQL 操作要么完全执行，要么完全不执行，来维护数据库的完整性。

事务处理是一种机制，用来管理必须成批执行的 SQL 操作，保证数据库不包含不完整的操作结果。利用事务处理，可以保证一组操作不会中途停止，它们要么完全执行，要么完全不执行（除非明确指示）。如果没有错误发生，整组语句提交给（写到）数据库表；如果发生错误，则进行回退（撤销），将数据库恢复到某个已知且安全的状态。

事务处理需要知道的几个术语：

- 事务（transaction）指一组 SQL 语句

- 回退（rollback）指撤销指定 SQL 语句的过程

- 提交（commit）指将未存储的 SQL 语句结果写入数据库表

- 保留点（savepoint）指事务处理中设置的临时占位符（placeholder），可以对它发布回退（与回退整个事务处理不同）

> 提示：**可以回退哪些语句？**  
> 事务处理用来管理 INSERT、UPDATE 和 DELETE 语句。不能回退 SELECT 语句（回退 SELECT 语句也没有必要），也不能回退 CREATE 或 DROP 操作。事务处理中可以使用这些语句，但进行回退时，这些操作也不撤销。

### 控制事务处理

不同 DBMS 用来实现事务处理的语法有所不同。

管理事务的关键在于将 SQL 语句组分解为逻辑块，并明确规定数据何时应该回退，何时不应该回退。

有的 DBMS 要求明确标识事务处理块的开始和结束。如在 SQL Server 中：

```sql
BEGIN TRANSACTION
-- 事务内的SQL语句
COMMIT TRANSACTION
```

MariaDB 和 MySQL 中：

```sql
START TRANSACTION
-- 事务内的SQL语句
```

Oracle 中：

```sql
SET TRANSACTION
-- 事务内的SQL语句
```

PostgreSQL 使用 ANSI SQL 语法：

```sql
BEGIN
-- 事务内的SQL语句
```

多数 DBMS 实现没有明确标识事务处理在何处结束。事务一直存在，直到被中断。通常 COMMIT 用于保存更改，ROLLBACK 用于撤销。

#### 使用 ROLLBACK

```sql
DELETE FROM table1;
ROLLBACK;
```

#### 使用 COMMIT

一般的 SQL 语句都是针对数据库表直接执行和编写的。这就是所谓的隐式提交（implicit commit），即提交（写或保存）操作是自动进行的。

在事务处理块中，提交不会隐式进行。不过，不同 DBMS 的做法有所不同。有的 DBMS 按隐式提交处理事务端，有的则不这样。

进行明确提交使用 COMMIT 语句。

在 SQL Server 中：

```sql
BEGIN TRANSACTION
DELETE table2 WHERE column1_2 = 1;
DELETE table1 WHERE column1_2 = 1;
COMMIT TRANSACTION;
```

在 Oracle 中：

```sql
SET TRANSACTION
DELETE table2 WHERE column1_2 = 1;
DELETE table1 WHERE column1_2 = 1;
COMMIT;
```

#### 使用保留点

使用简单的 ROLLBACK 和 COMMIT 语句，可以写入或撤销整个事务。但是只对简单的事务才能这样做，复杂的事务可能需要部分提交或回退。

要支持回退部分事务，必须在事务处理块中的合适位置放置占位符。如果需要回退，可以回退到某个占位符。

在 SQL 中，这些占位符称为保留点。在 MariaDB、MySQL 和 Oracle 中创建占位符，可使用 SAVEPOINT 语句。

```sql
SAVEPOINT delete1;
```

在 SQL Server 中：

```sql
SAVE TRANSACTION delete1;
```

每个保留点都要取能够标识它的唯一名字，以便在回退时，DBMS 知道回退到何处。

在 SQL Server 中：

```sql
ROLLBACK TRANSACTION delete1;
```

在 MariaDB、MySQL、Oracle 中：

```sql
ROLLBACK TO delete1;
```

下面是一个完整的 SQL Server 例子：

```sql
BEGIN TRANSACTION
INSERT INTO table1(column1_2, column2)
VALUES(1, 'foo');
SAVE TRANSACTION StartTable1;
INSERT INTO table2(column2_3, column2, column1_2)
VALUES('001', '2020/1/31', 1);
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartTable1;
INSERT INTO table3(column2_3, column2, column3, column4)
VALUES('001', 'BR01', 100, 5.49);
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartTable1;
INSERT INTO table3(column2_3, column2, column3, column4)
VALUES('001', 'BR02', 100, 10.99);
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartTable1;
COMMIT TRANSACTION
```

@@ERROR 变量返回一个非 0 值，表示有错误发生，事务处理回退到保留点。如果整个事务处理成功，发布 COMMIT 保留数据。

可以在 SQL 中设置任意多的保留点，保留点越多越好，这样能够灵活地进行回退。

## 游标

SQL 检索操作返回的所有行称为结果集，结果集都是与 SQL 语句相匹配的行（零行或多行）。简单地使用 SELECT 语句，没有办法得到第一行、下一行或前十行。

游标（cursor）是一个存储在 DBMS 服务器上的数据库查询，它不是一条 SELECT 语句，而是被该语句检索出来的结果集。在存储了游标之后，应用程序可以根据需要滚动或浏览其中的数据。

不同的 DBMS 支持不同的游标选项和特性。常见的一些选项和特性如下：

- 能够标记游标为只读，使数据能读取，但不能更新和删除

- 能控制可以执行的定向操作（向前、向后、第一、最后、绝对位置、相对位置等）

- 能标记某些列为可编辑的，某些列为不可编辑的

- 规定范围，使游标对创建它的特定请求（如存储过程）或所有请求可访问

- 指示 DBMS 对检索出的数据（而不是指出表中活动数据）进行复制，使数据在游标打开和访问期间不变化

> 说明：**具体 DBMS 的支持**  
> Access 不支持游标，MySQL5 以下不支持游标，SQLite 支持的游标称为步骤（step），这里讲述的基本概念适用于 SQLite 的步骤，但语法可能完全不同。

游标主要用于交互式应用，其中用户需要滚动屏幕上的数据，并对数据进行浏览或做出更改。

> 说明：**游标与基于 web 的应用**  
> 游标对基于 web 的应用用处不大。虽然游标在客户端应用和服务器会话期间存在，但这种客户/服务器模式不适合 web 应用，因为应用服务器是数据库客户端而不是最终用户。所以，大多数 web 应用开发人员不使用游标，他们根据自己需要重新开发相应功能

使用游标涉及几个明确的步骤：

1. 在使用游标前，必须声明它。这个过程实际上没有检索数据，它只是定义要使用的 SELECT 语句和游标选项

2. 一旦声明，就必须打开游标以供使用。这个过程用前面定义的 SELECT 语句把数据实际检索出来

3. 对于填有数据的游标，根据需要取出各行

4. 在结束游标使用时，必须关闭游标，可能的话，释放游标（依赖于具体的 DBMS）

声明游标后，可根据需要频繁地打开和关闭游标。在游标打开时，可根据需要频繁地执行取操作。

### 创建游标

使用 DECLARE 语句创建游标，这条语句在不同的 DBMS 中有所不同。

在 DB2、MariaDB、MySQL 和 SQL Server 中：

```sql
DECLARE cusor1 CURSOR FOR
SELECT * FROM table1 WHERE column1 IS NULL;
```

在 Oracle 和 PostgreSQL 中：

```sql
DECLARE CURSOR cursor1 IS
SELECT * FROM table1 WHERE column1 IS NULL;
```

### 使用游标

使用 OPEN CURSOR 语句打开游标，大多数 DBMS 中语法相同：

```sql
OPEN CURSOR cursor1;
```

在处理 OPEN CURSOR 语句时执行查询，存储检索出的数据以供浏览和滚动。

现在可以用 FETCH 语句访问游标数据了。FETCH 指出要检索哪些行，从何处检索它们以及将它们放于何处（如变量名）。

在 Oracle 中：

```sql
DECLARE TYPE cursor1 IS REF CURSOR
  RETURN table1%ROWTYPE;
DECLARE record1 table1%ROWTYPE
BEGIN
  OPEN cursor1;
  FETCH cursor1 INTO record1;
  CLOSE cursor1;
END;
```

本例中，FETCH 用来检索当前行（自动从第一行开始），放到声明的变量 record1 中。对检索出的数据不做任何处理。

```sql
DECLARE TYPE cursor1 IS REF CURSOR
  RETURN table1%ROWTYPE;
DECLARE record1 table1%ROWTYPE
BEGIN
  OPEN cursor1;
  LOOP
  FETCH cursor1 INTO record1;
  EXIT WHEN cursor1%NOTFOUND;
  END LOOP;
  CLOSE cursor1;
END;
```

本例对检索出的数据进行循环。

### 关闭游标

游标在使用完毕时需要关闭，此外，SQL Server 等 DBMS 要求明确释放游标所占用的资源。

在 DB2、Oracle、PostgreSQL 中：

```sql
CLOSE cursor1;
```

在 SQL Server 中：

```sql
CLOSE cursor1;
DEALLOCATE CURSOR cursor1;
```

CLOSE 语句用来关闭游标。一旦游标关闭，如果不再次打开，将不能使用。第二次使用它时不需要再声明，只需要使用 OPEN 再次打开它。

## 高级 SQL 特性

### 约束（constraint）

约束是指管理如何插入或处理数据库数据的规则。

为什么不在客户端层面实施数据库完整性规则？

1. 若在客户端层面实施数据库完整性规则，则每个客户端都要实施

2. 执行 UPDATE 和 DELETE 操作时也必须实施这些规则

3. 执行客户端检查非常耗时，而 DBMS 更高效

DBMS 通过在数据库表上施加约束来实施引用完整性。大多数约束是在表定义中定义的，即在使用 CREATE TABLE 或 ALTER TABLE 语句时。

#### 主键

主键是一种特殊约束，用来保证一列（或一组列）中的值是唯一的，且永不改动。没有主键，要安全地 UPDATE 或 DELETE 特定行而不影响其他行会非常困难。

主键的条件：

- 任意两行的主键值都不相同

- 每行都具有一个主键值（即列中不允许有 NULL 值）

- 包含主键的列从不修改或更新（大多数 DBMS 不允许这样做）

- 主键值不能重用。如果从表中删除某行，其主键值不分配给新行

主键使用 PRIMARY KEY 关键字

```sql
-- 创建表时
CREATE TABLE table1(
  column1 CHAR(10) NOT NULL PRIMARY KEY,
  column2 CHAR(50) NULL
);
-- 修改表时
ALTER TABLE table1 ADD CONSTRAINT PRIMARY KEY (column1);
```

> 说明：**SQLite 中的键**  
> SQLite 不允许使用 ALTER TABLE 定义键，要求在初始的 CREATE TABLE 语句中定义它。

#### 外键

外键是表中的一列，其值必须是另一个表的主键列值。外键是保证引用完整性的极其重要部分。

外键使用 REFERENCES 关键字

```sql
-- 创建表时
CREATE TABLE table1 (
  column1 INTEGER NOT NULL PRIMARY KEY,
  column2 DATETIME NOT NULL,
  column3 CHAR(10) NOT NULL REFERENCES table2(column1)
);
-- 修改表时
ALTER TABLE table1 ADD CONSTRAINT FOREIGN KEY (column3) REFERENCES table2 (column1);
```

> 提示：**外键有助于防止意外删除**  
> 在定义外键后，DBMS 不允许删除在另一个表中具有关联行的行。有的 DBMS 支持级联删除（cascading delete）的特性。如果启用，该特性在从一个表中删除行时删除所有相关的数据。

#### 唯一约束

唯一约束用来保证一列（或一组列）中的数据是唯一的。它们类似于主键，但存在以下重要区别：

- 表可以包含多个唯一约束，但每个表只允许一个主键

- 唯一约束可以包含 NULL 值

- 唯一约束可以修改或更新

- 唯一约束列的值可重复使用

- 与主键不一样，唯一约束不能用来定义外键

唯一约束既可以用 UNIQUE 关键字在表定义中定义，也可以用单独的 CONSTRAINT 定义。

#### 检查约束

检查约束用来保证一列（或一组列）中的数据满足一组指定的条件。检查约束的常见用途有：

- 检查最小或最大值

- 指定范围

- 只允许特定值

数据类型限制了列中可保存的数据类型。检查约束在数据类型内又做了进一步的限制，不需要依赖于客户端应用程序或用户来保证正确获取它，DBMS 本身将拒绝任何无效的数据。

检查约束使用 CHECK 关键字

```sql
-- 创建表时
CREATE TABLE table1 (
  column1 INTEGER NOT NULL,
  column2 CHAR(10) NOT NULL CHECK (column2 > 0)
);
-- 修改表时
ALTER TABLE table1 ADD CONSTRAINT CHECK (column2 > 0);
```

> 提示：**用户定义数据类型**  
> 有的 DBMS 允许用户定义自己的数据类型。定制数据类型的优点是只需施加一次约束（在数据类型定义中），而每当使用该数据类型时，都会自动应用这些约束。

### 索引

索引用来排序数据以加快搜索和排序操作的速度。

主键数据总是排序的，按主键检索特定行总是一种快速有效的操作。但是，搜索其他列中的值通常效率不高，解决方法是使用索引。可以在一个或多个列上定义索引，使 DBMS 保存其内容的一个排序列表。DBMS 搜索排过序的索引，找出匹配的位置，然后检索这些行。

创建索引的注意事项：

- 索引改善检索操作的性能，但降低了数据插入、修改和删除的性能。在执行这些操作时，DBMS 必须动态地更新索引

- 索引数据可能要占用大量的存储空间

- 并非所有数据都适合做索引。取值不多的数据不如具有更多可能值的数据，能通过索引得到那么多的好处

- 索引用于数据过滤和数据排序。若你经常以某种特定的顺序排序数据，则该数据可能适合做索引

- 可以在索引中定义多个列

大多数 DBMS 提供了可以用来确定索引效率的实用程序，应该经常使用这些实用程序。

索引用 CREATE INDEX 语句创建（不同的 DBMS 创建索引的语句变化很大），索引必须唯一命名。

```sql
CREATE INDEX column1_index ON table1 (column1);
```

> 提示：**检查索引**  
> 索引的效率随表数据的增加或改变而变化。过去创建的某个理想的索引经过几个月的数据处理后可能变得不再理想了。最好定期检查索引，并根据需要对索引进行调整。

### 触发器

触发器是特殊的存储过程，它在特定的数据库活动发生时自动执行。触发器可以与特定表上的 INSERT、UPDATE 和 DELETE 操作（或组合）相关联。

与存储过程不一样。存储过程只是简单的 SQL 存储 SQL 语句，触发器与单个表相关联。

触发器内的代码具有以下数据的访问权：

- INSERT 操作中的所有新数据

- UPDATE 操作中的所有新数据和旧数据

- DELETE 操作中删除的数据

根据 DBMS 不同，触发器可在特定操作执行之前或之后执行

触发器常见用途：

- 保证数据一致。如在 INSERT 或 UPDATE 操作中转换数据格式

- 基于某个表的变动在其他表上执行活动

- 进行额外的验证并根据需要回退数据

- 计算计算列的值或更新时间戳

不同 DBMS 触发器语法差异很大

```sql
-- SQL Server
CREATE TRIGGER column1
ON table1
FOR INSERT, UPDATE
AS
UPDATE table1
SET column1 = Upper(column1)
WHERE table1.column1 = inserted.column1;
-- Oracle、PostgreSQL
CREATE TRIGGER column1
AFTER INSERT OR UPDATE
FOR EACH ROW
BEGIN
UPDATE table1
SET column1 = Upper(column1)
WHERE table1.column1 = :OLD.column1
END;
```

> 提示：**约束比触发器更快**  
> 一般来说，约束的处理比触发器快，因此在可能的时候，应该尽量使用约束。

### 数据库安全

大多数 DBMS 都给管理员提供了管理机制，利用管理机制授予或限制对数据的访问。

安全性使用 SQL 的 GRANT 和 REVOKE 语句来管理，不过大多数 DBMS 提供了交互式管理实用程序，这些程序底层实际上还是在使用 GRANT 和 REVOKE 语句。
