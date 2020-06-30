# MySQL

##### 1，下载 MySQL
- [下载地址](https://dev.mysql.com/downloads/mysql/)
- 选择 mysql-5.7.29-winx64.zip
- 下载后存放在 C:\ChrisLi\MyTool

##### 2，安装
- 直接解压zip文件
- 存放地址：C:\ChrisLi\MyTool\mysql-5.7.29-winx64

##### 3，配置环境变量
- 我的电脑->属性->高级->环境变量
- 新增系统变量：MYSQL_HOME=C:\ChrisLi\MyTool\mysql-5.7.29-winx64
- 修改Path环境变量：Path = Path + %MYSQL_HOME%/bin;

##### 4，配置数据库
- 在C:\ChrisLi\MyTool\mysql-5.7.29-winx64目录下，新建data文件夹，用来存放数据库数据
- 在C:\ChrisLi\MyTool\mysql-5.7.29-winx64目录下，新增配置文件mysql.ini，内容如下：
```
[mysql]

# 设置mysql客户端默认字符集
default-character-set=utf8 

[mysqld]

#设置3306端口
port = 3306 

# 设置mysql的安装目录
basedir=C:\ChrisLi\MyTool\mysql-5.7.29-winx64

# 设置mysql数据库的数据的存放目录
datadir=C:\ChrisLi\MyTool\mysql-5.7.29-winx64\data

# 允许最大连接数
max_connections=200

# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8

# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```
##### 5，安装并启动数据库
- 以管理员的身份打开cmd，无需进入安装目录，输入以下命令
```
C:\WINDOWS\system32>mysqld --initialize-insecure --user=mysql
C:\WINDOWS\system32>mysqld install
```

- 安装成功会提示：Service successfully installed
- 命令行使用`net start mysql`启动服务，启动成功会提示：MySQL 服务已经启动成功

##### 6，登录并修改密码
- 命令行使用`mysql -u root -p`登录（第一次登录没有密码，直接回车过）
- 如果登录后想退出，输入exit，回车即可
- 命令行使用`mysqladmin -u root -p password`修改密码（输入第一个密码处直接回车，第二个输入密码处，输入新密码）

##### 7，设置开机自启
- 在允许中输入 Services.msc
- 找到MySQL右键属性，设置为自动重启
