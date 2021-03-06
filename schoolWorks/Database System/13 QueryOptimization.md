查询优化

<font face = "Consolas">
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [概述](#概述)
- [关系表达式的转换](#关系表达式的转换)
  - [等价规则(Equivalence Rules)](#等价规则equivalence-rules)
  - [等价表达式的枚举(Enumeration of Equivalent Expressions)](#等价表达式的枚举enumeration-of-equivalent-expressions)
- [表达式结果集统计大小的估计](#表达式结果集统计大小的估计)
- [执行计划选择(Choice of Evaluation Plans)](#执行计划选择choice-of-evaluation-plans)
  - [基于代价的连接顺序选择(Cost-Based Optimization)](#基于代价的连接顺序选择cost-based-optimization)
  - [采用等价规则的基于代价的优化器(Cost Based Optimization with Equivalence Rules)](#采用等价规则的基于代价的优化器cost-based-optimization-with-equivalence-rules)
  - [启发式优化(Heuristic Optimization)](#启发式优化heuristic-optimization)
- [其他查询优化(Additional Optimization Techniques)](#其他查询优化additional-optimization-techniques)
  - [top-K优化](#top-k优化)
  - [更新的优化(Optimization of Updates)](#更新的优化optimization-of-updates)
  - [多查询优化(Multiquery Optimization)](#多查询优化multiquery-optimization)
  - [参数化查询优化(Parametric Query Optimization)](#参数化查询优化parametric-query-optimization)

<!-- /code_chunk_output -->

# 概述
每个关系运算可以有几种不同的算法(等价表达式)
查询优化器需要找到代价最小的等价表达式
1. 产生等价表达式
2. 对所产生的表达式产生不同的查询计划
3. 估计每个执行计划的代价,选择最小的一个

# 关系表达式的转换
* 等价表达式(equivalent): 两个关系表达式在每一个有效数据库实例中都会产生相同的元组集
    > 有效的数据库实例: 满足在数据库模式中指定的所有完整性约束的数据库实例
    元组顺序无所谓
## 等价规则(Equivalence Rules)
1. $σ_{θ1∩θ2}(E)=σ_{θ1}(σ_{θ2}(E))$
   >σ的级联
2. $σ_{θ1}(σ_{θ2}(E))=σ_{θ2}(σ_{θ1}(E))$
   >选择运算满足交换律
3. $π_{L1}(π_{L2}(...(π_{Ln}(E)...))=π_{L1}(E) $
   >π的级联,投影运算只有最后一个必须
4. $σ_θ(E1*E2)=E1⋈_θE2 $
   $σ_{θ1}(E1⋈_{θ2}E2)=E1⋈_{θ1∩θ2}E2 $
   >选择操作可与笛卡尔积以及θ连接结合
5. $E1⋈_θE2=E2⋈_θE1 $
   >θ连接满足交换律(不考虑属性顺序)
6. $(E1⋈E2)⋈E3=E1⋈(E2⋈E3) $
    >⋈满足结合律(associative)

   $(E1⋈_{θ1}E2)⋈_{θ2∩θ3}E3=E1⋈_{θ1∩θ3}(E2⋈_{θ2}E3) $
   >θ连接满足如上结合律(θ2只涉及E2E3属性时)
7. $σ_{θ0}(E1⋈_θE2)=(σ_{θ0}(E1))⋈_θE2 $
   >选择运算的分配律(θ0只涉及表达式之一时)

   $σ_{θ1∩θ2}(E1⋈_θE2)=(σ_{θ1}(E1))⋈_θσ_{θ2}(E2) $
   >θ1只涉及E1,θ2只涉及E2时
8. $π_{L1∪L2}(E1⋈_θE2)=π_{L1}(E1)⋈_θ(π_{L2}(E2)) $
    > L1,L2分别为E1,E2的属性集,连接条件θ只涉及L1∪L2

    $π_{L1∪L2}(E1⋈_θE2)=π_{L1∪L2}((π_{L1∪L3}(E1))⋈_θ(π_{L2∪L4}(E2))) $
    > L1,L2分别为E1,E2的属性集,L3为E1出现在θ中但不在L1∪L2中的属性,L4为E2 出现在θ中但不在L1∪L2中的属性
9. $E1∩E2=E2∩E1 $
   $E1∪E2=E2∪E1 $
   > 集合交并满足交换律(差不满足)
10. $(E1∩E2)∩E3=E1∩(E2∩E3) $
   $(E1∪E2)∪E3=E1∪(E2∪E3) $
    > 集合的交并满足结合律
11. $σ_p(E1-E2)=σ_p(E1)-σ_p(E2) $
    >选择对并交差具有分配律
12. $π_L(E1∪E2)=π_L(E1)∪π_L(E2) $
    > 投影对并有分配律

## 等价表达式的枚举(Enumeration of Equivalent Expressions)
如果暴力循环穷举的话,代价很大,可以采取下列优化方法
1. 通过等价关系生成的子表达式生成的子树通常与父表达式的相同,可用指针共享
2. 优化器考虑

# 表达式结果集统计大小的估计
估计并不精确,但接近

# 执行计划选择(Choice of Evaluation Plans)
* 搜索所有计划
* 使用启发式选择计划
## 基于代价的连接顺序选择(Cost-Based Optimization)
$r1⋈r2⋈...⋈rn $
连接运算随意结合,等价表达式很多
可用动态规划找到最佳连接顺序
时间复杂度为$O(3^n)$
## 采用等价规则的基于代价的优化器(Cost Based Optimization with Equivalence Rules)
许多其他功能(聚集,外连接,嵌套查询)无法通过连接顺序选择接近
添加物理等价规则(Physical equivalence rules),允许将逻辑操作(连接等)转换成散列连接,嵌套循环连接这样的物理操作
通过添加这类规则,产生所有可能的执行计划
这依赖于:
1. 一种节省空间的表达式表示形式,避免创建多个副本
2. 检测相同表达式重复推导
3. 基于缓存(memoization)的动态规划形式
4. 维护动态规划,剪枝减少计算量
## 启发式优化(Heuristic Optimization)
优化本身也有代价,启发式方法可以用来减少优化代价
启发式优化器不验证采用规则转换后代价是否减少,而是直接加以使用
> 许多优化器只考虑左深连接顺序(left-deep join orders)(只考虑向左延申的树),适合流水线计算

# 其他查询优化(Additional Optimization Techniques)
## top-K优化
返回排序结果时,对于某特定K值只取结果前K个
```sql
select * 
from r, s
where r.B = s.B
order by r.A ascending
limit 10
```
## 更新的优化(Optimization of Updates)
* 万圣节问题: `update R set A = 5 * A where A > 10`
计算顺序问题
## 多查询优化(Multiquery Optimization)
```sql
Q1: select * from (r natural join t) natural join s
Q2: select * from (r natural join u) natural join s
```
查询优化器发现不同查询之间共同的子表达式
## 参数化查询优化(Parametric Query Optimization)
涉及常数的查询被优化器缓存时,被再次查询即使常数不同也会被重用
使用参数化查询优化,优化器提供不同参数值最优的几个计划,在常数查询来时直接使用已得到的优化计划.
