# Mysql


## 安装

下载地址：[MySQL dwonload](https://dev.mysql.com/downloads/mysql/)
所有平台都可以在这里选择需要的 *MySQL Community Server* 版本及对应的平台

具体安装方法参考：[MySQL install](http://www.runoob.com/mysql/mysql-install.html)

windows 解压下载的文件，在解压的文件夹下创建 **my.ini** 配置文件，编辑配置：

> [mysqld]  
> *# 设置3306端口*  
> port=3306  
> *# 设置mysql的安装目录*  
> basedir=E:\\mysql 
> *# 设置mysql数据库的数据的存放目录*  
> datadir=E:\\mysql\Data  
> *# 允许最大连接数*  
> max_connections=200  
> *# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统*  
> max_connect_errors=10  
> *# 服务端使用的字符集默认为UTF8*  
> character-set-server=utf8  
> *# 创建新表时将使用的默认存储引擎*  
> default-storage-engine=INNODB  
> *# 默认使用“mysql_native_password”插件认证*  
> default_authentication_plugin=mysql_native_password  
>  
> [mysql]  
> *# 设置mysql客户端默认字符集*  
> default-character-set=utf8  
>  
> [client]  
> *# 设置mysql客户端连接服务端时默认使用的端口*  
> port=3306  
> default-character-set=utf8  

用管理员身份打开 cmd 命令行工具，切换目录
> E:  
> cd mysql\bin  

安装时可以为 mysql 服务创建名称，方便建立多个 mysql 服务不冲突，否则默认 mysql 服务名为 mysql
> mysqld install [sql server name]
> 

初始化数据库，执行完成会输出 root 用户的初始默认密码，复制后可以在要求输入密码时粘贴
> mysqld \-\-initialize \-\-console  

启动
> net start mysql

![全部命令](/images/mysql.png)

# 登陆 MySQL

> mysql -h 主机名 -u 用户名 -p
* -h : 指定客户端所要登录的 MySQL 主机名, 登录本机(localhost 或 127.0.0。1)该参数可以省略;
* -u : 登录的用户名;
* -p : 告诉服务器将会使用一个密码来登录, 如果所要登录的用户名密码为空, 可以忽略此选项。

如果登陆本机的 mysql 数据库，直接输入，提示输入密码，然后将之前的默认初始密码复制粘贴输入
> mysql -u root -p  
> Enter password:

## 其他命令

**mysql 命令一定要在结尾加 ;**

卸载
> mysqld remove

关闭 mysql 服务
> net stop mysql

修改用户密码
> mysql>alter user 'root'@'localhost' identified by 'newpassword';

列出所有数据库
> mysql>show databases;

创建数据库
> mysql>create database dbname;

切换数据库
> mysql>use dbname;  
> Database changed

导入 sql 文件
> mysql>source e:\mysql\sql.sql

列出所有表
> mysql>show tables;

显示数据表结构
> mysql>describe tablename;

删除数据库和数据表
> mysql>drop database dbname;  
> mysql>drop table tablename;

### 创建表

创建数据表需要以下信息 :  
* 表名
* 表字段名
* 定义各个表字段

> CREATE TABLE table_name (column_name column_type);

创建数据表 tbl :  

``` sql
CREATE TABLE IF NOT EXISTS `tbl` (
  `id` INT UNSIGNED AUTO_INCREMENT,
  `title` VARCHAR(100) NOT NULL,
  `author` VARCHAR(40) NOT NULL,
  `submission_date` DATE,
  PRIMARY KEY ( `id` )
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

* 设置字段属性为 **NOT NULL**，如果插入的时候该字段的数据为 NULL 时，就会报错
* **AUTO_INCREMENT** 定义列为自增的属性，一般用于主键，数值会自动加 1
* **PRIMARY KEY** 用于定义列为主键，也可以使用多列定义主键，列间用逗号隔开
* **ENGINE** 设置存储引擎，**CHARSET** 设置编码

### 删除表

> DROP TABLE table_name;

### 插入数据

可以插入一行或者多行数据

> INSERT INFO table_name  
> ( field1, field2, field3, ... )  
> VALUES  
> ( value1, value2, value3, ... ),  
> ( value1, value2, value3, ... ),  
> ...  
> ( value1, value2, value3, ... );  

### 查询数据

> SELECT column_name, column_name  
> FROM table_name [, table_name]  
> [WHERE Clause]  
> [LIMIT N] [OFFSET M]  

* SELECT \*: 返回所有记录
* LIMIT N: 返回 N 条记录
* OFFSET M: 跳过 M 条记录，默认 M=0
* LIMIT N,M: 相当于 LIMIT M OFFSET N，从第 N 条记录开始，返回 M 条记录

#### UPDATE 语句

> UPDATE table_name   
> SET field1=new_value1, field2=new_value2  
> [WHERE Clause]

#### DELETE 语句

> DELETE FROM table_name  
> [WHERE Clause]

* 如果没有指定 WHERE 语句，表中的所有记录将被删除

#### drop, truncate, delete 的区别

* 不需要表了，用 drop
* 保留表结构，删除所有记录，用 truncate
* 删除部分记录（always with a WHERE clause），用 delete
* delete 是 DML 语句，操作完不想提交事务可以回滚
* truncate 和 drop 是 DDL 语句，操作完马上生效，不能回滚
* 执行速度：drop > truncate > delete

#### LIKE 子句

与 **WHERE** 搭配使用，进行模糊匹配，与 **%** 和 **_** 结合使用

**%** 匹配多个任意字符，**_** 匹配一个任意字符

