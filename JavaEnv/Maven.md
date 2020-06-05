# Maven

##### 1，配置Maven开发环境
- 示例版本号：3.2.1
- 1-1，下载压缩包
  - [官方下载地址](http://maven.apache.org/download.cgi)
  - 直接下载`-bin.zip`结尾的文件
- 1-2，解压并存放
  - 本地存放路径为：`C:\ChrisLi\MyJava\JavaEnv\apache-maven-3.2.1`
- 1-3，配置MVN_HOME系统环境变量
  - 环境变量新增`MVN_HOME`:`C:\ChrisLi\MyJava\JavaEnv\apache-maven-3.2.1`
- 1-4，配置path系统环境变量
  - path环境变量后面追加`%MVN_HOME%\bin;`
- 1-5，版本升级
  - 后续如果进行版本升级，直接把`MVN_HOME`路径指向新的MAVEN地址路径
- 1-6，检查是否安装成功
  - `cmd` 调出命令行工具
  - `mvn -v` 查看当前MAVEN版本号
- 1-7，配置文件修改
  - 进入`C:\ChrisLi\MyJava\JavaEnv\apache-maven-3.2.1\conf`文件夹，找到`setting.xml`文件
  - 修改`localRepository`属性
    - `<localRepository>C:\ChrisLi\MyJava\JavaEnv\apache-maven-3.2.1/repository</localRepository>`
  - 配置`mirror`属性
```
<mirror>
  <id>alimaven</id>
  <name>aliyun maven</name>
  <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
  <mirrorOf>central</mirrorOf>        
</mirror>
```
---
