

## 概念
```
DB:DataBase（存储数据的仓库）
DBMS：DataBase Management System  常见的：mysql，oracle，db2等
	DBMS分两类：
		1、基于共享文件系统的DBMS（Access）
		2、基于C/S架构的--需要安装客户端和服务端（Mysql，oracle，sqlserver）
sql：structure query language
```


## sql语句

### 基本操作

```sql
-- 查看当前所有数据库
show databases;

-- 打开 mysql 个库
use mysql;

-- 查看某个数据库里的表
show tables;
show tables from mysql;

-- 查看现在在哪个数据库
select database();
	
-- 查看表的结构
desc 表名;

-- 查看当前数据库的版本
select version(); 
```

### 注释
```mysql
# 单行注释

-- 单行注释

/*
多行注释
*/
```



## DQL（Data Query Language）

### 基础查询
语法
```mysql
select 查询列表 from 表名;
	-- 1、查询列表可以是：表中字段、常量值、表达式、函数
	-- 2、查询的结果是一个虚拟表格
	
-- 查询表中单个字段
	select 字段名 from 表名;
-- 查询表中多个字段
	select 字段名1，字段名2，字段名3 from 表名;
-- 查询表中所有字段
	select * from 表名;
-- 查询常量值(字符型和日期型的常量值必须用单引号扩起来，数值型常量不需要)
	select 100;
	select 'John';
-- 查询表达式
	select 100*98;
-- 查询函数
	select 函数名(实参列表);
-- 起别名
		-- 方式1（使用AS）:给100*98的结果起别名为‘结果’
		select 100*98 AS 结果;	
		-- 方式2（使用空格）
		select 100*98 结果;	
		-- 案例：查询salary，显示结果为out put
				select salary AS "out put"; 		 -- 若别名中有特殊符号，需要对别名加上""
-- 去重（distinct）
	select distinct 字段名 from 表名;
-- +号的作用（mysql中+号只有充当运算符功能）
		-- 1）两个操作数都是数值型 （select 100+98）
		-- 2）其中一个为字符型，试图将字符型转换成数值型;
			 	-- 若转换成功，则继续做加法运算	
			 			select ‘123’+90;
				--  若转换失败，则将字符型转换成0 
						select ‘john’+90;
				--  若其中一方为null，结果肯定为null 
						select null +30;
		-- 3)将字段进行拼接的函数-concat（字段1,字段2,..字段n）
				-- 案例：查询员工名和姓连接成一个字段，并显示为姓名
						select concat(last_name,first_name) AS 姓名 from employees;
				-- 案例：显示出表employee的全部列，各个列之间用逗号连接，列头显示成out_put
						select ifnull (commission_pct,0) AS 奖金率 from employees;
						select concat(first_name,last_name,job_id,ifnull (commission_pct,0)) AS out_put from employees;
        -- null和任何字段拼接都是null(commission_pct字段值为null)
        -- ifnull(字段名，返回值):判断字段/表达式是否为空，若为空，可以按返回值返回
			
-- 注：
		-- 1、区分字段和关键字，可以对字段加上着重号`		`column`
	 	-- 2、sql不区分字符和字符串，都用''
```

### 条件查询
语法
```sql
select 查询列表 from 表名 where 筛选条件；
-- 筛选条件：
	-- 1、条件表达式	 > < = != <> >= <=
	-- 2、逻辑表达式  $$ || ! and or not
	-- 3、模糊查询		like 	between and 	in	is null
		--like特点
			-- 一般和通配符搭配使用
		-- 通配符
			-- % 任意多个字符，包含0个字符
			-- - 任意单个字符
		-- between and
			-- 包含临界值
			-- 临界值不能颠倒（>=左边 <=右边）
		-- in 
			-- 用于判断某字段的值是否属于in列表中的某一项
			-- in列表的值类型必须一致或者兼容
			-- in列表里面不支持通配符
		-- is null / is not null
			-- = < > 不能用于判断null值
			-- is null /is not null 可以判断null值
		-- 安全等于 <=>
			-- 可以判断null值
			-- 可以判断普通数值
			-- 可读性差
		
```

实例
```mysql
-- 查询员工工资大于12000
	select * from employees where salary >12000;
-- 查询部门编号不等于90号的员工名和部门编号
	select last_name,dep_id from employees where dep_id！=90;
-- 查询工资在10000-20000的员工名
	select last_name from employees where salary>=10000 and salary <=20000;
