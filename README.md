# mini-sql
sql nosql

```shell
yum install wget -y
brew install wget
# 重置mysql密码
alter user user() identified by "123456"
# 登录mysql
mysql -h 127.0.0.1 -P 33062 -u root -proot

```

### sql
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/1.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/2.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/3.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/4.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/5.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/6.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/7.png)

### 设计逻辑
```shell
第一范式：表中的所有字段都是不可再分的
第二范式：表中必须存在业务主键,并且非主键依赖于全部业务主键
第三范式：表中的非主键列之间不能相互依赖
# 反范式化设计
章节表与小结表之间存在着一对多的关系，即可合成一个表。减少一个关联表
```
### 物理设计
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/8.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/9.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/10.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/11.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/12.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/13.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/14.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/15.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/16.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/17.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/18.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/19.png)
### GRANT
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/20.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/21.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/22.png)

```shell
# windows的数据库安装使用
yum install python-setuptools python-dev
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
pip install --upgrade setuptools
pip install PyMySQL
python 
import pymysql

# mysql环境下
help create user; #查看数据库用户的创建数据
show plugins; #查看数据库插件
# 建立账号
create user mc_test@'192.168.1.%' identified by '123456' with max_user_connections 1;
mysql -umc_test -p123456 -h192.168.43.237
show privileges\G //查看账号权限
# 账户授权
help GRANT; # 查看GRANT参数

```
### DDL(Data Definition Language)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/23.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/24.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/25.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/26.png)
```shell
create database imc_db;
help create table; #查看创建table的语法

truncate table imc_note; #清空表，不会有sql的记录
rename table imc_note to bak_imc_note; #重命名表

# 唯一索引 couseid
# UNIQUE KEY `udx_couseid` (`course_id`,`chapter_name`)
CREATE TABLE `imc_chapter` (
  `chapter_id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '章节ID',
  `course_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '课程ID',
  `chapter_name` varchar(50) NOT NULL DEFAULT '' COMMENT '章节名称',
  `chapter_info` varchar(200) NOT NULL DEFAULT '' COMMENT '章节说明',
  `chapter_no` tinyint(2) unsigned zerofill NOT NULL DEFAULT '00' COMMENT '章节编号',
  PRIMARY KEY (`chapter_id`),
  UNIQUE KEY `udx_couseid` (`course_id`,`chapter_name`)
) ENGINE=InnoDB AUTO_INCREMENT=1330 DEFAULT CHARSET=utf8 COMMENT='课程章节';



```

### DML(Data Manipulation language)
https://github.com/mini-docker/mini-shell/blob/master/mysql.md
- 新增表中的数据：insert into
- 删除表中的数据：delete
- 修改表中的数据：update
- 查询表中的数据：select
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/27.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/28.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/29.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/30.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/31.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/32.png)

