# mysql 必知必会

## 了解SQL

__数据库：保存有组织的数据的容器（通常是一个文件或一组文件）__

__DBMS：数据库管理系统，或者称为数据库软件__

__数据库是通过DBMS创建和操纵的容器，我们使用的是DBMS，用于访问数据库__

__模式(schema)：关于数据库和表的布局及特性的信息__

__主键(primary key)：一列或一组列，其值能够唯一区分表中的每一行__

__主键使用的好习惯：__

（1）不更新主键列中的值；
（2）不重用主键列的值；
（3）不在主键列中使用可能会更改的值。

__MySQL是一种DBMS，SQL是结构化查询语句，用于数据库中__


## MySQL使用

show tables;

show columns from table; == describe table

### 检索数据

select * from table (检索table所有列)

select distinct col_name from table;(对检索的列标签去重)

__distinct语句必须直接放在列名的前面，应用于所有列__

__LIMIT限制结果__

select col_name from table
limit 5; (限制结果为头5行)

select col_name from table
limit 1,1；(限制结果为第2行)

__limit 1,1 指示SQL返回第1行开始的1行，即第二行；limit 5,5即为第5行开始的5行，
指代6~10行，若最大行数不够，仅返回到最大行__

MYSQL5中limit的替代语法为：limit 4 offset 3，等价于limit 3,4

select col_name from table
order by col_name1,col_name2;(按col_name来排序检索结果，可用非检索列排序数据)

__重要的是理解在按多个列排序时，排序完全按所规定的顺序进行，可能只对部分数据排了序__

select col_name1,col_name2 from table
order by col_name1 desc,col_name2;(按col_name1降序，col_name2升序进行排序)

__DESC关键字只应用到直接位于其前面的列名，果想在多个列上进行降序排序， 必须
对每个列指定DESC关键字__

__order by 和 limit指令的组合可以找出一列中最高或最低的值__

select * from table
where ... order by ...

__在SELECT语句中，数据根据WHERE子句中指定的搜索条件进行过滤,在同时使用ORDER BY和
WHERE子句时，应该让ORDER BY位于WHERE之后， 否则将会产生错误__

__<>操作符表示‘不是’，between.. and...表示值位于两个值之间__

__NULL(无值),它与字段包含0、空字符串或仅仅包含空格不同，WHERE子句中IS NULL可以
用来检查具有NULL值的列__
