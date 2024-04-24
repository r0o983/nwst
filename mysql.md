# Centos 7 安装mysql 以及设置

## 安装Mysql 默认安装后密码所在位置：

`grep password /var/log/mysqld.log`

修改密码：

`alter user user() identified by'1234'`

**如果遇到不可修改当前密码，则需要先修改一个复杂的密码后进行修改密码设置规则**

查看当前密码设置规则：

`show variables like 'validate_password%';`

设置当前密码设置规则：

`set global validate_password_policy=0;` 8.0之前修改方式

`set global validate_password.policy=0;` 8.0之后修改方式

设置当前密码长度：

`set global validate_password_length=4;`8.0之前修改方式

`set global validate_password.length=4;`8.0之后修改方式

修改完成后刷新权限：

`flush privieleges;`





## 二进制安装方式：

`https://dev.mysql.com/downloads/mysql/` 选择对应的版本

将安装包上传到服务器当中或者使用wget进行下载

`wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.35-linux-glibc2.12-x86_64.tar.gz`

解压当前文件

`tar -xvf mysql-5.7.35-linux-glibc2.12-x86_64.tar.gz`

安装依赖

`yum install -y libaio libaio-devel`

删除之前的MariaDB配置文件

`rpm -aq | grep mariadb`

`yum remove -y  mariadb-libs-5.5.68-1.el7.x86_64`

创建一个新的安装路径

`mkdir /application`

将解压后的数据移动到该目录下并改名为mysql

`mv mysql-5.7.35-linux-glibc2.12-x86_64 /application/mysql `

### ~~新增一块数据盘~~（数据持久化

在虚拟机或者云服务器中添加一块新硬盘

查看所有的硬盘信息`fdisk -l` 

使用`fdisk`来进行分区操作

使用`p`查看分区

使用`n`进行分区，再次输入`p`进行分区，默认三次回车（此时我只需要进行将这一块硬盘进行分区）

使用`w`进行写入

使用`mkfs -t ifs /dev/sdb1` 设置磁盘格式

查看新增硬盘的`uuid`

`blkid` 将新硬盘的`uuid`给记录下来，进行开机自动挂载

修改`/etc/fstab`文件中的内容，将刚才记录的uuid给添加到该文件中

`UUID=ab574cdc-b6af-4066-b209-5e94b1c8388d /data xfs defaults 0 0  ` 

参数说明：第一个参数为挂载目录，`xfs`为磁盘格式，后面的两个0，第一个代表备份，第二个代表开机自检

挂载 `mount /dev/sdb1 data/`

添加一个新用户：`useradd -s /sbin/nologin mysql `

修改所属权`chown -R mysql:mysql application/`

修改环境变量：`export PATH=/application/mysql/bin:$PATH`

刷新环境变量：`source /etc/profile`

以非安全模式进入初始化：`mysqld --initialize-insecure --user=mysql --basedir=/application/mysql --datadir=/data`

**参数说明：**

* Insecure  非安全模式进入
* user 指定用户
* basedir 文件路径
* datadir 数据存放路径

编辑mysql配置文件

`vim /etc/mysql.cnf`

```shell
[mysqld]
user=mysql
basedir=/application/mysql
datadir=/date
port=3306
socket=/tmp/mysql.sock
[mysql]
socket=/tmp/mysql.sock
```

启动服务：

将服务文件复制到系统目录`cp /application/mysql/support-files/mysql.server /etc/init.d/mysqld`

启动：`/etc/init.d/mysqld start`

设置mysql用户密码：

`mysqladmin -u root -p password` 

第一次输入密码为原始密码

第二次输入为新密码

第三次输入为确认新密码

使用systemctl方式进行启动mysql

```shell
cat>/etc/systemd/system/mysqld.service <<EOF
[Unit]
Description=MySQL Server
Documentation=man:mysqld(8)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target
[Install]
WantedBy=multi-user.target
[Service]
User=mysql
Group=mysql
ExecStart=/application/mysql/bin/mysqld --defaults-file=/etc/my.cnf
LimitNOFILE = 5000
EOF
```

