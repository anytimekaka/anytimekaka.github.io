---
layout:     post
title:      "SQL简明教程"
subtitle:   "Problems look mighty small from 150 miles up"
date:       2015-02-24 22:30:00
author:     "anytimekaka"
header-img: "img/post-bg-06.jpg"
---

##数据类型

数据库的数据类型有很多，各个数据库的实现也有所不同，这里只列出常用的几个类型，并做详细说明（以MySQL数据库为例）。

1. char(n)：char是定长的，也就是当你输入的字符小于你指定的数目时，char(8)，你输入的字符小于8时，它会再后面补空值。当你输入的字符大于指定的数时，它会**截取**超出的字符。最多存储255个字符。
2. varchar(n)：存储变长数据，但存储效率没有char高。如果一个字段可能的值是不固定长度的，我们只知道它不可能超过10个字符，把它定义为 VARCHAR(10)是最合算的。**VARCHAR类型的实际长度是它的值的实际长度+1**。为什么“+1”呢？这一个字节用于保存**实际使用了多大的长度**。从空间上考虑，用varchar合适；从效率上考虑，用char合适，关键是根据实际情况找到权衡点。最多存储255个字符。
3. int(n)：n表示最大位数。
4. double(n,d)：n表示最大位数，d表示小数点后面的最大位数。
5. decimal(n,d)：作为字符串存储的double类型。
6. date：格式"YYYY-MM-DD"

关于更多SQL数据类型的描述，可参考<a href="http://www.w3school.com.cn/sql/sql_datatypes.asp">这里</a>

##DDL
    create database database_name
    create table r(A1 D1,A2 D2,......An Dn,完整性约束1,......,完整性约束k);
    Drop table r;
    Alter table r add A D; A：要添加的属性的名字 D：要添加的属性的类型（关系中的所有元组在该新属性的取值将被设置为null）；
    create view ; alter view ; delete view;
    truncate table;

>**truncate和delete的区别：**
>delete from后面可以写条件，truncate不可以；delete from记录是一条条删的，所删除的每行记录都会进日志，而truncate一次性删掉整个页，因此日至里面只记录页释放，简言之，delete from更新日志，truncate基本不，所用的**事务日志**空间较少；delete from删空表后，会保留一个空的页，truncate在表中不会留有任何页；当使用行锁执行 DELETE 语句时，将锁定表中各行以便删除，truncate始终锁定表和页，而不是锁定各行；如果有identity产生的自增id列，delete from后仍然从上次的数开始增加，即种子不变，而truncate后，种子会恢复初始;truncate不会触发delete的触发器，因为truncate操作不记录各个行删除。

##DML
    Delete table r;删除关系中所有的元组
    INSERT INTO table_name VALUES (值1, 值2,....);
    INSERT INTO table_name (列1, 列2,...) VALUES (值1, 值2,....);
    update table_name set col1='' where col2='';

####DQL Data Query Language 

示例数据库结构：

<img src="http://imagekaka.qiniudn.com/示例数据库的表关系.png" alt="示例数据库结构" style="display:inline;text-align:center"></img>

    Select A1,A2,......An from R1,R2,......Rm Where P;
    Select distinct branch_name from loan;
    在select后加入关键词distinct，强行删除重复，SQL允许我们使用all来显式指明不去除重复：
    select all branch_name from loan;
    保留重复元组是默认的


**where子句**
* 逻辑连词：`and or not`
* 比较运算符：`<、<=、>、>=、=、<>`
* 为简化where子句，SQL提供`between、not between`，
    Select loan_number from loan where amount (not) between 3000 and 5000;

**更名运算**

    Select loan_number as loan_id from loan;
    Select customer_name, T.loan_number, S.amount from borrower as T, loan as S Where T.loan_number=S.loan_number;

    元组变量在比较同一关系中的两个元组时非常有用
    Select T.branch_name from branch as T, branch as S Where T.assets>S.assets and S.branch_name=’....’;

**字符串运算**

SQL中使用**单引号**来标示字符串，如果单引号是字符串的组成部分，可以使用两个单引号标示：如表示“It’s right”可用：“It’’s right”来标示。

最常见的是使用like运算符的模式匹配（**大小写敏感**）：
* `%`：匹配任意子串
* `_`：匹配任意一个字符