```sql
# 内关联
# 查询由course_id相关联的imc_course,imc_chapter得到一个新表(course_id,title,chapter_name)
SELECT a.course_id,a.title,b.chapter_name FROM imc_course a JOIN imc_chapter b ON b.course_id=a.course_id;
SELECT a.course_id,a.title,b.chapter_name FROM imc_course a LEFT JOIN imc_chapter b ON b.course_id=a.course_id where title='MYSQL关联查询';

INSERT INTO imc_course(title,title_desc,type_id,class_id,level_id,online_time,user_id)
VALUES('MYSQL关联查询','测试MYSQL关联查询',8,1,1,NOW(),29);

select * from imc_course where title='MYSQL关联查询';


# 查询出只存在于imc_course而不存在于imc_chapter的(course_id,title);
select course_id,title from imc_course where course_id not in (select b.course_id from imc_chapter b);
select a.course_id,a.title from imc_course a left join imc_chapter b on b.course_id=a.course_id where b.course_id is null;


# group by...having 字句的作用
把结果集按某些列分成不同的组，并对分组后的数据进行聚合操作
可以通过可选的having字句对聚合后的数据进行过滤

select level_name,count(*) from imc_course a join imc_level b on b.level_id=a.level_id group by level_name;

select title,study_cnt from imc_course where title like '%mysql%' or study_cnt>5000;
select title,study_cnt from imc_course where title like '%mysql%' and study_cnt<5000;
select title,study_cnt from imc_course where title not like '%mysql%' and study_cnt>5000;

# 统计出每个分类不同难度的课程的数量
show variables like 'sql_mode';
set session sql_mode='ONLY_FULL_GROUP_BY';

select level_name,class_name,COUNT(*)
from imc_course a join imc_class b on b.`class_id`=a.class_id
join imc_level c on c.level_id=a.level_id group by level_name,class_name;

# 统计每个分类下课程大于3门的难度有哪些。
select class_name,level_name,COUNT(*)
from imc_course a join imc_class b on b.`class_id`=a.class_id 
join imc_level c on c.level_id=a.level_id 
group by class_name,level_name HAVING COUNT(*)>3;

## 关于count
# 统计课程表的总课程数 DISTINCT(去重)
select COUNT(course_id),COUNT(DISTINCT user_id) FROM imc_course;
# 统计所有课程总的学习人数
select SUM(study_cnt) from imc_course;

select level_name,SUM(study_cnt) from imc_course a join imc_level b on b.level_id=a.level_id
group by level_name;

# 统计出每门课程的平均学习人数 AVG
select SUM(study_cnt)/COUNT(study_cnt),AVG(study_cnt) from imc_course;
# 利用课程评价表中的评分，更新课程表中课程的评分（1）
select course_id
  ,AVG(content_score) AS avg_content
  ,AVG(level_score) AS avg_level
  ,AVG(logic_score) AS avg_logic
  ,AVG(score) AS avg_score
from imc_classvalue group by course_id;

# 查询出学习人数最多的课程
select title,study_cnt FROM imc_course
where study_cnt = (select MAX(study_cnt) from imc_course);

```
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/33.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/34.png)
```sql
# 查询出每门课程的学习人数并按学习人数从高到低排列
select title,study_cnt from imc_course order by study_cnt DESC;
# 分页返回课程ID和课程名称，每页返回10行记录
select course_id,title from imc_course order by study_cnt desc limit 0,10
```
```sql
# 创建视图
## 定义一个包括课程ID，课程名称，课程分类，课程方向以及课程难度的视图
create view vm_course as
select a.course_id,a.title,b.`class_name`,c.type_name,d.level_name
from imc_course a
join imc_class b on b.`class_id`=a.class_id
join imc_type c on c.type_id=a.type_id
join imc_level d on d.level_id=a.level_id
## 查询视图
select * from vm_course;
```
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/35.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/36.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/37.png)
```sql
update imc_user set user_status=0 where user_nick='沙占';
select user_nick,user_status from imc_user where user_nick='沙占';

#随机推荐10门课程
show create table imc_course;
update imc_course set is_recommand = 1 order by rand() limit 10;
select course_id,title from imc_course where is_recommand=1;

# 利用课程评价表中的评分，更新课程表中课程的评分（2）
update imc_course a join 
(
  select course_id
  ,AVG(content_score) AS avg_content
  ,AVG(level_score) AS avg_level
  ,AVG(logic_score) AS avg_logic
  ,AVG(score) AS avg_score
  from imc_classvalue group by course_id;
) b on a.`course_id`=b.course_id
set a.`content_score`=b.avg_content,
  ,a.`level_score`=b.avg_level
  ,a.`logic_score`=b.avg_logic
  ,a.`score`=b.avg_score


 select CURDATE(),CURTIME(),NOW();
 select DATE_FORMAT(NOW(),'%Y%m%d %H-%i-%s')
 select SEC_TO_TIME(60),TIME_TO_SEC('1:00:00');

 #计算每门课程，上线时间距当前时间的天数
 select title,DATEDIFF(NOW(),online_time)
 from imc_course order by 2 DESC;

```
### 常用的字符串函数
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/38.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/39.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/40.png)
```sql
# 出于seo优化的目的，我们需要合并显示课程分类名称和课程标题
-- select concat(class_name,title)
select concat_ws('||',class_name,title)
from imc_course a join imc_class b on b.class_id=a.class_id;

# 123,456.7890
select class_name,length(class_name),char_length(class_name) from imc_class;
select format(123456.789,4)
#www,com
select left('www.baidu.com',3),right('www.baidu.com',3);
select substring('www.baidu.com',5) #baidu.com
#0.100
select substring_index('192.168.0.100','.',-2)
## 截取课程标题里中横线之前的部分
select title,
locate('-',title),
substring(title,1,locate('-',title)),
substring(title,1,locate('-',title)-1),
substring_index(title,'-',1)
from imc_course;

#baidu
select trim('  baidu  '),trim('x' from 'xxxxxxxbaiduxxxx');

#显示每个用户的昵称和性别
select user_nick
  ,case when sex=1 then '男'
  when sex=0 then '女'
  else '未知'
  end as '性别' from imc_user
  where 
  case when sex=1 then '男'
  when sex=0 then '女'
  else '未知'
  end='男';
```
### SQL高级特性(>=8.0)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/40.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/41.png)
```sql
with cte as(
  select title,study_cnt,class_id
  from imc_course
  where study_cnt>2000
)
select * from cte
union all select * from cte order by title;

# CTE递归生成序列  recursive(允许自引用)
with recursive test as (
  select 1 as n
  union all select 1+n from test where n<10
)
select * from test;

# 递归查询课程评论信息
with recursive replay(quest_id,quest_title,user_id,replyid,path)
as
(
  select quest_id,quest_title,user_id,replyid,cast(quest_id as char(200)) as path
  from imc_question where course_id=59 and replyid=0
  union all
  select a.quest_id,a.quest_title,a.user_id,a.replyid,CONCAT(b.path,'>>',a.quest_id)
  from imc_question a 
  join replay b on a.replyid=b.quest_id
)
select * from replay;

```
### 窗口函数
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/41.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/42.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/43.png)
- ![sql](https://github.com/mini-docker/mini-sql/blob/master/img/44.png)

```sql
# row_number,rank,dense_rank 之间的区别
with test(study_name,class_name,score) as (
  select 'sqlercn','MySQL',95
  union all
  select 'tom','MySQL',99
  union all
  select 'Jerry','MySQL',99
  union all
  select 'Gavin','MySQL',98
  union all
  select 'sqlercn','PostGreSQL',99
  union all
  select 'tom','PostGreSQL',99
  union all
  select 'Jerry','PostGreSQL',98
)
select study_name,class_name,score
  ,row_number() over(partition by class_name order by score desc) as rw
  ,rank() over(partition by class_name order by score desc) as rk
  ,dense_rank() over(partition by class_name order by score desc) as drk
from test 
order by class_name,rw;

# 按学习人数对课程进行排名，并
# 列出每类课程学习人数排名前三的课程名称，
# 学习人数以及名次。
with tmp as (
  select class_name,title,score
  ,rank() over(partition by class_name order by score desc) as cnt
  from imc_course a
  join imc_class b on b.`class_id`=a.`class_id`
)
select * from tmp where cnt<=3;

# 每门课程的学习人数占本类课程总学习人数的百分比
with tmp as (
  select class_name,title,study_cnt
    ,SUM(study_cnt) over(partition by class_name) as class_total
  from imc_course a 
  join imc_class b on b.`class_id`=a.`class_id`
)
select class_name,title,CONCAT(study_cnt/class_total*100,'%') 
from tmp order by class_name;

# 查询出分类ID为5的课程名称和分类名称
select a.`class_name`,b.`title`,a.`class_id`
from imc_class a 
join imc_course b 
on b.`class_id`=a.`class_id` where a.`class_id`=5;
# 执行SQL中易犯的错误
select * 
from imc_course
where title in (select imc_class.title from imc_class)
```