设置开机启动：

`systemctl enable mysqld`



## mysql 忘记密码

首先停止该服务`systemctl stop mysqld`

`mysqld_safe --skip-grant-tables --skip-networking &`

修改密码：

`alter user root@"localhost" identified by "123";`

如果执行不成功则先刷新权限：`flush privileges;`

执行成功后kill掉启动的服务

`pkill mysqld`

验证新密码是否可以成功登陆

`mysql -uroot -p123`



## DCL (data control language)

**数据控制语言**

查看当前数据库中有哪些用户

`select user,host from mysql.user;` dql 语句

### 添加用户：

`create user sparrow@"%" identified by "123";`

查看当前数据库的版本`select @@version;`

查看当前系统的加密方式：`select user,plugin from mysql.user;`

如果使用8.0的mysql无法连接7.*的数据库时，可以进行修改加密方式从而进行登陆

`alter user sparrow@"%" identified with mysql_native_password by "123";`

#### 新增用户并设置最大连接数

`create user test1@"%" identified by "1234" with max_user_connection 1;`

### 删除用户：

`drop user test1@"%";`

### 锁定用户：(DDL)

`alter user test@"%" account lock;`

#### 解锁用户：

`alter user test@"%" account unlock;`

### 修改密码：

* 修改管理员密码：
  * `mysqladmin -u root -p password`
* 修改普通用户密码
  * 进入mysql终端(以下两种方式任选其一)
  * `alter user sparrow@"%" identified by "1234";`
  * `set password for sparrow@"%" = password("123456");`

### 用户授权：

`grant all on *.* to sparrow@"%" identified by "1234";`

参数说明：

`grant` 授权命令

`all`	权限

`*.*`  指定某一个库,*代表全部

mysql的5.7版本可以使用该命令进行创建用户后直接授权.

注意：使用该命令时需要使用root用户

**如果在8.0版本中遇到授权问题，那么就需要先进行创建用户，后进行授权**

```mysql
select user,host from mysql.user;
create user sparrow@"%" IDENTIFIED by "qQwe123.123"
grant all on *.* to sparrow@"%" ;
```

#### 查看全部授权权限

`show privileges;`

```mysql
+-------------------------+---------------------------------------+-------------------------------------------------------+
| Privilege               | Context                               | Comment                                               |
+-------------------------+---------------------------------------+-------------------------------------------------------+
| Alter                   | Tables                                | To alter the table                                    |
| Alter routine           | Functions,Procedures                  | To alter or drop stored functions/procedures          |
| Create                  | Databases,Tables,Indexes              | To create new databases and tables                    |
| Create routine          | Databases                             | To use CREATE FUNCTION/PROCEDURE                      |
| Create temporary tables | Databases                             | To use CREATE TEMPORARY TABLE                         |
| Create view             | Tables                                | To create new views                                   |
| Create user             | Server Admin                          | To create new users                                   |
| Delete                  | Tables                                | To delete existing rows                               |
| Drop                    | Databases,Tables                      | To drop databases, tables, and views                  |
| Event                   | Server Admin                          | To create, alter, drop and execute events             |
| Execute                 | Functions,Procedures                  | To execute stored routines                            |
| File                    | File access on server                 | To read and write files on the server                 |
| Grant option            | Databases,Tables,Functions,Procedures | To give to other users those privileges you possess   |
| Index                   | Tables                                | To create or drop indexes                             |
| Insert                  | Tables                                | To insert data into tables                            |
| Lock tables             | Databases                             | To use LOCK TABLES (together with SELECT privilege)   |
| Process                 | Server Admin                          | To view the plain text of currently executing queries |
| Proxy                   | Server Admin                          | To make proxy user possible                           |
| References              | Databases,Tables                      | To have references on tables                          |
| Reload                  | Server Admin                          | To reload or refresh tables, logs and privileges      |
| Replication client      | Server Admin                          | To ask where the slave or master servers are          |
| Replication slave       | Server Admin                          | To read binary log events from the master             |
| Select                  | Tables                                | To retrieve rows from table                           |
| Show databases          | Server Admin                          | To see all databases with SHOW DATABASES              |
| Show view               | Tables                                | To see views with SHOW CREATE VIEW                    |
| Shutdown                | Server Admin                          | To shut down the server                               |
| Super                   | Server Admin                          | To use KILL thread, SET GLOBAL, CHANGE MASTER, etc.   |
| Trigger                 | Tables                                | To use triggers                                       |
| Create tablespace       | Server Admin                          | To create/alter/drop tablespaces                      |
| Update                  | Tables                                | To update existing rows                               |
| Usage                   | Server Admin                          | No privileges - allow connect only                    |
+-------------------------+---------------------------------------+-------------------------------------------------------+
```

