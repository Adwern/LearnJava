# 安装mysql

安装brew

Homebrew 国内自动安装脚本
```c
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

$ brew install mysql@5.7

环境变量配置
```cmd
# 终端
echo 'export PATH="/opt/homebrew/opt/mysql@5.7/bin:$PATH"' >> ~/.zshrc
 
# 刷新 ~/.zshrc
source ~/.zshrc
```

初始化密码
```c
mysql_secure_installation
```

运行
```c
mysql -u root -p
```
```c
mysql.server start  # 启动mysql
 
mysql.server stop # 停止mysql
 
mysql.server restart # 重启mysql
```

# 使用Navicat
查询，新建查询

上访工具栏-工具-历史记录，可以看到sql语句
```sql
CREATE DATABASE `school` CHARACTER SET 'utf8' COLLATE 'utf8_general_ci'
```
右键表打开表，左下角➕号，增加数据，然后点勾，保存
```sql
cmd > show databases;  
use school  
show tables;  
describe student;  (desc)  
exit; --注释  
```
ctrl shift r运行本行

# MySQL数据管理

- DDL 定义
- DML 操作
- DQL 查询
- DCL 控制

```sql
USE `school`;
SELECT `age` FROM student;
```

unsigned:无符号的整数，也就是不能为负数  
zerofill:不足的位数用0填充  
自增:自动在上一条的记录基础上+1  

- id
- version
- is_delete
- gmt_create
- gmt_update

每个表都要存在以上几个字段（标准的话）

```sql
CREATE TABLE IF NOT EXISTS `student`(
	`id` INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
	`name` VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名',
	`pwd` VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
	`sex` VARCHAR(2) NOT NULL DEFAULT '女' COMMENT '性别',
	`birthday` DATETIME DEFAULT NULL COMMENT '出生日期',
	`address` VARCHAR(100) DEFAULT NULL COMMENT '家庭住址',
	`email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8;
-- 默认编码不支持中文

```


```sql
SHOW CREATE DATABASE school;//显示详细代码
SHOW CREATE TABLE student;
DESC student;//显示
```

数据引擎：
- MYISAM 节约空间，速度快
- INNODB 安全性高，可以事务处理，多表操作
- 两者文件分类目录不同
  

修改表：
```sql
ALTER TABLE teacher RENAME AS teacher1 /ADD age INT(11) /MODIFY age VARCHAR(11) /change age age1 INT(1) /DROP age1

DROP TABLE IF EXISTS teacher1
-- 要加上if exists！！否则报错
```

被引用的表，也就是被用作外键的表不能被先删除

方法一：  
在创建表的时候就设置外键

方法二：  
```sql
ALTER TABLE `student`
ADD CONSTRAINT `FK_gradeid` FOREIGN KEY(`gradeid`) REFERENCES `grade`(`gradeid`);
-- 没有分号。添加约束 约束名 作为外键的列，引用的是那个表的哪个列
-- FK_..是命名规范
```

## DML语言
数据操作语言

### INSERT



### UPDATE



### DELETE



### 外键

navicat在增加表的外键时，确保外键的表有对应的数据。

school中有一个gradeid外键，其中xiaoming数据数值为0，grade表中的gradeid需要有对应的值0。

# JDBC

结果集是链表？

## 数据库驱动

应用程序通过jdbc连接不同的数据库驱动，我们只关心jdbc的接口

## 第一个JDBC程序

导入java.sql，javax.sql，还需要一个数据库驱动的包

maven仓库中可以搜索到mysql，导入5.1.47

突然发现这个maven程序没设置好仓库

idea tips：com.shawn.study01创建完后不显示层级目录，在project窗口上方有个设置，取消勾选compactxxxx即可。

```java
package com.shawn.study01;
import java.sql.*;
public class JdbcFirstDemo {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        //加载驱动
        Class.forName("com.mysql.jdbc.Driver");
        //用户信息url
        String url = "jdbc:mysql://localhost:3306/school?useUnicode=true&characterEncoding=utf8&useSSL=true&useSSL=false";//useSLL=false!!
        String username = "root";
        String password = "password";
        //连接成功，返回数据库对象 Connection
        Connection connection = DriverManager.getConnection(url, username, password);
        //执行sql，返回执行sql的对象 Statement
        Statement statement = connection.createStatement();
        //用statement执行sql，可能存在结果，返回链表结果集Resultset
        String sql = "SELECT * FROM student";//Query查询
        ResultSet resultSet = statement.executeQuery(sql);//结果集，是个链表！
        while (resultSet.next()) {
            System.out.println("id"+resultSet.getObject("id"));
            System.out.println("id"+resultSet.getObject("name"));
            System.out.println("id"+resultSet.getObject("pwd"));
            System.out.println("id"+resultSet.getObject("sex"));
            System.out.println("id"+resultSet.getObject("birthday"));
            System.out.println("id"+resultSet.getObject("address"));
            System.out.println("id"+resultSet.getObject("email"));
        }
        //释放连接
        resultSet.close();
        statement.close();
        connection.close();
        //从后往前一个一个关闭
    }
}
```
mysql默认端口号3306  
oralce默认端口号1521
url格式："jdbc:mysql://主机地址：端口号/数据库名?参数一&二&三&四

```java
statement.executeQuery();//查询，返回结果集
statement.execute;
statement.executeUpdate();//更新，插入，删除，返回受影响行数有多少行
```

```java
resultSet.getObject();//用于在不知道类型的时候
resultSet.getString();
...
result.next();//移动到下一个
result.previous();//上一个
resultSet.beforeFirst();//最前面
resultSet.afterLast();//最后面
result.absolute(row);//移动到指定行
```

## statement
增删改查

## io流
将配置写入db.properties
然后新建一个工具类JdcbUtils写static用io将文件内容里的配置读取进来

## PreparedStatement
防止sql注入：
```java
login("'or '1=1","'or'1=1")//sql注入，漏洞，会输出所有的用户名和密码

static void login (String username,String password){
    String sql = "select * from where `name`= '"+ username +"' AND `password` ='"+password"'"; 
}
```

```java
String sql =  "insert into users(`id`,`NAME`,`PASSWORD`,`email`,`birthday`)values(?,?,?,?,?)";
st = conn.prepareStatement(sql);//预编译SQL，先写SQL，然后不执行
st.setInt(1,4);//id
st.setString(2,"qinjiang")
...
st.setDate(5,new java.sql.Date(new Date().getTime()))//new Date是Java util.Date的，然后用sql.Date转为数据库的Date

int i = st.executeUpdate();
```
先预编译，然后补问号，然后执行

# IDEA连接数据库
 类似navicat
 更改数据后，要点击上方的提交键





