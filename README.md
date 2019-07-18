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


```