-- 查询部门编号不是在90到110之间，或者工资高于15000之间的员工信息
	select * from employees where dep_id<90 or dep_id>110 or salary > 15000;
	select * from employees where NOT (dep_id>=90 and dep_id<=110)÷ or salary > 15000;
-- 查询员工名工名中包含字符a的员工信息(模糊查询)
	select * from employees where last_name like '%a%';
-- 查询员工第三个字符为e，第五个字符为a的员工名和工资
	select last_name,salary from employees where last_name like '__e__a%';
-- 查询员工名中第二个字符为_的员工名
	select last_name from employees where last_name like '-\-%';-- %为转义字符
	select last_name from employees where last_name like '-$-%' escape '$'; -- 指定$为转义字符
-- 查询员工编号在100到120之间的员工信息
	select * from employees where employee_id between 100 and 120;-- 100<=employee_id<=120
-- 查询员工的工种编号是是IT_PROG,AD_VP的中的一个员工名和工种编号
	select last_name,job_id from employees where job_id in ('IT_PROG','AD_VP');
-- 查询没有奖金的员工名和奖金率
	select last_name,commission_pct from employees where commission_pct is null;
	select last_name,commission_pct from employees where commission_pct <=> null;
-- 查询有奖金的员工名和奖金率
	select last_name,commission_pct from employees where commission_pct is not null;
-- 查询员工工号为176的员工姓名和年薪
	select last_name,salary*12*(1 + ifnull(commission_pct,0)) AS 年薪 from employees where emplyoyee_id=176;
-- 查询没有奖金，且工资不小于18000的salary和last_name
	select last_name,salary where commission_pct is null and salary >=18000;
-- 查询employee 表中，job_id不为‘IT’，或者工资为12000的员工信息
	select * from employees where job_id <> 'IT'  || salary =12000;
-- select * from employees; 与 select * from employees where commission_pct like '%%' and employee_id like '%%';不同
	-- select * from employees;  select * from employees where commission_pct like '%%' || employee_id like '%%';				查出所有，包含commission_pct 为null的值
	-- select * from employees where commission_pct like '%%' and employee_id like '%%';查出所有commission_pct 不为null的值

```

### 排序查询
语法
```mysql
-- 升序｜降序
select 查询列表 from 表 where 筛选条件 order by 排序列表 asc｜desc
	-- 不写asc|desc,默认升序
	-- order by后可以支持单个字段，多个字段，表达式，函数，别名
	-- order by 一般放在查询语句的最后面，limit子句除外 
```
案例
```mysql
-- 查询员工信息，要求工资从高到低排序
select * from employees order by salary desc;
-- 查询部门编号>=90的员工信息，按入职时间的先后顺序排序
select * from t where dep_id >=90 order by hiredata asc;
-- 按年薪高低显示员工信息和年薪【表达式排序】
select *，salary*12*(1+ifnull(commission_pct,0)) 年薪 from t order by 年薪 asc;
select *，salary*12*(1+ifnull(commission_pct,0)) 年薪 from t order by salary*12*(1+ifnull(commission_pct,0))  asc;
-- 按姓名的长度显示员工的姓名和工资
select length('last_name') 字节长度,last_name ,salary from t oeder by 字节长度 desc;
-- 查询员工信息，要求先按工资升序，再按员工编号降序【多个字段排序】
select * from t order by salary asc,employee_id desc;
-- 查询邮箱中包含e的员工信息，并按邮箱的字节数降序，再按部门号升序
select *,length(email) from t where email like '%e%' order by length(email) desc,dep_id asc;
```
### 常见函数
语法
```mysql
selec 函数名(实参列表) from 表名;
-- 分类
	-- 单行函数 concat(),length(),ifnull()		有一个返回值
	-- 分组函数/统计函数/聚合函数/组函数		做统计使用
