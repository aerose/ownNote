基本SQL
[TOC]
<font face = "Consolas">

# 基本类型
* char(n)
    固定长度字符串
    >填充空格
* varchar(n)
    可变长字符串,最大长度n
    > 不填充空格,所以和char比较时可能有问题,不建议用char
* int
    整形
* smallint
    小整数
* real,double precision
    浮点,双精度浮点
* float(n)
    精度至少为n的浮点
# 基本模式
## 关系定义
```
create table instructor (
ID char(5),
name varchar(20) not null,
dept_name varchar(20),
salary numeric(8,2),
primary key (ID),
foreign key (dept_name) references department);
```
* `create table`  创建关系
* `primary key` 声明主码(非空,唯一)
* `foreign key(A1,A2)references` 外码
    >声明属性取值对应于其他关系上某元组在主码上的取值(所有元组的该属性必须存在在外关系的主码中)
* `not null` 不允许空值
* `;` 结束
## 更新
### insert
添加
```
insert into instructor
values (‘10211’, ’Smith’, ’Biology’, 66000);
```
按顺序
### delete
删除所有元组
`delete from instructor;`
### drop table
去掉一个关系(包括元组)
`drop table r;`
### alter table
添加/去掉属性
`alter table r add A D;`
`alter table r drop A;`

# 查询
```
select A1,A2,...,An     /属性
from r1,r2,...,rm       /关系
where P                 /谓词
```
## select
* 关键词:
    * `distinct` 去重(一般允许重复)
        `select distinct dept_name`
        
    * `all` 显示不去重
        `select all dept_name`
    * `*` 指名全部属性
        ```
        select *
        from instructor
        ```
* 可以用直接值作为关系
    `select ‘437’`
    `select ‘437’ as FOO`
* 可以运算符修改属性(临时值,不改原关系)
    `select ID, name, salary/12`
## where
* 可用逻辑连词(and, or, not)
比较运算符< <= > >= <>(不等)
* `between and` <= >=
    `where salary between 90000 and 100000`
* `as` 重命名
    ```
    select distinct T.name
    from instructor as T, instructor as S
    where T.salary > S.salary and S.dept_name = ‘Comp. Sci.’
    ```
    可省略as `instructor as T ≡ instructor T`
* 字符串匹配
    `where name like '%dar%' `
    % :子串(可为空)
    _ :一个字符
    escape: 定义转义字符 `like ‘100 \%' escape
'\' `定义转义字符为\
## order by
排序,默认升序(desc降序,asc升序)
```
select distinct name
from instructor
order by name desc, salary asc;
```
## 集合运算
```
(select course_id from section where sem = ‘Fall’ and year = 2009)
union
(select course_id from section where sem = ‘Spring’ and year = 2010)
```
* union (or)
* intersect (and)
* except (not)
> 可加关键词如`union all`
## 空值
* 算术表达式任一输入为空: 结果为空
* 涉及空值的比较运算: unknown(为逻辑谓词)
    >  OR: 
    (unknown or true) = true,
    (unknown or false) = unknown
    (unknown or unknown) = unknown
    AND: 
    (true and unknown) = unknown,
    (false and unknown) = false,
    (unknown and unknown) = unknown
    NOT: 
    (not unknown) = unknown
* null测试空值 `where salary is null`
    > null = null 返回unknown
* 聚集函数: 除了count(*)其余聚集函数不算空值
    > 只有空值时count return 0,其余return null
## 聚集函数
>avg: 平均
min/max : 最小/最大
sum : 总和
count : 计数
`select avg (salary)`
`select count (distinct ID)`
`select count (*)`
## group by
根据属性构造分组, 聚集函数根据组来分别操作(每个组独立聚集)
```
select dept_name, avg (salary) as avg_salary
from instructor 
group by dept_name;
```
## having
在分组后起作用: 对每个组使用谓词/聚集函数
```
select dept_name, avg (salary)
from instructor
group by dept_name
having avg (salary) > 42000;    /得到平均工资超过42000的系
```
> having 在组形成之后应用,where 在组形成之前应用
## 嵌套子查询
### in/not in
测试元组是否是集合中的成员
```
select distinct course_id
from section
where semester = ’Fall’ and year= 2009 and
course_id in (select course_id
from section
where semester = ’Spring’ and year= 2010);  
/查询同时在2009年秋和2010年春开课的课程
```
```
select count (distinct ID)
from takes
where (course_id, sec_id, semester, year) in
(select course_id, sec_id, semester, year
from teaches
where teaches.ID= 10101);
/查询一组属性
```
```
select distinct name
from instructor
where name not in ('Mozart', 'Einstein');
/枚举集合
```
### some/all
给出集合比较,some存在成立的,all和所有成员成立
```
select name
from instructor
where salary > some (select salary
                     from instructor
                     where dept name = ’Biology’);
```
> 存在成员比较满足即可(>some即至少比一个人大)
所有用all
### exists/ not exists
检查是否存在元组,参数的子查询非空时return true
```
select course_id
from section as S
where semester = ’Fall’ and year = 2009 and
exists (select *
        from section as T
        where semester = ’Spring’ and year= 2010
        and S.course_id = T.course_id);
/查询同时在2009年秋和2010年春开课的课程
```
> 相关子查询: 外层查询的名称也可用在where的子查询中
### unique/ not unique
检查是否存在重复元组,参数的子查询没有重复元组时return true
```
select T.course_id
from course as T
where unique (select R.course_id
             from section as R
             where T.course_id= R.course_id
             and R.year = 2009);
```
### from中的子查询
```
select dept_name, avg_salary
from (select dept_name, avg (salary) as avg_salary
     from instructor
     group by dept_name)
where avg_salary > 42000;
```
也可用as对子查询结果起名
```
select dept_name, avg_salary
from (select dept_name, avg (salary)
from instructor
group by dept_name) as dept_avg (dept_name, avg_salary)
where avg_salary > 42000;
```
(课外)一般不允许使用其他关变量,子查询前加lateral允许访问外层查询的属性
`from lateral(`
### with
定义临时关系(只对包含with的查询有效)
```
with max_budget (value) as
    (select max(budget)
    from department)
select department.name
from department, max_budget
where department.budget = max_budget.value;
```
### 标量子查询
标量子查询(返回单个值的子查询)允许出现任何地方
```
select name,
        (select count(*)
        ...) as num_instructors
from department;
```
# 修改
## delete
删除整个元组
```
delete from instructor
where dept_name= ’Finance’;
```
`delete from instructor`缺省where就是整个关系中元组全删
## insert into
插入元组
* 按顺序
    ```
    insert into course
            values (’CS-437’, ’Database Systems’, ’Comp. Sci.’, 4);
    ```
* 也可指定属性
    ```
    insert into course (course_id, title, dept_name, credits)
            values (’CS-437’, ’Database Systems’, ’Comp. Sci.’, 4);
    ```
* 查询的基础上插入元组
    ```
    insert into student
        select ID, name, dept_name, 0
        from instructor
    ```
    > 未给出属性为null
* 必须执行完select再insert
    ```
    insert into table1 
        select * 
        from table1
    /会重复插入无数元组
    ```
## updates
更新属性
```
update instructor
    set salary = salary * 1.03
    where salary > 100000;
```
### case
条件更新
```
update instructor
set salary = case
            when salary <= 100000 then salary * 1.05
            else salary * 1.03
            end
```
```
where pred1 then res1
...
else res0
end     /case
```