#### 授权某些字段的权限

`grant select(plugin) on mysql.user to test1@"%" identified by "1234"; `

授权该用户只能查询mysql.user下的plugin字段。

#### 授权用户创建用户权限

`grant create on *.* to sparrow@"%";`

**授权多种权限用逗号（，）分开**



**注意**避免使用如下命令：All 以及grant option

grant all on *.* to test6@"%" identified by "123" with grant option;

参数说明：`grant option` 可以使该用户给其他的用户进行授权

### 查看用户权限

`show grants for sparrow@"%";`

#### 回收权限

`revoke select on *.* from sparrow@"%";`

回收用户的select权限

## DDL 数据定义语句

#### 创建新的数据库

* 如果该库不存在则创建，如果存在则不进行任何操作
  * `create database if not exists sparrow;`
  * `create database if not exists sparrow charset utf8mb4;` 设定编码字符集

##### 查询库定义（DML

```mysql
show databases ;
show create database sparrow;
```

##### 修改库定义字符集

```mysql
alter database sparrow charset utf8mb4;
```

##### 删除库定义：

```mysql
drop database sparrow;
```

**补充：库规范**

* 库名要和业务有关
* 不能使用大写字母
* 不能使用数字开头
* 显式设置字符集
* 不能使用系统内置字符



#### 表定义：

```mysql
create table student
(
    id int not null primary key auto_increment comment '学号，主键',
    sname varchar(32) not null comment '姓名',
    sage tinyint unsigned default 0 comment '年龄',
    telnum char(11) not null  unique comment '手机号' ,
    city enum('bj','sh','gz') not null  default 'bj' comment '城市',
    intime datetime not null default now() comment '入学时间'
)engine=innodb charset =utf8mb4 comment '学生表';
```

参数说明：

`not null ` 非空字段

`Primary key` 主键

`auto_increment ` 自增

`Comment ` 注释

`default`  指定默认值

`enum` 枚举

`datatime` 时间类型

`now()` 此参数为一个时间函数 

`engine=innodb` 指定数据库引擎(7.2版本后默认引擎为`innodb`)

**表规范**

* 表名要和业务有关
* 不能使用大写字母
* 不能使用数字开头
* 显式设置字符集和存储引擎
* 不能使用系统内置字符
* 表名控制长度在16字符以内
* 表名前缀为库名的缩写
* 选择合适，简短，足够的数据类型。
* 设置主键，数字自增
* 最好有一个时间戳列
* 每一列设置非空属性，能设置默认值时最好进行设置
* 注释
* 控制字段在50个字段内

##### 查询表

```mysql
show tables; //显示当前库下的所有表
show tables from mysql; //查询mysql库下的所有表
show create table student; //查询创建的表结构
desc student; //显示表结构
```

##### 修改表

```mysql
create table t1(
    id int not null primary key auto_increment,
    sname varchar(32) not null
)
```

* 加列
  * `alter table t1 add age tinyint unique not null default 0;`
* 插入列，使用after关键字
  * `alter table t2 add gender char(1) not null default "M" after id;`