在like比较运算符中，使用**escape关键字**来定义转义字符，例子：

    Like ‘ab\%cd%’ escape ‘\’ 匹配所有以ad%cd开头的字符串
    
**排序**
    Order by desc 表示降序，asc 表示升序，默认升序

**集合运算**

`union intersect except`：并、交、非。

    (select customer_name from depositor) Union (select customer_name from borrower)
    表示在银行有账户、有贷款或者两者都有的客户

Union自动除去重复，如果想保留，使用`union all`（如果Jeason在银行中有三个账户和	两笔贷款，那么将会有五个元组包含Jeason）
    
    (select customer_name from depositor) intersect (select customer_name from borrower)
    表示在银行有账户并且有贷款的客户
    
intersect自动除去重复，如果想保留，使用`intersect all`（如果Jeason在银行中有三个账户和两笔贷款，那么将会有2个元组包含Jeason）

except：(oracle中为minus)

    (select customer_name from depositor) except (select customer_name from borrower)
    表示在银行有账户但是没有贷款的客户。
    
except自动除去重复，如果想保留，使用`except all`（如果Jeason在银行中有三个账户和两笔贷款，那么将会有1个元组包含Jeason；如果Jeason在银行中有两个账户和三笔贷款，那么结果没有元组包含Jeason）

**聚集函数**

`avg min max sum count`

使用聚集函数是可以使用`group by`和`having`作为辅助：

    Select branch_name,avg(balance) From account Group by branch_name;
    
如果在计算聚集函数前先删掉重复元组，可以在聚集函数中使用关键字distinct：

    找出每个支行储户数：
    Select branch_name count(distinct customer_name) From depositor,account Where depositor.account_number=account.account_number Group by branch_name;
    
`Having`子句用于**对分组的限定条件**：

    Select branch_name avg(balance) From account Group by branch_name Having avg(balance)>1000;
    
having子句中的谓词在形成分组后才起作用，因此可以使用聚集函数。

> 如果一个查询语句中同时存在where子句和having子句，那么SQL首先应用where子句中的谓词，满足where谓词的元组通过group by子句形成分组，having子句若在，就将作用于每个分组，不符合having子句谓词的分组将被抛弃。


**空值**

`Is null, is not null`：

    Select loan_number from loan where amount is null;
    
> 如果算术运算的输入有一个是空值，则该算术表达式（+、-、\*、/）的结果是空，如果有空值参与比较运算，SQL将比较结果看成`unknown`

> Unknown可以被布尔运算符处理
>
>     Select ... From R1,R2,R3... Where P;
> 查询结果中包含使得P为true的元组，如果元组使得P的值为false或者unknown，那么该元组就不会被添加到结果中去
>
> `Is unknown , is not unknown` 判断比较结果是不是unknown 


**嵌套子查询**

`in, not in`:

    Select distinct customer_name from borrower where customer_name in (select customer_name from depositor);

`some, all`
  
    Select branch_name from branch where assets > some (select assets from branch where branch_city=’Brooklyn’);
    some表示至少比某一个要大
    
还有=some,=some和<>some（=some等价于in）

    Select branch_name from branch where assets > all (select assets from branch where branch_city=’Brooklyn’);
    all：比所有的都大

还有=all,=all和<>all（<>all等价于not in）


SQL中**聚集函数不能进行组合使用**，因而使用max(avg(...))是不允许的

问题：找出平均余额最高的支行

    Select branch_name from account group by branch_name having avg(balance)>=all(select avg(balance) from account group by branch_name);
    
**测试是否为空关系**

`exists`：测试一个子查询的结果中是否有元组,exists结构在作为参数的子查询非空时返回true

    查找在银行既有账户又有贷款的客户
    Select customer_name from borrower where exists (select * from depositor Where depositor.customer_name=borrower.customer_name)

`Not exists`：可以模拟集合包含运算

关系A包含B：not exists(B except A)

    查找在Brooklyn所有支行都有账户的客户
    Select distinct S.customer_name From depositor as S Where not exists ((select branch_name from branch Where branch_city=’Brooklyn’) Except (select R.branch_name From depositor as T,account as R Where T.account_number=R.account_number and S.customer_name=T.customer_name))
    
**测试是否存在重复元组**

