# sql注入
>_Belonging to: data_storage_

>_`sql` `injection` `数据库注入`_

>_Written date: 2020.06.01_

>_All knowledge is for learning use only, not for commercial use, and the author reserves all rights._

### 一、含义

攻击者通过拼接sql语句的方式，执行了原sql查询之外的操作，对他们没有权限的数据进行增删改查或对数据库进行非法操作。

### 二、危害

1. 用户敏感数据泄露
2. 数据被更改或删除
3. 破坏server基础架构，Dos攻击等

### 三、注入方式

#### 1. 使用注释符--

原查询语句：SELECT * FROM users WHERE user_name = 'Admin' AND password = '1234'

用户输入：user_name = Admin' --

语句变为：SELECT * FROM users WHERE user_name = 'Admin' --' AND password = '1234'

注释符后面部分被注释掉，无需密码即可登录

用户输入：user_name = Admin' OR 1 = 1 --

语句变为：SELECT * FROM users WHERE user_name = 'Admin' OR 1 = 1 --' AND password = '1234'

WHERE语句中条件恒为真，可以获取到所有用户的信息

注：这只是举例，真实场景下，涉及隐私数据的查询不限制条数，而且写个SELECT * 的话，看会不会被炒🦑。

#### 2. 使用UNION

' UNION SELECT username, password FROM users--

这样可以拼接去查另一张表，获取非法信息。

#### 3. 测试数据库版本，表结构等信息

SELECT * FROM v$version

SELECT * FROM information_schema.tables

获取到版本信息后，可以采取针对性策略去注入。

### 四、如何预防

#### 1. 数据库层面

保护好数据库信息，不要泄露，包括但不限于：

  a. 数据库所在server的ip地址和端口号
  
  b. 数据库认证信息（用户名和密码或其他认证方式）
  
  c. 数据库的database结构，包括库名、表名、表结构、字段信息等

#### 2. 服务层面
  
  a. 不要拿到前端传来的字段后，直接拼接sql语句去操作数据库。
  
  b. 采用参数化查询的方式去设置sql语句，从而确保参数在设置时进行了类型校验和转换，从根本上避免被注释符和关键字的方式注入。
  
  c. sql查询失败后要有合理的逻辑结束服务，避免程序走到未知的错误上，暴露不应暴露的信息。
