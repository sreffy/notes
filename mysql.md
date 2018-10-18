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

### 正则表达式进行搜索

通过REGEXP语句来使用正则表达式

select prod_name
from products
where prod_name REGEXP '1000'
order by prod_name;

检索列prod_name包含文本1000的所有行。其中'1000'可以换成正则表达式'.000'，则可以匹配出文本包含'1000','2000'等数据。__.表示匹配任意一个字符__

__LIKE和REGEXP有一个重要差别：LIKE匹配整个列。如果被匹配的文本在列值
中出现， LIKE将不会找到它，相应的行也不被返回（除非使用
通配符）。而REGEXP在列值内进行匹配，如果被匹配的文本在
列值中出现， REGEXP将会找到它，相应的行将被返回。__

若正则表达式匹配想区分大小写，可以使用 REGEXP BINARY语句即可。

__OR匹配，搜索几个串之一使用‘|’，如'1000|2000|3000'，相当于OR条件匹配__

__匹配几个字符之一，使用[1,2,3]或[1-3],[a-z]等，[^123]则是匹配除这些字符以外的任何数据__

__匹配特殊字符，必须使用前导'\\'表示转义，如匹配包含'.'的字符，语句为REGEXP '\\.'__

| 元字符 | 说明     |
| :----- | :--- --- |
| \\f    | 换页     |
| \\n    | 换行      |
|\\r     | 回车     |
|\\t     | 制表     |
| \\v    | 纵向制表 |


__匹配‘\’本身时，则需使用'\\\'__

多数正则表达式实现使用单个反斜杠转义特殊字符，以便能使用这些字符本身。但MySQL要求两个反斜杠（ MySQL自己解释一个，正则表达式库解释另一个）。

__匹配字符类，多个实例以及定位符__


### 创建计算字段

__字段（field） 基本上与列（ column） 的意思相同，经常互换使用，不过数据库列一般称为列，而术语字段通常用在计算字段的连接上。__

__拼接（concatenate） 将值联结到一起构成单个值__

select concat(vend_name,'(',vend_country,')') as vend_title
from vendors
order by vend_name;

Concat()需要一个或多个指定的串，各个串之间用逗号分隔。上面的SELECT语句连接以下4个元素：

(1)存储在vend_name列中的名字；

(2)包含一个空格和一个左圆括号的串；

(3)存储在vend_country列中的国家；

(4)包含一个右圆括号的串。

__AS 可以为字段或值作为替换名__

select prod_id,quantity,item_price,
quantity*item_price as expanded_price
from orderiterms
where order_num = 20005;

__expanded_price列为一个计算字段，此计算为quantity*item_price。客户机应用现在可以使用这个新计算列，就像使用其他列一样。__

### 使用函数

__文本处理函数__

| 函数 | 说明   |
| :------------- | :------------- |
| left()     | 返回串左边的字符       |
|length()    |返回串的长度|
|locate()     |找出串的一个子串|
|lower()     | 将串转换为小写|
|ltrim()     |去掉串左边的空格|
|right()    |返回串右边的字符|
|rtrim()     | 去掉串右边的空格|
|soundex()   | 返回串的SOUNDEX值|
|substring() | 返回子串的字符|
|upper()     |将串转换为大写|

__SOUNDEX是一个将任何文本串转换为描述其语音表示的字母数字模式的算法__

select cust_name,cust_concat
from cutomers
where soundex(cust_concat)= soundex('Y Lie');

其联系名为Y.Lee。但如果这是输入错误，此联系名实际应该是Y.Lie，因为Y.Lee和Y.Lie发音相似，所以它们的SOUNDEX值匹配

__日期处理函数__

| 函数 | 说明   |
| :------------- | :------------- |
| adddate()     | 增加一个日期（天、周等）      |
|addtime()    |增加一个时间（时、分等）|
|curdate()     |返回当前日期|
|curtime()     | 返回当前时间|
|date()     |返回日期时间的日期部分|
|datediff()    |计算两个日期之差|
|date_add()     | 高度灵活的日期运算函数|
|date_format()   | 返回一个格式化的日期或时间串|
|day() | 返回一个日期的天数部分|
|dayofweek()     |对于一个日期，返回对应的星期几|
|hour()     | 返回一个时间的小时部分  |
|minute()    |返回一个时间的分钟部分|
|month()     |返回一个日期的月份部分|
|now()     | 返回当前日期和时间|
|second()     |返回一个时间的秒部分|
|time()    |返回一个日期时间的时间部分|
|year()     |返回一个日期的年份部分|

