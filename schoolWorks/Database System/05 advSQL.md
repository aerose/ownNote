高级SQL

<font face = "Consolas">
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [高级语言连接数据库](#高级语言连接数据库)
  - [ODBC](#odbc)
  - [嵌入式SQL(Embedded SQL)](#嵌入式sqlembedded-sql)
    - [连接](#连接)
    - [断开连接](#断开连接)
    - [游标cursor](#游标cursor)
    - [SQL通信区域(SQLCA)](#sql通信区域sqlca)
- [函数和过程](#函数和过程)
  - [函数(function)](#函数function)
  - [过程(procedure)](#过程procedure)
  - [循环loop](#循环loop)
  - [条件语句](#条件语句)
  - [异常条件(exception condition)/句柄(handler)](#异常条件exception-condition句柄handler)
  - [外部语言过程](#外部语言过程)
- [触发器(triggers)](#触发器triggers)
- [递归查询(Recursive Queries)](#递归查询recursive-queries)
- [高级聚集特性(Advanced Aggregation Features)](#高级聚集特性advanced-aggregation-features)
  - [排名(ranking)](#排名ranking)
  - [分窗(window)](#分窗window)

<!-- /code_chunk_output -->

# 高级语言连接数据库
## ODBC
## 嵌入式SQL(Embedded SQL)
嵌入SQL到其他语言中
`EXEC SQL <str>`
> 使用宿主变量时前加`:`
  数据库修改的语句可直接EXEC SQL <>;
### 连接
`EXEC SQL connect to server user user-name using password;`
### 断开连接
`EXEC SQL COMMIT WORK RELEASE`
`EXEC SQL DISCONNCET [connection]` 

### 游标cursor
定义一个查询,然后用open执行查询
```sql
EXEC SQL
    declare c cursor for
    ...;

EXEC SQL open c;

/执行操作
EXEC SQL UPDATE instructor
SET salary = salary + 1000
WHERE CURRENT OF c
```
执行open后,cursor指向结果的第一个元组,执行fetch后指向下一个元组,没有待处理元组时`sqlca.sqlstate='02000'`
### SQL通信区域(SQLCA)
`EXEC SQL INCLUDE sqlca`
放置错误信息
`sqlca.sqlcode>=0`表示没有错误

# 函数和过程
## 函数(function)
```sql
create function dept_count (dept_name varchar(20))
    returns integer
    begin
    declare d_count integer;
        select count (* ) into d_count
        from instructor
        where instructor.dept_name = dept_name
    return d_count;
end

where dept_count (dept_name ) > 12

```
## 过程(procedure)
```sql
delimiter //
create procedure dept_count_proc (in dept_name varchar(20), out d_count integer)
begin
    select count(*) into d_count
    from instructor
    where instructor.dept_name = dept_count_proc.dept_name
end//
delimiter ;

declare d_count integer;
call dept_count_proc( ‘Physics’, d_count);

```
> in/out表示待赋值的参数/返回结果
## 循环loop
```sql
/for
declare n integer default 0;
for r as
    select budget from department
do
    set n = n + r.budget
end for

/while
while <bool> do
    ...;
end while

/repeat
repeat
    ...;
until <bool>
end repeat
```
## 条件语句
```sql
/if
if <bool>
    then ...;
elseif <bool>
    then ...;
else ...;
end if

/case
CASE case_value
    WHEN when_value THEN statement_list
    [WHEN when_value THEN statement_list] ...
    [ELSE statement_list]
END CASE
```
## 异常条件(exception condition)/句柄(handler)
发信号通知异常条件,声明句柄处理异常条件
```sql
declare out_of_classroom_seats condition
declare exit handler for out_of_classroom_seats /exit表示条件发生终止语句,还有continue(下一条开始执行)等
begin
…statements
end

执行signal out_of_classroom_seats引发一个异常
```
## 外部语言过程
用程序设计语言定义函数,过程
```sql
create procedure dept_count_proc(in dept_name varchar(20),out count integer)
language C
external name '/usr/avi/bin/dept_count_proc'
create function dept_count(dept_name varchar(20))
returns integer
language C
external name '/usr/avi/bin/dept_count'
```
> 效率更高,但有安全性问题
# 触发器(triggers)
但对数据库作修改时自动被系统执行
> 可用来实现完整性约束
```sql
create trigger credits_earned after update of takes on (grade)
referencing new row as nrow     /建立过渡变量(transition variable存储修改行的值)
referencing old row as orow
for each row
when nrow.grade <> ’F’ and nrow.grade is not null
    and (orow.grade = ’F’ or orow.grade is null)
begin atomic
    update student
    set tot_cred= tot_cred + 
        (select credits
        from course
        where course.course_id= nrow.course_id)
    where student.id = nrow.id;
end;

alter trigger trigger_name enable/disable
drop trigger trigger_name
```
>old: delete,update
 new: insert,update
 不应使用触发器:
 备份数据时
 考虑触发器的相互调用

# 递归查询(Recursive Queries)
使用传递闭包找出所有层级中的相关单元
`with recursive`支持有限形式的递归
```sql
with recursive rec_prereq(course_id, prereq_id) as (
    select course_id, prereq_id
    from prereq
union
    select rec_prereq.course_id, prereq.prereq_id, 
    from rec_rereq, prereq
    where rec_prereq.prereq_id = prereq.course_id
)
select ∗
from rec_prereq;
```
> 首先进行非递归的基查询(prereq上),然后使用递归视图的递归查询
# 高级聚集特性(Advanced Aggregation Features)
## 排名(ranking)
```sql
select ID, rank() over (order by GPA desc) as s_rank
from student_grades
order by s_rank
```
用在查询中:
```sql
select ID, (1 + (select count(*)
    from student_grades B
    where B.GPA > A.GPA)) as s_rank
from student_grades A
order by s_rank;
```
> 多层order by会先按前项排序,然后在前项分类中排序`order by dept_name,dept_rank;`

其他排序函数
* `percent_rank (within partition, if partitioning is done)`
    以分数给出该元组名次(名次-1)/(总数-1)
* `cume_dist (cumulative distribution)`
    排序值<=该元组的元组数/总数
* `row_number (non-deterministic in presence of duplicates)`
    对行进行排序
## 分窗(window)
对聚集函数over将其改为开窗函数,可对该聚集函数增加修饰,不影响其余
```sql
select account_number, date_time,
    sum (value) over
        (partition by account_number
        order by date_time
        rows unbounded preceding)
    as balance
from transaction
order by account_number, date_time
```