---
title: PL/SQL quick start
date: 2018-01-30 12:56:19
tags: 技术
---

Procedure Language/SQL是一门编程语言，Oracle公司对SQL进行了扩展，扩展后的SQL，就叫PL/SQL，功能更强大，面向过程语言，模块化编程语言，用于开发基于数据库的应用程序。下面关于PL/SQL编程基础主要介绍两点PL/SQL程序块于PL/SQL语句。

[PL/SQL Tutorial](https://www.tutorialspoint.com/plsql/index.htm)

### 程序块

#### 定义

PL/SQL程序的基本单元，按照指定的方式，进行定义的一段程序。PL/SQL 块中可以包含子块，子块可以位于PL/SQL 中的任何部分。

	DECLARE
	--声明部分: 在此声明 PL/SQL 用到的变量,类型及游标，以及局部的存储过程和函数
	BEGIN
	-- 执行部分: 过程及 SQL 语句 , 即程序的主要部分
	EXCEPTION
	-- 执行异常部分: 错误处理
	END;


#### 分类

##### 匿名块
  不能被其他程序调用
  可以临时使用
  匿名块的代码不会存储在数据库中


##### 命名块
  触发器
  存储过程
  函数
  包
  包体
  有名字的程序块
  可以重复调用
  会被编译并存储在数据库中


##### 子程序
  命名块
  有时候特指存储过程和函数


### 标识符

PL/SQL 程序设计中的标识符定义与SQL 的标识符定义的要求相同。要求和限制有：


* 标识符名不能超过30字符
* 第一个字符必须为字母
* 不分大小写
* 不能用’-‘(减号)
* 不能是SQL 保留字


一般不要把变量名声明与表中字段名完全一样，如果这样可能得到不正确的结果。建议的命名方法：

| 标识符  | 命名规则            | 例子              |
|:-----|:----------------|:----------------|
| 程序变量 | V_name          | V_name          |
| 程序变量 | C_Name          | C_company_name  |
| 游标变量 | Cursor_Name     | Cursor_Emp      |
| 异常标识 | E_name          | E_too_many      |
| 表类型  | Name_table_type | Emp_record_type |
| 表    | Name_table      | Emp             |
| 记录类型 | Name_record     | Emp_record      |
| 替代变量 | P_name          | P_sal           |
| 绑定变量 | G_name          | G_year_sal      |


### 数据类型

Oracle数据类型大致分为：character,numberic,date,lob和raw等，这些是最基本的Oracle数据类型。当然在Oracle中也允许自定义数据类型。

#### character数据类型

char(<size>:固定长度字符串，最大长度为2000字节，如果不指定长充，缺省为1个字节长。
varchar2(<size>:可变长度的字符串，最大长度为4000字节，具体定义时指明最大长度，这种类型可以放数字、字母以及ASCII码字符集(或者EBCDIC等数据库系统接受的字符集标准)中的所有符号。如果数据长度没有达到最大值，Oracle会根据数据大小自动调节字段长度。是最长用的数据类型。
nchar(<size>:根据字符集而定的固定长度字符串，最大长度2000字节。
nvarchar2(<size>:根据字符集而定的可变长度字符串，最大长度4000字节。
long:可变长字符列，最大长度限制为2GB，用于不需要作字符串搜索的长串数据。此类型是一个遗留下来的而且将来不会被支持的数据类型，逐渐被BLOB，CLOB，NCLOB等大的数据类型所取代。

#### numberic数据类型

numberic数据类型用来存储负的和正的整数，分数和浮点型数据，在Oracle中提供的numberic数据类型：

number(<m>,<n>:可变长的数值列，允许0、正值及负值，m是所有的有效数字的位数，n是小数点以后的位数。

#### date数据类型

date:缺省格式是dd-mon-yy(日-月-年)

#### lob数据类型

blob、clob、nclob：三种大型对象(lob)，用来保存较大的图形文件或带格式的文本文件，如word文档，以及音频、视频等非文本文件，最大长充是4GB。
bfile:在数据库外部保存的大型二进制对象文件，最大长度是4GB，这种外部的LOB类型，通过数据库记录变化情况，但是数据的具体保存是在数据库外部进行的。

#### raw数据类型

raw(<size>:可变长二进制数据，具体定义字段时必须指明最大长度，这种格式用来保存较小的图形文件或带格式的文本文件，它也是一种较老的数据类型，将被lob数据类型所取代。
long raw:可变长二进制数据，最大长度是2GB，可以用来保存较大的图形或带格式的文本文件，以及音频、视频等非文本文件，这也是一种较老的数据类型，将被lob数据类型所取代。

#### 其它的Oracle数据类型

rowid:这是Oracle数据表中的一个伪例，它是数据表中每行数据内在的唯一标识
integer:整数类型

### 变量

#### 变量声明

语法

	variable_name [CONSTANT] datatype [NOT NULL] [:= | DEFAULT initial_value]


示例

	sales number(10, 2);
	pi CONSTANT double precision := 3.1415;
	name varchar2(25);
	address varchar2(100);


#### 变量初始化

声明一个变量的时候，变量的默认值是NULL。 如果想要在声明变量的同时设置初始值，有两种方式： 1. **DEFAULT** 关键字 2. 赋值 := 操作符

	greetings varchar2(20) DEFAULT 'Have a Good Day';
	counter binary_integer := 0;


#### 变量作用域

PL/SQL 允许程序块嵌套， 父程序块无法访问子程序块的变量，但是子程序块可以访问所有父程序快的变量。这一点和Javascript的变量作用域有点类似。

	DECLARE
	   -- Global variables 
	   num1 number := 95; 
	   num2 number := 85; 
	BEGIN 
	   dbms_output.put_line('Outer Variable num1: ' || num1);
	   dbms_output.put_line('Outer Variable num2: ' || num2);
	   DECLARE 
	      -- Local variables
	      num1 number := 195; 
	      num2 number := 185; 
	   BEGIN 
	      dbms_output.put_line('Inner Variable num1: ' || num1);
	      dbms_output.put_line('Inner Variable num2: ' || num2);
	   END; 
	END;


#### 常量

使用CONSTANT关键字声明常量。常量要求有一个默认的值，并且无法修改。

	DECLARE
	   -- constant declaration
	   pi constant number := 3.141592654;
	   -- other declarations
	   radius number(5,2); 
	   dia number(5,2); 
	   circumference number(7, 2);
	   area number (10, 2);
	BEGIN 
	   -- processing
	   radius := 9.5; 
	   dia := radius * 2; 
	   circumference := 2.0 * pi * radius;
	   area := pi * radius * radius;
	   -- output
	   dbms_output.put_line('Radius: ' || radius);
	   dbms_output.put_line('Diameter: ' || dia);
	   dbms_output.put_line('Circumference: ' || circumference);
	   dbms_output.put_line('Area: ' || area);
	END;


### 操作符

#### 比较操作符

| Operator | Example                                                                                                                  |
|:---------|:-------------------------------------------------------------------------------------------------------------------------|
| LIKE     | If 'Zara Ali' like 'Z% A_i' returns a Boolean true, whereas, 'Nuha Ali' like 'Z% A_i' returns a Boolean false.           |
| BETWEEN  | If x = 10 then, x between 5 and 20 returns true, x between 5 and 10 returns true, but x between 11 and 20 returns false. |
| IN       | If x = 'm' then, x in ('a', 'b', 'c') returns boolean false but x in ('m', 'n', 'o') returns Boolean true.               |
| IS NULL  | If x = 'm', then 'x is null' returns Boolean false.                                                                      |


#### 逻辑操作符

| Operator | Example                |
|:---------|:-----------------------|
| AND      | (A and B) is false.    |
| OR       | (A or B) is true.      |
| NOT      | not (A and B) is true. |


### 条件判断

	-- IF-THEN-ELSE
	
	DECLARE
	   a number(3) := 100;
	BEGIN
	   IF ( a = 10 ) THEN
	      dbms_output.put_line('Value of a is 10' );
	   ELSIF ( a = 20 ) THEN
	      dbms_output.put_line('Value of a is 20' );
	   ELSIF ( a = 30 ) THEN
	      dbms_output.put_line('Value of a is 30' );
	   ELSE
	       dbms_output.put_line('None of the values is matching');
	   END IF;
	   dbms_output.put_line('Exact value of a is: '|| a ); 
	END;
	
	-- CASE
	
	DECLARE
	   grade char(1) := 'A';
	BEGIN
	   CASE grade
	      when 'A' then dbms_output.put_line('Excellent');
	      when 'B' then dbms_output.put_line('Very good');
	      when 'C' then dbms_output.put_line('Well done');
	      when 'D' then dbms_output.put_line('You passed');
	      when 'F' then dbms_output.put_line('Better try again');
	      else dbms_output.put_line('No such grade');
	   END CASE;
	END;
	
	-- CASE-WHEN
	
	DECLARE
	   grade char(1) := 'B';
	BEGIN
	   case 
	      when grade = 'A' then dbms_output.put_line('Excellent');
	      when grade = 'B' then dbms_output.put_line('Very good');
	      when grade = 'C' then dbms_output.put_line('Well done');
	      when grade = 'D' then dbms_output.put_line('You passed');
	      when grade = 'F' then dbms_output.put_line('Better try again');
	      else dbms_output.put_line('No such grade');
	   end case;
	END;


### 循环

#### FOR循环

	-- 语法
	
	FOR counter IN initial_value .. final_value LOOP
	   sequence_of_statements;
	END LOOP;
	
	-- 默认情况下，FOR循环会从第一个值向最后一个值开始遍历
	
	DECLARE
	   a number(2);
	BEGIN
	   FOR a in 10 .. 20 LOOP
	       dbms_output.put_line('value of a: ' || a);
	  END LOOP;
	END;
	
	-- 通过使用REVERSE关键字，FOR循环可以冲最后一个值向第一个值遍历
	
	DECLARE
	   a number(2) ;
	BEGIN
	   FOR a IN REVERSE 10 .. 20 LOOP
	      dbms_output.put_line('value of a: ' || a);
	   END LOOP;
	END;


#### WHILE循环

	-- 语法
	
	WHILE condition LOOP
	   sequence_of_statements
	END LOOP;
	
	-- 示例
	
	DECLARE
	   a number(2) := 10;
	BEGIN
	   WHILE a < 20 LOOP
	      dbms_output.put_line('value of a: ' || a);
	      a := a + 1;
	   END LOOP;
	END;


#### 循环控制


* EXIT子句
* CONTINUE子句


	-- EXIT
	
	DECLARE
	   a number(2) := 10;
	BEGIN
	   -- while loop execution 
	   WHILE a < 20 LOOP
	      dbms_output.put_line ('value of a: ' || a);
	      a := a + 1;
	      IF a > 15 THEN
	         -- terminate the loop using the exit statement
	         EXIT;
	      END IF;
	   END LOOP;
	END;
	
	-- CONTINUE
	
	
	DECLARE
	   a number(2) := 10;
	BEGIN
	   -- while loop execution 
	   WHILE a < 20 LOOP
	      dbms_output.put_line ('value of a: ' || a);
	      a := a + 1;
	      IF a = 15 THEN
	         -- skip the loop using the CONTINUE statement
	         a := a + 1;
	         CONTINUE;
	      END IF;
	   END LOOP;
	END;


### 字符串

PL/SQL 使用 || 操作符拼接字符串。

### 数组

PL/SQL提供了VARRAY数组类型。 在ORACLE数据库中，VARRAY数组的下标从1开始。

	-- 语法
	
	TYPE varray_type_name IS VARRAY(n) of <element_type>
	
	TYPE namearray IS VARRAY(5) OF VARCHAR2(10);
	Type grades IS VARRAY(5) OF INTEGER;


### 记录

PL/SQL Record 类似于数据库表的一条记录，也可以按照C语言的结构体来理解，用来封装不同的数据类型。PL/SQL 接受三种不同的record


* Table-based
* Cursor-based records
* User-defined records


	DECLARE
	   type books is record
	      (title varchar(50),
	       author varchar(50),
	       subject varchar(100),
	       book_id number);
	   book1 books;
	BEGIN
	   -- Book 1 specification
	   book1.title  := 'C Programming';
	   book1.author := 'Nuha Ali '; 
	   book1.subject := 'C Programming Tutorial';
	   book1.book_id := 6495407;
	
	   -- Print book 1 record
	   dbms_output.put_line('Book 1 title : '|| book1.title);
	   dbms_output.put_line('Book 1 author : '|| book1.author);
	   dbms_output.put_line('Book 1 subject : '|| book1.subject);
	   dbms_output.put_line('Book 1 book_id : ' || book1.book_id);
	END;


### Subprogram

PL/SQL 提供两种子程序：


* Functions: 有返回值
* Procedures: 没有返回值


	-- Procedure
	
	CREATE OR REPLACE PROCEDURE greetings
	AS
	BEGIN
	   dbms_output.put_line('Hello World!');
	END;
	
	-- Procedure
	
	DECLARE
	   a number;
	PROCEDURE squareNum(x IN OUT number) IS
	BEGIN
	  x := x * x;
	END; 
	BEGIN
	   a:= 23;
	   squareNum(a);
	   dbms_output.put_line(' Square of (23): ' || a);
	END;
	
	-- Function
	
	CREATE [OR REPLACE] FUNCTION function_name
	[(parameter_name [IN | OUT | IN OUT] type [, ...])]
	RETURN return_datatype
	{IS | AS}
	BEGIN
	   < function_body >
	END [function_name];
	
	-- Function
	
	DECLARE
	   a number;
	   b number;
	   c number;
	FUNCTION findMax(x IN number, y IN number) 
	RETURN number
	IS
	    z number;
	BEGIN
	   IF x > y THEN
	      z:= x;
	   ELSE
	      Z:= y;
	   END IF;
	
	   RETURN z;
	END; 
	BEGIN
	   a:= 23;
	   b:= 45;
	
	   c := findMax(a, b);
	   dbms_output.put_line(' Maximum of (23,45): ' || c);
	END;
