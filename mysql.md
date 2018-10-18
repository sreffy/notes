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


### 数据过滤

能够用来联结或改变where子句中子句的关键字称为逻辑操作符，包括AND,OR,IN,NOT IN等

__AND指示DBMS只返回满足所有给定条件的行，每添加一个过滤条件就需增加一个操作符__

__OR操作符指示MySQL检索匹配任一条件的行__

SQL（像多数语言一样）在处理OR操作符前，优先处理AND操作符。因此在多个AND和OR进行组合
时，由于AND在计算次序中优先级更高，操作符可能会被错误地组合，解决方法是使用圆括号明确地分组相应的操作符。

select col_name1,col_name2 from table
where col_name1 in (1,2,3);(检索col_name1列为1,2,3的所有行)

__IN操作符用来指定条件范围，范围中的每个条件都可以进行匹配,IN取合法值的由逗号分隔的清单，全都括在圆括号中，IN实际上是OR操作符的简单形式__

（1）在使用长的合法选项清单时， IN操作符的语法更清楚且更直观。
（2）在使用IN时，计算的次序更容易管理（因为使用的操作符更少）。
（3）IN操作符一般比OR操作符清单执行更快。
（4）IN的最大优点是可以包含其他SELECT语句，使得能够更动态地建
立WHERE子句。

__NOT操作符有且只有一个功能，那就是否定它之后所跟的任何条件，MySQL支持使用NOT对IN、BETWEEN和EXISTS子句取反__


利用通配符可创建比较特定数据的搜索模式。

__通配符（wildcard） 用来匹配值的一部分的特殊字符__

__搜索模式（search pattern）由字面值、通配符或两者组合构成的搜索条件__

为在搜索子句中使用通配符，必须使用LIKE操作符。 LIKE指示MySQL后跟的搜索模式利用通配符匹配而不是直接相等匹配进行比较。

__%通配符表示任何字符出现的任意次数，例如找出所有以jet开头的产品__

select prod_id, prod_name
from products
where prod_name like 'jet%'

__根据mysql的配置方式，搜索可以是区分大小写的__

重要的是要注意到，除了一个或多个字符外，%还能匹配0个字符。%代表搜索模式中给定位置的0个、 1个或多个字符，但不可匹配NULL

__/_通配符只匹配单个字符，而不匹配多个字符__

__使用通配符的技巧__

（1）不要过度使用通配符。如果其他操作符能达到相同的目的，应该
使用其他操作符。
（2）在确实需要使用通配符时，除非绝对有必要，否则不要把它们用
在搜索模式的开始处。把通配符置于搜索模式的开始处，搜索起
来是最慢的。
（3）仔细注意通配符的位置。如果放错地方，可能不会返回想要的数据。

__正则表达式进行搜索__

通过REGEXP语句来使用正则表达式
