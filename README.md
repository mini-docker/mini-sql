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




