# Mysql

## 视图

> 创建视图

CREATE VIEW 视图名[字段 ..]  AS 查询sql语句

> 更新视图

CREATE OR REPLACE VIEW 视图名 [字段..] AS 查询的sql语句

或

ALTER VIEW 视图名 [字段...]  AS 查询的sql语句

> 删除视图

DROP VIEW 视图名

> 查看数据中哪些是表哪些是视图

show table status

==视图的优点==

- 查询性能提高
  - 相对于直接SQL省去了语法检查和解析阶段的开销
- 改动表结构 达到少修改的效果
  - 假如因为某种需要a表和b表需要进行合并起来组成一个新的表c最后a表和b表都不会存在了，而且由于原来程序中编写sql分别是基于a表和b表查询的。这就意味着需要重新编写大量的sql(改成向c表去操作数据) 而通过视图就可以做到步修改 定义两个视图名字还是原来的表名a和b。a  b视图完成从c表中取出内容。比如视图名a 那么 查询还是select * from a
- 复杂的查询需求
  - 可以进行问题分解 然后将创建多个视图获取数据 将视图联合起来就能得到需要的结果。






































