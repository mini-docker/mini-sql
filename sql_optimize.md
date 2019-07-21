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