```
#### 字符函数
```mysql
	-- length()	获取字节个数
	select length('john');  		-- 4个字节
	select length('张三丰哈哈哈');	-- 取决于字符集，utf-8 一个汉字占两个字节，一个字母占一个字节
	-- concat()	拼接字符串
  select 	concat(last_name,'-',fist_name) from t;
	-- upper(),lower()	大写，小写
	select upper('john');	-- JOHN
	-- substr(str,pos)	
		-- 两个参数，截取从指定索引后所有字符
		-- 截取从指定索引处指定字符的长度
		select substr('小明和小红是好朋友',6) output;  -- 是好朋友
		select substr('小明和小红是好朋友',1,2) output; -- 小明
	-- instr() 返回子串第一次出现的索引，如果找不到，返回0
	select instr('小明和小红是好朋友','是好朋友') ;	-- 6
	-- trim()	去除某个字符
		-- 只有一个参数的话，默认去除空格
	select trim('a' from 'aaaa张aaaaaa翠山aaaaaaaa') as output;	-- 去除a字符
	-- lpad() rpad()  用指定的字符实现左/右填充指定长度，
	select lpad('小红',5,'*') as output;		-- ***小红
	select lpad('小红',1,'*') as output;		-- 小
	-- replace()	替换
	select replace('周芷若周芷若张无忌爱上周芷若','周芷若','赵敏') as output;	-- 张无忌爱上赵敏
```
#### 数学函数
```mysql
-- round()	四舍五入
select round(1.65);		-- 2
select round(1.567,2);	-- 1.57	小数点后保留两位
-- ceil()		向上取整,只支持一个参数，返回大于等于该参数的最小整数（向右取整）
select ceil(1.52)	;	-- 2
-- floor()	向左取整
-- truncate()		截断
select truncate(1.69999,1);		-- 1.6	小数点后保存1位
-- mod()	取余
-- rand()	获取随机数，返回的是0-1之间的小数
```
#### 日期函数
```mysql
-- now()	返回当前日期和时间
select now();
-- curdata()	返回当前系统日期，不包含时间
-- curtime()	返回当前时间，不包含日期
-- str_to_date()	将日期格式的字符转换成指定格式的日期
select str_to_date('9-13-1999','%m-%d-%y');	-- 1993-09-13
-- date_format()	将日期转换成字符
select date_format('2018/6/6','%y年%m月%d日');	-- 2018年6月6日
-- datediff(expr1,expr2)	求两个日期时间相差的天数
select datediff('2017-10-1','2017-9-29');	-- 2天
select datediff(now(),1994-05-09);
```

#### 其他函数
```mysql
-- 查询版本号
select version();
-- 查询当前数据库
select database();
-- 查询当前用户
select user();
-- 返回该字符的加密形式
select password('aaaa');
select md5('aaaaa');

```
#### 流程控制函数
```mysql
-- if(expr1,expr2,expr3):expr1为true将返回expr2的值，反之返回expr3
select if(10>5,small,big);
-- case()
		-- 用法1：类似于switch case 的用法
					/* case 要判断的字段或表达式
						 when	常量1	then	要显示的值1或者语句1;
						 when	常量2	then	要显示的值2或者语句2;
						 ....
						 else 要显示的值n或者语句n;
						 end
					*/  -- then 后如果是表达式，不用加分号
		-- 用法2
					/* case 
						 when 条件1 then	要显示的值1或者语句1;
						 when 条件2 then	要显示的值2或者语句2;
						 ....
						 else 要显示的值n或者语句n;
						 end
					*/
	
			

