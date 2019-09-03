### 数据库修改 SQL 语句

```sql
ALTER  DATABASE  CourseDB  RENAME TO  CourseManageDB;

列完整性约束关键词
    PRIMARY KEY——主键
    NOT NULL——非空值
    NULL——空值
    UNIQUE——值唯一
    CHECK——有效性检查
    DEFAULT——缺省值

CREATE  TABLE  Student
（ StudentID  		char(13)  		PRIMARY  KEY,
   StudentName  	varchar(10)  	NOT  NULL,
   StudentGender  	char(2)  		NULL,
   BirthDay  		date  		    NULL,
   Major  			varchar(30)  	NULL,
   StudentPhone  	char(11)   		NULL
 ）;

 CREATE  TABLE  Course
( CourseID  	char(4)      PRIMARY  Key,
  CourseName  	varchar(20)  NOT  NULL  UNIQUE,
  CourseType  	varchar(10)  NULL CHECK(CourseType IN('基础课','专业课','选修课')),
  CourseCredit  	smallint   	 NULL,
  CoursePeriod  	smallint     NULL,
  TestMethod  	char(4)      NOT  NULL  DEFAULT  '闭卷考试'
);

CREATE  TABLE  Plan
( CourseID  	char(4)  	NOT  NULL,
  TeacherID  	char(4)  	NOT  NULL,
  CourseRoom  	varchar(30),
  CourseTime  	varchar(30),
  Note  		varchar(50),
  CONSTRAINT	CoursePlan_PK	PRIMARY Key(CourseID,TeacherID)
);

CREATE TABLE  <表名>
 （ <代理键列名>  <Serial数据类型>  NOT NULL，
	<列名2>  <数据类型>  [列完整性约束]，	
    <列名3>  <数据类型>  [列完整性约束]，
    …
    CONSTRAINT  <约束名>  PRIMARY Key(代理键列名)
   )；

1) ADD修改方式，用于增加新列或列完整性约束
    ALTER TABLE <表名> ADD <新列名称><数据类型>|([完整性约束](varchar(20)))
2）DROP修改方式，用于删除指定列或列的完整性约束条件
    ALTER TABLE <表名> DROP  COLUMN <列名>；
    ALTER TABLE <表名> DROP  CONSTRAINT<完整性约束名>；
3）RENAME修改方式，用于修改表名称、列名称
    ALTER TABLE <表名> RENAME TO <新表名>；
    ALTER TABLE <表名> RENAME <原列名> TO <新列名>；
4）ALTER修改方式，用于修改列的数据类型
    ALTER TABLE <表名> ALTER  COLUMN <列名> TYPE<新的数据类型>；




数据查询SQL语句格式
    SELECT  [ALL|DISTINCT]  <目标列>[，<目标列>…]
    [ INTO <新表> ]
    FROM  <表名|视图名>[，<表名|视图名>…]
    [ WHERE  <条件表达式> ]
    [ GROUP BY  <列名> [HAVING <条件表达式> ]]
    [ ORDER BY  <列名> [ ASC | DESC ] ];

    SELECT  *   FROM  <关系表>  WHERE  <条件表达式>;

Where条件子句
    在WHERE子句中可以使用如下方式，指定范围数据。
    1）使用BETWEEN..AND关键词来限定列值范围，还可以使用关键词LIKE与通配符来限定查询条件。
    2）使用通配符来限定字符串数据范围。下划线（_）通配符用于代表一个未指定的字符。百分号（%）通配符用于代表一个或多个未指定的字符。
    SELECT  *
    FROM  STUDENT
    WHERE BirthDay BETWEEN ‘2000-01-01’ AND ‘2000-12-30’;

    SELECT  *
    FROM  STUDENT
    WHERE  Email  LIKE  ’%@163.com’;

    SELECT  StudentID, StudentName, StudentGender, Major
    FROM  STUDENT
    WHERE  Major=’软件工程’  AND  StudentGender=’男’;

-- 例4 在STUDENT表查询时，使用IN关键字限定范围”计算机应用”专业的学生。其SQL语句如下所示。
    SELECT  StudentID, StudentName, StudentGender, Major
    FROM  STUDENT
    WHERE  Major IN  ('计算机应用');

    SELECT  *
    FROM  STUDENT
    ORDER  BY  Birthday DESC;

    SELECT  *
    FROM  STUDENT
    ORDER  BY  Birthday DESC ,  StudentName  ASC;

    -- 例2 若要分专业统计STUDENT表中男生人数，但限定只显示人数大于2的人数，其查询SQL语句如下：
    SELECT  Major  AS 专业,  COUNT（StudentID） AS 学生人数
    FROM  Student
    WHERE  StudentGender=’男’
    GROUP  BY  Major
    HAVING  COUNT(*)>2;

    -- 例 在选课管理系统数据库中，希望获得各个学院的教师信息列表，包括学院名称、教师编号、教师姓名、教师性别、职称等信息。要求按学院名称、教师编号分别排序输出，其查询SQL语句如下：
    SELECT  B.CollegeName AS 学院名称,  A.TeacherID  AS 编号, A.TeacherName  AS 姓名,  A.TeacherGender  AS 性别,  A. TeacherTitle  AS 职称
    FROM  Teacher  AS  A，College  AS  B
    WHERE  A.CollegeID=B.CollegeID 
    ORDER  BY  B.CollegeName, A.TeacherID;

    SELECT  <目标列>[，<目标列>…]
    FROM  <表名1>  JOIN  <表名2>  ON <连接条件>;
```

### 权限处理
```sql
1) GRANT语句是一种由数据库对象创建者或管理员执行的权限授予语句，它可以把访问数据库对象权限授予给其他用户或角色。
    GRANT  <权限列表>  ON  <数据库对象>  TO  <用户或角色> [ WITH GRANT OPTION ]；
例 在选课管理系统数据库中，将课程注册表REGISTER的数据插入、数据修改、数据删除、数据查询访问权限赋予学生角色RoleS。
    GRANT  SELECT, INSERT, UPDATE, DELETE  ON  REGISTER  TO  RoleS;

2) REVOKE语句是一种由数据库对象创建者或管理员将赋予其它用户或角色的权限进行收回语句，它可以收回原授予给其他用户或角色的权限。
    REVOKE  <权限列表>  ON  <数据库对象>  FROM  <用户或角色> ;

例 在选课管理系统数据库中，收回学生角色RoleS在课程注册表REGISTER的数据删除访问权限。
    REVOKE  DELETE  ON  REGISTER  FROM  RoleS;

3) DENY语句用于拒绝给当前数据库内的用户或者角色授予权限，并防止用户或角色通过其组或角色成员继承权限。
    DENY  <权限列表>  ON  <数据库对象>  TO  <用户或角色> ;
例 在选课管理系统数据库中，若拒绝教师角色RoleT对教师表TEACHER的数据删除访问权限。
    DENY  DELETE  ON  TEACHER  TO  RoleT;

```

### 视图
```sql
    通过视图，可提供一定程度的数据逻辑独立性。当数据表结构发生改变，只要视图结构不变，应用程序可以不作修改。
    集中展示用户所感兴趣的特定数据
    通过视图，可以将部分用户不关心的数据进行过滤，仅仅提供他们所感兴趣的数据。

例 在选课管理系统数据库中，若需要建立一个查看基础课数据的视图BasicCourseView，其创建SQL语句如下。
    CREATE  VIEW  BasicCourseView  AS
    SELECT  CourseName,  CourseCredit,  CoursePeriod,  TestMethod
    FROM    COURSE
    WHERE  CourseType=‘基础课’;

    DROP  VIEW  <视图名>；

```