* 在首位插入字段，使用first关键字
  * `alter table t2 add sid int not null first ;`
* 删除列
  * `alter table t2 drop sid;`
* 列属性变更，需要将之前的格式进行复写一遍，否则会清空其他以设置的属性。例如：`not null`
  * `alter  table t1 modify sname varchar(32) not null;`
* 修改列名，使用change
  * `alter table t1 change age sage tinyint unsigned not null default 0;`

##### 复制表

`create table t2 like t1;`

## DML数据操作语言

##### 插入数据

`insert into t2 ( sname, age)values ("张三",17);`

##### 更新数据

`UPDATE t3 set sname="里斯" where id=1;`

如果不加where条件则会修改全表数据，属于逻辑损坏

强制加上where条件`set global sql_safe_updates =1;`

##### 删除数据

`delete from t3 where id =7;`

如果不加where条件则会修改全表数据，属于逻辑损坏

强制加上where条件`set global sql_safe_updates =1;`

* 伪删除
  * 添加一列状态列`alter table t2 add state tinyint default 1;`
  * 修改状态列数据`update t2 set state=2 where age=12;`
  * 查询数据`select * from t2 where state =1;`

 ## DQL 数据查询语言

MySQL独创使用

`select version()` 查看当前数据库的版本

`select user() ` 查看当前操作的用户

`select database()` 当前操作的数据库

`select now();` 查看当前时间

`select length(database());` 查看当前数据库长度



常用

`select concat ('hello')` 将多列拼成一列显示

`select user,host from mysql.user;`

`select concat(user,host) from mysql.user;`



查询参数

`select @@port;` 查看当前端口

`select @@version;` 查看当前数据库版本

`select @@datadir;`  当前数据库存放路径

`select  @@basedir;` 查看数据库安装路径

`select @@log_bin;` 查看当前日志存储路径



高级应用

`set sparrow:=10;`



计算

`select 1+1`

`select PI() * 100`



查看表结构

`desc 表名`  如果不支持desc命令可以使用`show create table 表名`



最简单的select应用

`select * from city limit 100;`  查询时禁止使用`*` 查询数据，如有必要使用`limit`关键字限制查询条数；

`select * from city where CountryCode = 'chn' or CountryCode='usa';` 使用`or`关键字来查询多个结果

`select * from city where CountryCode = 'chn' and District='shanghai';`  使用`and` 关键字来合并多个查询条件

`where` 条件可以使用` = < > != >= <= in not in like `作为条件判断符

```mysql
select * from city where Population < 100;
select * from city where Population > 100;
select * from city where Population between 10000 and 20000;
select * from city where CountryCode in ('usa','jpn');
```

模糊查询`like`

`select * from city where CountryCode like "ch%";  #以ch开头的国家都会被查询出来`



#### Group By

`Group By + 分组函数（count, sum , min , max , avg , group_concat)`

查询`countrycode`下一共有多少个城市

`select CountryCode,count(*) from city group by CountryCode;`

计算`chn`下一共有多少人口

`select CountryCode,sum(Population) from city where CountryCode='chn' group by CountryCode;`



5.7版本新增的一个特性：`sql_mode=only_full_group_by` 沿用至8.0版本

`select @@sql_mode;`

```mysql
select CountryCode,count(*) ,name from city group by CountryCode; //错误的展示方式，5.7版本之前可用
select CountryCode,count(*),group_concat(Name) from city group by CountryCode;
```

统计中国每个省的总人口

`select District,sum(Population) from city where CountryCode='chn' group by District;`

#### Having (后过滤)

在`group by`之后还需要过滤数据时使用`having`

城市个数小于10个的国家

`select CountryCode,count(*),group_concat(Name) from city group by CountryCode having count(*) <10;`

**where 语句只能出现在group之前，不能出现在之后**

`having` 在生产环境尽量少用，因为不走索引

#### Order by

`order by` 末尾使用