```

案例
```mysql
-- 将姓大写，名小写，然后拼接
		select 	concat((upper(last_name),'-',lower(fist_name))from t;
-- 姓名中首字符大写，其他字符小写，再用下划线拼接
   select	concat(upper(substr(last_name,1,1)),'_' substr(last_name,2)) as output from t;
-- 将员工的姓名按首字母排序，并写出姓名的长度
   select length(last_name) 姓名长度 ,substr(last_name,1,1) 首字母,last_name from t order by 首字母 asc;              
-- 获取指定的部分，年，月，日，小时，分钟，秒
   select year(now()) 年;
   select month_name(now()) 月;
-- 查询入职日期为1992-04-03的员工信息
   select * from t where hiredate=‘1992-04-03’;
   select * from t  where hiredate=str_to_date('4-3 1992','%m-%d %y');     
-- 查询有奖金的员工名和入职日期（xx月/xx日 xx年） 
   select  last_name,date_formate(hiredate,'%m月/%d日 %y年') 入职日期 from t where commission_pct is not null;
-- 查看员工是否有奖金
   select last_name,commission_pct,if(commission_pct is null,'no money','yes') from t;     
-- 查询员工的工资，要求：部门号=30，显示的工资为1.1倍；部门号=40，显示的工资为1.2倍；部门号为50，显示的工资为1.3倍
   select salary 原始工资,dep_id,
   case dep_id
   when 30 then salary*1.1 
   when 40 then salary*1.2
   when 50 then salary*1.3
   else salary    
   end as 新工资  from t;
-- 查询员工的工资情况，如果工资大于>20000,显示A级别;如果工资大于>15000,显示B级别;如果工资大于>10000,显示C级别;否则显示D级别
   select salary
   case 
	 when salary>20000 then	'A';
	 when salary>15000 && salary<=20000 then	'B';
	 when salary>10000 && salary<=15000 then	'C';			
	 else 'D';
	 end  as  工资级别 from t;        
-- 做一个查询，产生下面的结果:<last_name> earns <salary> monthly but wants <salary*3>
   select concat(last_name,'earns',salary,' monthly but wants' salary *3) as 'dream salary' from t;           

```

### 分组函数
语法
```mysql
-- sum():求和	一个参数
select sum(salary) from t;
-- avg():求平均数	一个参数
select avg(salary) from t;
-- min():求最小值	一个参数
select min(salary) from t;
-- max():求最大值	一个参数
select max(salary) from t;
-- count():计数		一个参数
select count(salary) from t;	-- salary这个字段非空的值有多少个
select sum(salary) 和,avg(salary)	平均,min(salary)	最小,max(salary)	最大,count(salary)	计数 from t;
select count(*) from t;	-- 统计非空行数
select count(1) from t;	-- 统计非空行数（加了一列，该列中每行为1，统计1的个数）
-- 效率
	-- MYISAM存储引擎下，count(*)效率最高
	-- INNODB存储引擎下，count(*)和count(1)效率差不多，但比count(字段)效率高
-- 和分组函数一同查询的字段有限制（和分组函数一同查询的字段要求是group by后的字段）
select avg(salary),dep_id from t;	-- 没有意义

-- 注：
	-- sum(),avg()	仅支持数值型	忽略null值		可以和distnct搭配使用
	-- max(),min()	支持任何类型	忽略null值		可以和distnct搭配使用
	-- count()			支持任何类型	仅对非空数值计数	可以和distnct搭配使用
```
案例
```mysql
-- 查询员工表中的最大入职时间和最小入职时间的相差天数
select datediff(max(hiredate) min(hiredate))	相差天数	from t;
-- 查询部门编号为90的员工个数
select count(*) from t where t.dep_id=90;
```

### 分组查询
语法
```mysql
select 分组函数，列（要求出现在group by后面） from 表 where 筛选条件 group by 分组列表 order by 子句;
-- 分组前筛选	查询列表比较特殊，要求是分组函数和group by后面的字段
select 分组函数，列（要求出现在group by后面） from 表 where 筛选条件 group by 分组列表 order by 子句 having 筛选条件;
-- 分组后筛选
-- 分组函数做条件肯定放在having子句中
-- 能用分组前筛选，优先使用分组前筛选
-- group by子句支持单个字段分组，也支持多个字段分组（字段间没有顺序要求）
```
案例
```mysql
-- 查询每个工种的最高工资
select max(salary),job_id from t group by job_id;
-- 查询每个位置上的部门个数
select count(*),,loc_id from t group by loc_id;
-- 查询邮箱中包含a字符的，每个部门的平均工资
select avg(salary),dep_id from t where email like '%a%' group by dep_id;
-- 查询有奖金的每个领导手下员工的最高工资
select max(salary), manage_id from t where com_pct is not null  group by manage_id;
-- 查询哪个部门的员工个数大于2
	-- 查询每个部门的员工数
	select count(*),dep_id from t group by dep_id; 
	-- 在上述的结果上进行筛选，查询哪个部门的员工数>2
	select count(*),dep_id from t group by dep_id having count(*)>2;
-- 查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
 	-- 查询每个工种有奖金的员工的最高工资
 	select max(salary),job_id from t where com_pct is not null group by job_id;
 	-- 在上述结果上进行筛选，查询最高工资>12000的工种编号和最高工资
 	select max(salary),job_id from t where com_pct is not null group by job_id having max(salary)>12000;
-- 查询领导编号>102的每个领导手下的最低工资>5000的领导编号是哪个，以及最低工资
select min(salary),manage_id from t where manage_id>102 group by manage_id having min(salary)>5000;
-- 按员工姓名的长度分组，查询每一组的员工个数，筛选员工个数>5的有哪些
select length(last_name),count(*) from t group by length(last_name) having count(*)>5;
-- 查询每个部门的每个工种的平均工资
select avg(salary) ,dep_id,job_id from t group by dep_id,job_id;	
-- 查询每个部门的每个工种的平均工资，并按平均工资降序
select avg(salary) ,dep_id,job_id from t group by dep_id,job_id order by avg(salary) desc;	
-- 查询员工最高工资和最低工资的差距
select max(salary)-min(salary) difference from t;
-- 查询各个管理者手下员工的最低工资，其中最低工资不能低于6000，没有管理者的员工不在计算内
select min(salary),manage_id from t where manage_id is not null group by manage_id having min(salary)>=6000;
```

### 连接查询
#### 内连接（写法1）
语法
```mysql
-- 多表连接，当查询字段来自多个表时，就会用到连接查询
select	name,boyname from boys t1,beauty t2 where t1.id=t2.boyname;
-- 等值连接
select	name,boyname from boys t1,beauty t2 where t1.id=t2.boyname; 
	-- t1.id=t2.boyname其实是不同表中的相同的值
	-- 多表等值连接的结果为多表的交集部分
	-- n表连接，至少需要n-1个连接条件
	-- 多表顺序无要求；需要起别名
-- 非等值连接
	-- 查询员工的工资和工资级别
	select salary,grade_lev from employees e,job_grades j where salary between lowest_sal and highest_sal;
-- 自连接
	-- 查询员工名和其上级名(employees看成是两张表，两张表进行连接)
	select e.last_name,empolyee_id,l.lastname,manage_id from employees e,employees l where e.employee_id=l.manage_id;
```
案例
```mysql
-- 查询工种号，员工名，工种名
select last_name,t1.job_id,job_title from jobs t1,employees t2 where t1.job_id=t2.job_id;
-- 查询员工名和对应的部门名	(给表起别名后,只能用别名)
select  last_name,dep_name from employees t1,departments t2 where t1.dep_id=t2.dep_id;
-- 查询有奖金的员工名和部门名
select last_name,dep_name from employees t1,departments t2 where t1.dep_id=t2.dep_id and t1.com_pct is not null;
-- 查询城市中第二个字符为O的部门名和城市名
select dep_name，city from departments t1,locations t2 where t1.loc_id=t2.loc_id and city like'_O%';
-- 查询每个城市的部门个数
select count(*),city from departments t1,locations t2	 where t1.loc_id=t2.loc_id  group by city;
-- 查询出有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
select  dep_name,manage_id,min(salary) from employees t1,department t2 where t1.dep_id=t2.dep_id and t1.com_pct is not null group by dep_name,t2.manage_id;
-- 查询每个国家下的部门个数大于2的国家编号
select country_id ,count(*) 部门个数 from locations l,departments d where l.loc_id=d.loc_id group by country_id having count(*)>2;
```
#### 内连接（写法2）
```mysql
select 查询列表 from 表1 连接类型 join 表2 on 连接条件 where 筛选条件 group by 分组 having 筛选条件 order by;
-- 连接类型	inner
```
案例
```mysql
-- 查询员工名、部门名
select last_name,dep_name from employees e inner join department d on e.dep_id=d.dep_id;
-- 查询名字中包含e的员工名和工种名
select last_name,job_title from employees e inner join jobs j on e.job_id=j.job_id where e.last_name like '%e%';
-- 查询部门个数>3的城市名和部门个数
select city,count(*) 部门个数from departmnt d inner join location j on d.loc_id=l.loc_id group by city having count(*) >3;
-- 查询哪个部门的部门员工个数>3的部门名和员工个数，并按员工个数降序
select dep_name,count(*) from department d inner join employees e on e.dep_id=d.dep_id group by dep_name having count(*) >3 order by count(*) desc;
-- 查询员工名、部门名、工种名，并按部门名降序（多表连接）
select last_name,dep_name,job_title
from employees e 
inner join department d on e.dep_id=d.dep_id
inner join jobs j on e.job.id=j.job_id 
order by dep_name desc;
```

#### 外连接
语法
```mysql
select 查询列表 from 表1 连接类型 join 表2 on 连接条件 where 筛选条件 group by 分组 having 筛选条件 order by;
-- 左外连接	left [outer]
-- 右外连接	right [outer]
-- 全外连接	full outer
-- 交叉连接	cross
```
案例
```mysql

```


### 子查询
```mysql

```

### 分页查询
```mysql

```

### union联合查询
```mysql

```

## DML（Data Manipulation Language）

## DDL（Data Definition  Language）

## TCL（Transaction Control Language）























```

```
