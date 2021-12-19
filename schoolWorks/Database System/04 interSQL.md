中级SQL
[TOC]
<font face = "Consolas">

# 视图(Views)
>上文都是在逻辑模型层操作,但让所有用户看到整个逻辑模型是不合适的,有时需要隐藏.
* 视图: 作为虚关系对用户可见的关系(不是逻辑模型的一部分)
> 视图关系并不预计算和存储,被访问时查询创建(物化视图)
## `creat view as`
`create view v as <query expression>`
```
create view faculty as
    select ID, name, dept_name
    from instructor;
```
## 查询中使用视图
用视图名指代该虚关系
```
create view physics_fall_2009 as
    select course.course_id, sec_id, building, room_number
    from course, section
    where course.course_id = section.course_id
        and course.dept_name = ’Physics’
        and section.semester = ’Fall’
        and section.year = ’2009’;

create view physics_fall_2009_watson as
    select course_id, room_number
    from physics_fall_2009
    where building= ’Watson’;
```
然后就可随意使用该关系(甚至使用视图定义视图)
## 更新视图
一般来说不允许: 修改视图必须体现在真实的关系中,但属性的缺省会导致填充空值,且查询时不会得到期望结果
* 可更新的情况:
    * from只有一个关系
    * select只包含属性(不包含表达式,聚集,distinct)
    * 没出现在select的子句中属性可以取空值(没有not null,不为主码的一部分)
    * 查询不含group by, having
```
create view history_instructors as
select *
from instructor
where dept_name= ’History’;
```
> 然后就允许进行update,insert,delete
更新体现在真实关系中,如果不满足视图选择条件就不会出现再视图中

# 事务(transaction)
事务: 由查询,更新语句的序列组成(一条SQL语句被执行就隐式开始了一个事务)
* commit work:提交当前事务(保存)
* rollback work:回滚(撤销)
> 提供了错误处理方式
一个事务在没有完成所有步骤时崩溃,需在重启后回滚
因此完成所有步骤后提交,不能完成时回滚提供了对事务的原子性抽象

# 完整性约束(Integrity Constraints)
保证用户修改不破坏数据的一致性(即给出定义域和操作的限制)
在`creat table`时声明,也可通过`alter table name add constraint`添加
## `not null`
限制属性不为空值
`name varchar(20) not null`
##  `unique`
关系中所有元组在列出的属性上不能相同(null不等于其他任何值,可以用)
$unique(A_1,A_2,...,A_m)$
## `check`
指定一个谓词,关系中每个元组必须满足
```
create table section (
    course_id varchar (8),
    sec_id varchar (8),
    semester varchar (6),
    year numeric (4,0),
    building varchar (15),
    room_number varchar (7),
    time slot id varchar (4),
    primary key (course_id, sec_id, semester, year),
    check (semester in (’Fall’, ’Winter’, ’Spring’, ’Summer’)));
```
## 参照完整性(Referential Integrity)
一个关系中给定的属性集上的取值在另一关系的特定属性集的取值中出现
> 如instructor关系中一个元组的department属性为'Biology',则在department关系中存在'Biology'的元组
```
create table course (
course_id char(5) primary key,
title varchar(20),
dept_name varchar(20) references department
)
```
* `on delete cascade`: 删除department中元组破坏了参照完整性约束,不拒绝删除对course关系作级联删除(删除所有相关元组)
* `on update cascade`: 更新违反了也不拒绝而是将course中参照的元组相关属性改为新值
* `set null, set default`: 置null或域的默认值
```
create table course (
...
dept_name varchar(20),
foreign key (dept_name) references department
on delete cascade
on update cascade,
...
)
```
## 事务中对完整性约束的违反
可能会暂时违反,但后面会消除(如登记夫妻双方)
所以可将`initially deferred`加入约束声明,这样会在事务结束时检查完整性约束
## 复杂check与断言(assertion)
>!!未被大多数数据库系统支持
* check中子查询
    `check (time_slot_id in (select time_slot_id from time_slot))`

断言:一个谓词(域约束和参照完整性约束是断言的特殊形式)
`create assertion <assertion-name> check <predicate>;`

# 数据类型
## 日期,时间
* date
    >Example: date ‘2005-7-27’
* time
    >Example: time ‘09:00:30’ time ‘09:00:30.75’
* timestamp:日期时间
    >Example: timestamp ‘2005-7-27 09:00:30.75’
* interval: 一段时间
    >Example: interval ‘1’ day
    可通过其他类型相减得到,也可加到其他类型中
> 可用函数:`current_date`,`current_time`,`localtime`(不带时区),`current_timestamp`,`localtimestamp`
## 默认值`default`
```
creat table student(
tot_cred numeric (3,0) default 0
```
## 创建索引index
在属性上创建索引可快速查找
`create index studentID_index on student(ID);`
## 用户定义类型
`create type Dollars as numeric (12,2) final;`
就可使用该类型
```
create table department (
    dept_name varchar (20),
    building varchar (15),
    budget Dollars);
```
`drop type` `alter type` 删除/修改类型
* 类型转换:
`cast(department.budget to numeric(12,2))`
## 域domain
在自定义类型之前出现,在基本类型上加完整性约束
`create domain person_name char(20) not null`
> 类型与域区别:
>>域上可声明约束(not null,default等)
  域不是强类型的(允许基本类型相容的域相互赋值)

* 可check
```
create domain degree_level varchar(10)
    constraint degree_level_test    /命名约束,可省略
        check (value in (’Bachelors’, ’Masters’, ’Doctorate’));
```
## 大对象类型
* blob: 二进制
* clob: 字符型
## 授权
* 用户在数据库某些部分的权限:
    > Read Insert Update Delete 
    (all privileges所有权限)
* 在数据库模式上的权限:(修改关系)
    > Index(索引更改) Resources(关系创建) Alternation(属性修改) Drop(关系删除)
### 授予`grant`与收回`revoke`
```
grant <privilege list>(具体属性)
on <relation name or view name>
to <user list>
/授予grant,收回revoke on from
```
> 用户列表可为 用户名,public,role
* 外码权限授予
`grant reference (dept_name) on department to Mariano;`
## 角色role
* 角色为权限的集合
    `create role instructor;`
* 角色可以授予用户或其他角色
    `create role dean;`
    `grant instructor to dean;`
    `grant dean to Satoshi;`
## 视图的授权
## 权限相关子句
* `with grant option`:允许将权限传递
    `grant select on department to Amit with grant option;`
* `restrict`: 防止级联收回
    `revoke select on department from Amit, Satoshi restrict;`
* `cascade`: 需要级联收回(默认)

