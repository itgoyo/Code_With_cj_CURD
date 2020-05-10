### PostgreSQL 简介

#### PostgreSQL

`PostgreSQL` 是以加州大学伯克利分校计算机系开发的 POSTGRES，现在已经更名为 PostgreSQL，版本 4.2 为基础的**对象关系型数据库管理系统**（ORDBMS）。PostgreSQL 支持大部分 SQL 标准并且提供了许多其他现代特性：复杂查询、外键、触发器、视图、事务完整性、MVCC。同样，PostgreSQL 可以用许多方法扩展，比如， 通过增加新的数据类型、函数、操作符、聚集函数、索引。免费使用、修改、和分发 PostgreSQL，不管是私用、商用、还是学术研究使用。

##### psql

是 PostgreSQL 数据库的命令行交互工具。

##### pgAdmin

是 PostgreSQL 数据库的图形化管理工具。

### PostgreSQL 安装与卸载

#### 官网安装

在官网上下载安装包或者使用官网提供的 `Postgres.app`，这里就不详细介绍了，因为我们这里使用 `homebrew` 来安装。

#### homebrew 安装

```undefined
brew install postgresql

```

初始化：

```bash
initdb /usr/local/var/postgres

```

### 创建数据库和账户

#### 创建数据库和账户

mac 安装 PostgreSQL 后不会创建用户名数据库，执行命令：

```undefined
createdb

```

然后登录 PostgreSQL 控制台：

```undefined
psql

```

`psql` 连接数据库默认选用的是当前的系统用户

使用 \l 命令列出所有的数据库，看到已存在用户同名数据库、postgres 数据库，但是 postgres 数据库的所有者是当前用户，没有 postgres 用户。

然后我们来完成以下几件事：

一、创建 postgres 用户

```bash
CREATE USER postgres WITH PASSWORD 'XXXXXX';

```

二、删除默认生成的 postgres 数据库

```undefined
DROP DATABASE postgres;

```

三、创建属于 postgres 用户的 postgres 数据库

```undefined
CREATE DATABASE postgres OWNER postgres;

```

四、将数据库所有权限赋予 postgres 用户

```undefined
GRANT ALL PRIVILEGES ON DATABASE postgres to postgres;

```

五、给 postgres 用户添加创建数据库的属性

```undefined
ALTER ROLE postgres CREATEDB;

```

这样就可以使用 postgres 作为数据库的登录用户了，并可以使用该用户管理数据库

##### 登陆控制台指令

```css
psql -U [user] -d [database] -h [host] -p [port]

```

-U 指定用户，-d 指定数据库，-h 指定服务器，-p 指定端口
完整的登录命令，比如使用 postgres 用户登录

```undefined
psql -U postgres -d postgres

```

之前我们直接使用 psql 登录控制台，实际上使用的是缺省数据

```undefined
user：当前mac用户
database：用户同名数据库
主机：localhost
端口号：5432，postgresql的默认端口是5432

```

##### 常用控制台指令

```css
\password：设置当前登录用户的密码
\h：查看SQL命令的解释，比如\h select。
\?：查看psql命令列表。
\l：列出所有数据库。
\c [database_name]：连接其他数据库。
\d：列出当前数据库的所有表格。
\d [table_name]：列出某一张表格的结构。
\du：列出所有用户。
\e：打开文本编辑器。
\conninfo：列出当前数据库和连接的信息。
\password [user]: 修改用户密码
\q：退出

```

##### 使用 PostgreSQL

现在来简单的学习一下使用 PostgreSQL，以下命令都在 postgres=# 环境下
修改用户密码
之前我们用命令 CREATE USER postgres WITH PASSWORD 'XXXXXX'; 创建了 postgres 用户，现在我们来修改该用户的密码：

```bash
ALTER USER postgres WITH PASSWORD 'XXXXXX'

```

出现 ALTER ROLE, 代表修改角色成功

#### 创建和删除数据库用户

创建 user1 用户：`CREATE USER user1 WITH PASSWORD 'XXXX'`

查看数据库用户列表：`\du`

删除数据库用户：`drop user user1;`

#### 创建和删除数据库

创建数据库：`create database testdb;`

查看数据库列表：`\l`

删除数据库：`drop database db1;`

#### 创建和删除数据表

选择数据库：`\c DatabaseName`，比如 `\c testdb`

创建数据库表：`CREATE TABLE COMPANY( ID INT PRIMARY KEY NOT NULL, NAME TEXT NOT NULL, AGE INT NOT NULL, ADDRESS CHAR(50), SALARY REAL);`

删除数据库表： `drop table company;`

查看数据库信息：`\d`

查询数据：`select * from company`

#### 关于第三方连接本地数据库的问题

如何设置 PostgreSQL 允许被远程访问

```bash
/usr/local/var/postgres/postgresql.conf

```

1.  修改 postgresql.conf
    编辑或添加下面一行，使 PostgreSQL 可以接受来自任意 IP 的连接请求。

```bash
listen_addresses = '*'

```

1.  修改 pg_hba.conf
    pg_hba.conf，位置与 postgresql.conf 相同，虽然上面配置允许任意地址连接 PostgreSQL，但是这在 pg 中还不够，我们还需在 pg_hba.conf 中配置服务端允许的认证方式。任意编辑器打开该文件，编辑或添加下面一行。

```bash
# TYPE  DATABASE  USER  CIDR-ADDRESS  METHOD
host  all  all 0.0.0.0/0 md5

```

默认 pg 只允许本机通过密码认证登录，修改为上面内容后即可以对任意 IP 访问进行密码验证。对照上面的注释可以很容易搞明白每列的含义，具体的支持项可以查阅文末参考引用。

完成上两项配置后执行 `sudo service postgresql restart` 重启 PostgreSQL 服务后，允许外网访问的配置就算生效了。

[参考](https://zhuxin.tech/2017/11/24/Mac%E4%B8%8A%E5%AE%89%E8%A3%85%E5%92%8C%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8PostgreSQL/)

作者：不_一
链接：https://www.jianshu.com/p/9e91aa8782da
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。