```mysql
select CountryCode,count(*),group_concat(Name) from city group by CountryCode having count(*) >310 order by count(*) ;  默认进行升序排列
select CountryCode,count(*),group_concat(Name) from city group by CountryCode having count(*) >310 order by count(*) desc ;  降序排列
```

#### union and union all

将两个结果集合并在一个结果集中

```mysql
select  * from city where CountryCode = 'chn' union all select * from city where CountryCode='usa';
```

`union`和`union all` 的区别

`union` 会自动去重

`union all` 使用场景比较多，执行顺序为先执行第一条语句，后执行第二条语句，会将语句执行两次

#### limit

将查出的数据进行限制输出

`select * from city where CountryCode = 'chn' order by Population desc limit 10;`

将查出的数据进行分页输出，第一个参数为索引（从0开始），第二个参数为个数。

`select * from city where CountryCode = 'chn' order by Population desc limit 2,10;`



### 多表查询

下载库地址:`https://github.com/datacharmer/test_db`

快速查看当前库里面有哪些表，表中有哪些字段

```mysql
select table_name,group_concat(column_name) from information_schema.COLUMNS
where TABLE_SCHEMA = 'employees' group by table_name;
```

多表链接🔗

多表链接的意义在于，我们需要取得的数据不在一个数据表中，在多个不同的表中，需要将数据进行合并的操作

#### 三大类：

##### 笛卡尔乘积

```mysql
select * from a;
select * from b;
select * from a,b;
```

* 驱动表
  * 驱动表越小越好
  * 拿着哪一张表去找，那一个就是驱动表，拿着b表去找a表，b表就是驱动表

##### 内链接

* `select * from a,b where a.id=b.aid;`尽量不要使用这种方式
* `select * from a join b on a.id=b.aid;` 推荐使用`join on`  (简写)
* `select * from a inner join b on a.id=b.aid;`  完全体写法`inner join `

##### 外链接

* `select * from a left join b on a.id= b.aid;`

* ```mysql
  select * from b left join a on a.id = b.aid where b.addr is not null and aid > 3;
  select * from b left join a on a.id = b.aid where aid > 3;
  ```

* 驱动表

  * 以左表为驱动表

**基本定义：**
　　**left join （左连接）：返回包括左表中的所有记录和右表中连接字段相等的记录。
　　right join （右连接）：返回包括右表中的所有记录和左表中连接字段相等的记录。
　　inner join （等值连接或者叫内连接）：只返回两个表中连接字段相等的行。
　　full join （全外连接）：返回左右表中所有的记录和左右表中连接字段相等的记录。**

## mysql元数据

### 什么是元数据

> mysql中的元数据是用来反馈数据库实例的状态（例如：连接状态，运行状态等），存储数据库对象定义信息（例如：库，表，列，存储过程等）的数据，也就是在表数据行意外的数据的总称
>
> mysql为了保证元数据不被直接修改，删除，查询，原则上就是为了保证这些元数据，所以，在对于元数据修改时，通常会使用标准的DDL等语句方式修改，对于元数据查询提供了show，information_schema视图方式进行查询

### 元数据获取方法

```mysql
help show
help create table;
help create database;
使用help命令来获取
```

`information_schema`

```mysql
使用select 语句来进行获取帮助
select * from 表名;
```

### 生产应用案例

1. 查询数据库、表信息

   * `show databases; 查询数据库名称`

   * ```mysql
     use 库名
     show tables; 查询当前库中的表
     或者使用如下方式
     show tables from information_schema;
     或者
     show tables from 库名;
     使用模糊查询---注意：like后的字段需要匹配大小写
     show tables from information_schema like "INNO%";
     ```

   * 查询列信息

     ```mysql
     使用columns来进行查询表结构
     show columns from mysql.user;
     使用desc来进行查询表结构
     desc mysql.user;
     ```

2. 获取指定数据库状态信息

   * 查看所有数据库状态信息

     `show status\G`

   * 查询指定状态信息

     `show status like 'lock%';`