__指定日期时，不管是插入或更新表值还是用WHERE子句进行过滤，日期必须为格式yyyy-mm-dd__

select cust_id, order_num
from orders
where order_rate = '2005-09-01'；

但如果order_rate是datetime类型，如'2005-09-01 11:30:05'，查询就不准确。为此必须使用Date()函数。 Date(order_date)指示MySQL仅提取列的日期部分


select cust_id, order_num
from orders
where date(order_rate ) between'2005-09-01' and '2006-08-15'；


__数值处理函数__

| 函数 | 说明   |
| :------------- | :------------- |
|abs()     | 返回一个数的绝对值 |
|cos()    |返回一个角度的余弦|
|exp()     |返回一个数的指数值|
|mod()     |返回除操作的余数|
|pi()     |返回圆周率|
|rand()    |返回一个随机数|
|sin()     | 返回一个角度的正弦|
|sqrt()   |返回一个数的平方根|
|tan() | 返回一个角度的正切|



### 汇总数据

__聚集函数__

AVG()通过对表中行数计数并计算特定列值之和，求得该列的平均值，AVG()函数忽略列值为NULL的行

__可利用COUNT()确定表中行的数目或符合特定条件的行的数目__

(1)使用COUNT(\*)对表中行的数目进行计数， 不管表列中包含的是空值（NULL）还是非空值。

(2)使用COUNT(column)对特定列中具有值的行进行计数，忽略NULL值。

__MAX(),MIN()分别返回列最大值和最小值，忽略NULL值__

返回文本时数据按相应的列排序，MIN()返回最前面的行，MAX()返回最后面的行

__SUM()用来返回指定列值的和（总计）,忽略NULL值__

DISTINCT语句与聚集函数一起使用表示只计算包含不同值的数


select avg(distinct prod_price) as avg_price
from products
where vend_id =1003;


### 分组数据

__GROUP BY 数据分组__

(1)GROUP BY子句可以包含任意数目的列。这使得能对分组进行嵌套，
为数据分组提供更细致的控制。

(2)如果在GROUP BY子句中嵌套了分组，数据将在最后规定的分组上
进行汇总。换句话说，在建立分组时，指定的所有列都一起计算
（所以不能从个别的列取回数据）。

(3)GROUP BY子句中列出的每个列都必须是检索列或有效的表达式
（但不能是聚集函数）。如果在SELECT中使用表达式，则必须在
GROUP BY子句中指定相同的表达式。不能使用别名。

(4)除聚集计算语句外， SELECT语句中的每个列都必须在GROUP BY子
句中给出。

(5)如果分组列中具有NULL值，则NULL将作为一个分组返回。如果列
中有多行NULL值，它们将分为一组。

(6)GROUP BY子句必须出现在WHERE子句之后， ORDER BY子句之前。

select ven_id, count(\*) as num_pords
from products
group by ven_id;

__Having 子句过滤分组__

select cust_id,COUNT(\*) as orders
from orders
group by cust_id
having count(\*)>=2

这里有另一种理解方法，WHERE在数据分组前进行过滤， HAVING在数据分组后进行过滤。这是一个重要的区别， WHERE排除的行不包括在分组中。这可能会改变计算值，从而影响HAVING子句中基于这些值过滤掉的分组。

__GROUP BY 和 ORDER BY联用保证分组数据排序__

### 子查询

将两个select查询语句组合使用，在WHERE子句中使用子查询，应该保证SELECT语句具有与WHERE子句中相同数目的列。通常，子查询将返回单个列并且与单个列匹配，但如果需要也可以
使用多个列。

select cust_name,cust_state
      (select count(\*)
        from orders
        where orders.cust_id = customers.cust_id) as orders
from customers
order by cust_name;


### 联结表

__外键（foreign key） 外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系__

select vend_name, prod_name, prod_price
from vendors, products
where vendors.ven_id= products.vend_id
order by vend_name,prod_name;

__在引用的列可能出现二义性时，必须使用完全限定列名（用一个点分隔的表名和列名）。如果引用一个没有用表名限制的具有二义性的列名， MySQL将返回错误。__