关键字：`unique`，如果作为参数的子查询的结果中没有重复的元组，unique结构将返回true

例：查找所有在Perryridge支行中只有一个账户的客户

    Select S.customer_name from depositor as S where unique (select R.customer_name from account,depositor as R where account.account_number=R.account_number and Account.branch_name=’Perryridge’ and R.customer_name=T.customer_name)
    
`not unique`：

例：找出所有在Perryridge支行中有两个以上账户的客户

    Select distinct T.customer_name from depositor as T where not unique (select R.customer_name from account,depositor as R Where account.account_number=R.account_number and Account.branch_name=’Perryridge’ and R.customer_name=T.customer_name)


####复杂查询
**派生关系**

`as`：对from子句中的查询表达式产生的结果命名

    找出平均账户余额大于1200的支行的平均账户余额
    Select branch_name avg_balance from (select branch_name,avg(balance) From account Group by branch_name) As branch_avg (branch_name,avg_balance) Where avg_balance>1200;
    
可以用于替换having子句


###视图

任何不是逻辑模型的一部分但作为虚关系对用户可见的关系称为视图（view）

    Create view view_name as <查询表达式>
    Create view branch_total_loan(branch_name,total_loan) as Select branch_name sum(amount) From loan  Group by branch_name;
    
因为表达式sum(amount)没有名称，其属性名需要在视图中显示指定

>视图的一般实现：当我们定义一个视图时，数据库系统**存储视图的定义**本身，而不存储定义该视图的关系表达式的计算结果，一旦视图关系出现在查询中，他就被已存储的关系表达式代替，因此，无论何时我们执行这个查询，视图关系都被重新计算。

>**物化视图**：有些特定数据库允许视图关系被存储，但是它们保证如果用于定义视图的实际关系发生改变，视图也跟着修改。

>如果需要频繁使用一个视图，物化视图比较合适；那些需要快速响应基于视图的特定查询的应用也是如此。


###连接关系
  
<table>
<tr><th>连接类型</th><th>连接条件 </th></tr>
<tr><td>Inner join</td><td> Natural</td></tr>
<tr><td>Left outer join</td><td>On<谓词></td></tr>
<tr><td>Right outer join</td><td>Using(A1,A2...An)</td></tr>
<tr><td>Full outer join</td><td></td></tr>
</table>

关键字inner和outer是可选的。

对于外连接，连接条件是必须的；但是对于内连接，连接条件则是可选的（如果省略，将产生笛卡尔积）。On和using出现在连接表达式的末尾。

示例表内容：

**loan:**
 
loan_number	branch_name	amount

L-170	Downtown	3000

L-230	Redwood	4000

L-260	Perryridge	1700


**borrower:**

customer_name	loan_number

Jones	L-170

Smith	L-230

Hayes	L-155


    内连接
    loan inner join borrower on loan.loan_number = borrower.loan_number;
    
loan_number	branch_name	amount	customer_name	loan_number

L-170	Downtown	3000	Jones	L-170

L-230	Redwood	4000	Smith	L-230


所得结果的属性由左侧关系的属性后跟右侧关系的属性构成。 

    左外连接
    loan left outer join borrower on loan.loan_number = borrower.loan_number;
 

loan_number	branch_name	amount	customer_name	loan_number

L-170	Downtown	3000	Jones	L-170

L-230	Redwood	4000	Smith	L-230

L-260	Perryridge	1700	null	null


左外连接：先计算内连接的结果，然后，对左边关系（loan）中每个在内连接时与右边关系（borrower）中的任何元组都不匹配的元组t，向结果中加入一个元组r，r左边关系属性值为t，其他属性为null。

    自然连接
    loan natural inner join borrower;

 
loan_number	branch_name	amount	customer_name

L-170	Downtown	3000	Jones

L-230	Redwood	4000	Smith


自然连接：连接属性（公共的属性）首先出现，按照它们在左侧的关系中的顺序，然后是左侧关系中所有的非连接属性，最后是右侧关系中所有的非连接属性。

全连接是左外连接和右外连接的组合

    loan full outer join borrower using (loan_number);

loan_number	branch_name	amount	customer_name

L-170	Downtown	3000	Jones

L-230	Redwood	4000	Smith

L-260	Perryridge	1700	null

L-155	null	null	Hayes