3. 获取数据库连接信息

   * `show full processlist;` 显示全部连接信息
   * `show processlist;`
   * 如果有非法连接信息可以使用`kill 编号` 将会话进行杀掉

4. 获取指定数据库配置信息

   * `show variables \G;`查询所有配置信息

   * 查询指定的配置信息

     `show variables like 'inn%'`

5. **查看表的建表语句**

   * `show create table 表名`;

6. 查看表的索引信息

   * `show index from city;`
   * `desc city;`

7. 查询某用户的权限信息

   * `show grants for 用户名`

8. 查看`binlog`事件信息

   * `show binary logs;`

   * 查看`binlog`当前正在使用的日志

     `show master status;`

   * 查看`binlog`日志的内容

     `show binlog enevts in '日志名称';`

9. 查询从库状态信息

   `show slave status \G`

10. 查看`InnoDB`引擎状态信息

    `show engine innodb status; `

11. 查看统计表的存储引擎

    `select table_schema,table_name,engine from information_schema.tables;`

    `select table_schema,table_name,engine from information_schema.tables where table_schema not in ('information_schema','sys','performance_schema','mysql');`

    将业务库中,所有的非InnoDB表查询出来

    `select table_schema,table_name,engine from information_schema.tables where table_schema not in ('information_schema','sys','performance_schema','mysql') and engine !='innodb';`

12. 统计表的类型信息

    * 使用table_type

    * `select table_schema,table_name,table_type engine from information_schema.tables where table_schema not in ('information_schema','sys','performance_schema','mysql');`

13. 统计表个数信息

    ```mysql
    select table_schema ,count(*) from information_schema.tables
    where table_schema not in ('information_schema','sys','performance_schema','mysql')
    group by table_schema;
    //查询所有库下的所有表
    select table_schema ,count(*) from information_schema.tables group by table_schema;
    //将库中的所有表名展示出来
    select table_schema ,count(*) ,group_concat(TABLE_NAME) from information_schema.tables
    group by table_schema;
    ```

14. 统计表的列信息

    * ```mysql
      use information_schema;
      desc COLUMNS;
      
      
      统计world数据库下的所有表中的列信息
      select table_name,table_schema,COLUMN_NAME from information_schema.COLUMNS
      where TABLE_SCHEMA='world';
      
      select table_name,table_schema,group_concat(COLUMN_NAME) from information_schema.COLUMNS
      where TABLE_SCHEMA='world' group by table_name;
      
      查询某个库中的单表字段
      desc world.city;
      ```

15. 统计数据库量信息

    ```
    select TABLE_SCHEMA,sum(TABLE_ROWS*AVG_ROW_LENGTH+INDEX_LENGTH)from information_schema.TABLES group by
    TABLE_SCHEMA;
    ```

16. ~~巧妙应用IS进行拼接语句~~

    ```mysql
    //转换引擎
    alter table sparrow.b engine =Innodb;
    
    查询当前非innodb引擎
    select table_schema,table_name,ENGINE from information_schema.TABLES where
    TABLE_SCHEMA not in ('information_schema','sys','performance_schema','mysql')
    and ENGINE !='innodb';
    
    
    select concat("alter table ",TABLE_SCHEMA,".",table_name,"ENGINE=innodb;")from information_schema.tables where table_schema not in ('information_schema','sys','performance_schema','mysql') and engine !='innodb';
    
    操作之前首先修改配置文件----默认安全策略不允许导出数据
    vim /etc/my.cfg
    在mysqld下添加该参数
    secure-file-priv=/tmp
    重启数据库
    /etc/init.d/mysqld restart
    
    //导出非innodb的文件---> 导入并执行source /tmp/alter.sql
    select concat("alter table ",TABLE_SCHEMA,".",table_name,"ENGINE=innodb;")
    from information_schema.tables
    where table_schema not in ('information_schema','sys','performance_schema','mysql') and engine !='innodb'
    into outfile '/tmp/alter.sql';
    ```



## mysql 事务

### 事务的使用以及其ACID特性

* * 

