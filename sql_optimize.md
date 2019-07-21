### sql优化
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/1.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/2.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/3.png)
```sql
# 配置MySQL慢查询日志
set global slow_query_log = [ON|OFF]
set global slow_query_log_file = /sql_log/slowlog.log #指定日志存储位置
set global long_query_time = xx.xxx秒 #请求等待超过多少秒就会被记录进日志
set global log_queries_not_using_indexes = [ON|OFF] #是、否使用索引会被记录进日志

# 查看列表定义 
    show create table imc_course;

# 分析MySQL慢查询日志
mysqldumpslow[OPTS...][LOGS...]
## 下载地址 https://www.percona.com/downloads/percona-toolkit/LATEST/
pt-query-digest[OPTIONS][FILES][DSN] 
# windows
- yum install -y perl-DBD-MySQL.x86_64 perl-Time-HiRes.x86_64 perl-IO-Socket-SSL.noarch 
perl-TermReadKey.x86_64 perl-Digest-MD5
- rpm -ivh percona-toolkit-3.0.13-1.el7.x86_64.rpm
- pt
```
### 分析sql执行计划
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/4.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/5.png)

```sql
EXPLAIN
select course_id,class_name,level_name,title,study_cnt from imc_course a
join imc_class b on b.class_id=a.class_id
join imc_level c on c.level_id=a.level_id
where study_cnt>3000

EXPLAIN
select a.course_id,a.title from imc_course a where a.course_id NOT IN (
    select course_id from imc_chapter b
)

```
#### select_type
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/6.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/7.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/8.png)

```sql
EXPLAIN
select course_id,class_name,level_name,title,study_cnt from imc_course a
join imc_class b on b.class_id=a.class_id
join imc_level c on c.level_id=a.level_id
where study_cnt>3000
union
select course_id,class_name,level_name,title,study_cnt from imc_course a
join imc_class b on b.class_id=a.class_id
join imc_level c on c.level_id=a.level_id
where class_name='MySQL'
```
#### partitions
- 对于分区表，显示查询的分区ID、对于非分区表，显示NULL
#### type
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/9.png)
#### possible_keys key key_len
- 指出查询中可能会用到的索引、指出查询实际用到的索引、实际使用索引的最大长度
#### ref rows filtered
- 指出那些列或常量被用于索引查找 根据统计信息预估的扫描的行数、表示返回结果的行数占需读取行数的百分比
#### EXtra
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/10.png)
### 索引优化
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/11.png)
```sql
#Innodb支持的索引类型
Btree索引
自适应索引
全文索引
空间索引
```
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/12.png)
```sql
# Btree索引适用于全值匹配的查询
class_name='mysql'
class_name in ('mysql','postgreSQL')
# Btree索引适合处理范围查找
study_cnt between 1000 and 3000
study_cnt > 3000
# Btree索引的特点
- Btree索引从索引的最左侧列开始匹配查找列
create index idx_title_studyCnt on imc_course(title,study_cnt)
study_cnt>3000 and title='Mysql' title='Mysql'

# 应该在什么列上建立索引
    ##where字句中的列
    ## 查询出2019年1月1号以后注册的男性会员昵称
    EXPLAIN
    select user_nick from imc_user
    where sex=1 and reg_time>'2019-01-01';

    # 说明注册时间筛选性远远大于性别的筛选性
    select count(distinct sex)
    ,count(distinct date_format(reg_time,'%Y-%m-%d'))
    ,count(*)
    ,count(distinct sex)/count(*)
    ,count(distinct date_format(reg_time,'%Y-%m-%d'))/count(*)
    from imc_user;
    # 所以
    create INDEX idx_regtime on imc_user(reg_time)
    drop INDEX idx_regtime on imc_user(reg_time)#?
    create INDEX idx_sex on imc_user(sex)

    ##包含在 order by、group by、distinct 中的字段(查询顺序，方向，等必须一致)
    ##多表join的关联列
    EXPLAIN
    select course_id,b.class_name,d.type_name,c.level_name,title,score
    from imc_course a 
    join imc_class b on b.class_id=a.class_id
    join imc_level c on c.level_id=a.level_id
    join imc_type d on d.type_id=a.type_id
    where c.level_name='高级'
    and b.class_name='MySQL';
    #在关联列上一定要建立好索引
    #因为a表和b表是通过class_id来建立索引的,a表和c表是通过level_id来建立索引的
    #根据哪个多来决定哪个是索引优先级高 分类>方向>难度
    create index idx_classid_typeid_levelid on imc_course(class_id,type_id,level_id)
    create index idx_levelname on imc_level(level_name)
```
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/13.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/sql_optimize/img/optimize/14.png)

```sql
#区分度最高-》该列唯一值在该列总列数的比值，比值越大，区分度越高
```
###索引使用的误区
```
索引越多越好
使用IN列表查询不能用到索引
查询过滤顺序必须同索引键顺序相同才可以使用到索引（mysql会根据实际情况调整过滤条件顺序以适应索引键值得顺序）
```
###sql改写优化
```sql
## 使用 outer join 代替 not in
    #查询出不存在课程的分类名称
    EXPLAIN
    select class_name
    from imc_class
    where class_id not in (select class_id from imc_course)

    EXPLAIN
    select class_name
    from imc_class a
    left join imc_course b on a.class_id=b.class_id
    where b.class_id is null
    # mysql8.0可以自动优化 将 not in 改为 left join 条件了

## 使用cte(公共表表达式)代替子查询 生成多次引用的临时表
## 拆分复杂的大SQL为多个简单的小SQL

## 巧用计算列优化查询(mysql5.7以后新增的功能)
    查询对于内容，逻辑和难度三项评分之后大于28分的用户评分。
    EXPLAIN
    select * from imc_classvalue
    where (content_score+level_score+logic_score) > 28

    alter table imc_classvalue
    add column total_score decimal(3,1) as
    (content_score+level_score+logic_score)

    show create table imc_classvalue
    create index idx_totalScore on imc_classvalue(total_score)

    EXPLAIN
    select * from imc_classvalue
    where total_score > 28

```






