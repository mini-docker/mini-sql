
### 事务
数据库基本原理
https://blog.csdn.net/AllenWells/article/details/47419797
- ![sql](https://github.com/mini-docker/mini-sql/blob/handling_high_concurrency/img/high_%20concurrency/1.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/handling_high_concurrency/img/high_%20concurrency/2.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/handling_high_concurrency/img/high_%20concurrency/3.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/handling_high_concurrency/img/high_%20concurrency/4.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/handling_high_concurrency/img/high_%20concurrency/5.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/handling_high_concurrency/img/high_%20concurrency/6.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/handling_high_concurrency/img/high_%20concurrency/7.png)

### serializable 级别两个事务并发的情况
```sql
#事务一
set session transaction isolation level serializable;
show variables like '%iso%';

begin;
select course_id,title
from imc_course
where score>9.6;
-- rollback;
#事务二
begin;

update imc_course
set score=9.8
where course_id=34;
-- commit;

``` 
### read committed 级别两个事务并发的情况
```sql
#事务一
set session transaction isolation level read committed;
show variables like '%iso%';

begin;
select course_id,title
from imc_course
where score>9.6;
-- rollback;

#事务二
begin;
update imc_course 
set score=9.8
where course_id=37;
-- commit;

```
### read uncommitted 级别两个事务并发的情况
```sql
set session transaction isolation level read uncommitted;
show variables like '%iso%'
...
```
- ![sql](https://github.com/mini-docker/mini-sql/blob/handling_high_concurrency/img/high_%20concurrency/8.png)

### 阻塞和死锁
```sql
#什么是阻塞
由于不同锁之间的兼容关系，造成的一事务需要等待另一事务释放其所占用的资源的现象
--如何发现阻塞

#如何处理阻塞？
终止占用资源的事务，优化占用资源事务的SQL，使其尽快释放资源
#什么是死锁？
并行执行的多个事务相互之间占有了对方所需要的资源
#如何发现死锁？
set global innodb_print_all_deadlocks=on;
#如何处理死锁？
数据库自行回滚占用资源少的事务
并发事务按相同顺序占有资源

```