__笛卡儿积（cartesian product） 由没有联结条件的表关系返回
的结果为笛卡儿积。检索出的行的数目将是第一个表中的行数乘
以第二个表中的行数，因此应该保证所有联结都有where子句作为联结条件__

__内部联结（等值联结）__

select vend_name, prod_name, prod_price
from vendors, products
where vendors inner join products
on vendors.ven_id= products.vend_id
order by vend_name,prod_name;

两个表之间的关系是FROM子句的组成部分，以INNER
JOIN指定。在使用这种语法时，联结条件用特定的ON子句而不是WHERE
子句给出。传递给ON的实际条件与传递给WHERE的相同

__where子句中使用AND可以表示多个联结关系，进而联结多个表，同时还可以定义行筛选的标准__

__对表使用别名简化代码__

select cust_name,cust_concat
from customers as c, orders as o, ordertimes as oi
where c.cust_id = o.cust_id
  and oi.order_num = o.order_num
  and prod_id ='TNT2'


__自联结__

select p1.prod_id, p1.prod_name
from products as p1, products as p2
where p1.vend_id = p2.vend_id
  and p2.prod_id ='DTNTR';

自联结通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句。虽然最终的结果是
相同的，但有时候处理联结远比处理子查询快得多。


__自然联结__

自然联结排除多次出现，使每个列只返回一次

这一
般是通过对表使用通配符（SELECT \*），对所有其他表的列使用明确的子
集来完成的

__外部联结__

许多联结将一个表中的行与另一个表中的行相关联。但有时候会需要包含没有关联行的那些行。如：

(1)对每个客户下了多少订单进行计数，包括那些至今尚未下订单的
客户；

(2)列出所有产品以及订购数量，包括没有人订购的产品；

(3)计算平均销售规模，包括那些至今尚未下订单的客户。


select customers.cust_id, orders.order_num
from customers left outer join orders
on customers.cust_id = orders.cust_id;

__在使用OUTER JOIN语法时，必须使用RIGHT或LEFT关键字
指定包括其所有行的表（RIGHT指出的是OUTER JOIN右边的表，而LEFT
指出的是OUTER JOIN左边的表）__


__使用联结和联结条件__

注意所使用的联结类型。一般我们使用内部联结，但使用外部联
结也是有效的。

保证使用正确的联结条件，否则将返回不正确的数据。

应该总是提供联结条件，否则会得出笛卡儿积。

在一个联结中可以包含多个表，甚至对于每个联结可以采用不同
的联结类型。虽然这样做是合法的，一般也很有用，但应该在一
起测试它们前，分别测试每个联结。这将使故障排除更为简单。


### 组合查询

有两种基本情况，其中需要使用组合查询：

(1)在单个查询中从不同的表返回类似结构的数据；

(2)对单个表执行多个查询，按单个查询返回数据。

select ven_id, prod_id,prod_price
from products
where prod_price <=5
union
select vend_id, prod_id, prod_price
from products
where ven_id in (1001,1002);

这条语句等价为：

select vend_id, prod_id, prod_price
from products
where prod_price <=5
  or vend_id in (1001,1002);

__UNION从查询结果集中自动去除了重复的行,这是UNION的默认行为，但是如果需要，可以改变它。事实上，如果想返回所有匹配行，可使用UNION ALL而不是UNION__

在用UNION组合查询时，只能使用一条ORDER BY子句，它必须出现在最后一条SELECT语句之后。

### 全文本搜索

一般在创建表时启用全文本搜索。 CREATE TABLE语句接受FULLTEXT子句，它给出被索引列的一个逗号分隔的列表。

__使用Match(),Against()进行全文本搜索__

select note_test
from productnotes
where Match(note_text) Against('rabbit');



__case when 函数__

__简单 case函数__

case sex
when '1' then '男'
when '2' then '女'
else '其他' end

__case搜索函数__

case when sex='1' then '男'
when sex='2' then '女'
else '其他' end

__有一个需要注意的问题，Case函数只返回第一个符合条件的值，剩下的Case部分将会被自动忽略__
--比如说，下面这段SQL，你永远无法得到“第二类”这个结果
CASE WHEN col_1 IN ( 'a', 'b') THEN '第一类'
WHEN col_1 IN ('a')       THEN '第二类'
ELSE'其他' END
