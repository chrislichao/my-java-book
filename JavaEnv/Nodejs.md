# Nodejs
##### 前言
- 浏览器给服务器发送请求，服务器收到请求，搜寻被请求的资源，可能还会查询一下数据库，最后把想要的结果回传给浏览器。传统的WEB服务器中，每个请求都会让服务器创建一个新的连接来处理它。后来有了ajax技术，只需每次请求部分需要刷新的数据即可，HTTP请求不是持续的连接，你请求一次，服务器就响应一次。随着用户连接数增长到一定规模，服务器的内存将被耗光，解决方案就是**非阻塞**和**事件驱动**。
- 可以把非阻塞的服务器想象成一个loop循环，这个loop会一直跑下去，一个请求来了，这个loop就接到这个请求，把这个请求传给其他进程（比如查询数据库），然后响应一个回调，之后这个loop接着跑，接其他的请求，这样服务器就不会像之前那样傻等着数据库返回结果了。如果其他进程（比如查询数据库）有结果返回，loop就把结果传回给用户的浏览器，接着继续跑。服务器只在用户那边有事件发生时才相应，这就是**事件驱动**。

- 总结：Nodejs是一个基于V8引擎的JavaScript运行环境，能够使javascript脱离浏览器运行，它可以让JavaScript代码运行在浏览器以外的电脑上，从而实现使用JavaScript代码操作电脑上的任何文件。

##### 1，对Nodejs的认识
- Nodejs使用C++开发，将执行JavaScript的V8引擎单独拿出来，让电脑上安装了Nodejs就能让电脑直接运行JavaScript。
- Nodejs是一个基于V8引擎的JavaScript运行环境（运行时）。
- 浏览器中运行的JavaScript代码，浏览器的V8引擎是运行环境，Dom和Bom是浏览器内置的用来操作浏览器的方法api。
- Nodejs中运行的JavaScript代码，Nodejs的V8引擎是运行环境，Nodejs内置了Api用来操作电脑上的文件等功能，如：fs，path，http等，node里面这些api也叫模块，另外还有第三方库，就是插件，在Nodejs中叫做包，如：express等。
- Nodejs的版本，LTS是正式版，Current是测试版；

##### 2，下载并安装Nodejs

- [下载地址](http://nodejs.cn/download/)

- 存放路径：C:\MySoft\nodejs

##### 3，改变原有的环境变量

- 配置npm的全局模块存放路径，cache的路径
```shell
npm config set prefix "C:\MySoft\nodejs\node_global"
npm config set cache "C:\MySoft\nodejs\node_cache"
```

- 配置NODE_HOME：C:\MySoft\nodejs
- 配置NODE_PATH：C:\MySoft\nodejs\node_global\node_modules
- path：path + %NODE_HOME%

##### 4，运行第一个js代码
- 编写js文件（C:\ChrisLi\HelloNodejs.js）
```js
console.log("Hello World");
```
- 调出命令行
```shell
cd C:\ChrisLi
node HelloNode.js
```

##### 5，npm介绍和使用
- 在Nodejs中，如果需要引入一个框架（如：express），不是直接点击下载，而是使用npm这个包管理工具去下载，启动和运行时都会使用它。用JavaScript（运行在Nodejs上）写的npm，全称是Node Package Manager，目的就是为了集中管理代码包，Nodejs中内置了npm，安装好Nodejs后可以通过`npm -v`指令查看npm版本。
- npm[官网](https://npmjs.com)在国外，如果要下载包，速度特别慢，所以我们安装淘宝镜像源，安装cnpm
```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
# 使用
cnpm install xxx
```
##### 6，安装组件
- 打开项目根目录，打开终端，输入 `npm install 包名`即可安装第三方包
- 常用命令
  - 安装包：`npm install 包名`
  - 删除包：`npm uninstall 包名`
  - 初始化一个记录包的package.json文件：`npm init`
  - 一次性安装所有package.json文件中的包：`npm install`
- package.json类似于Maven中的pom.xml文件
- 安装express
```shell
# -g”表示安装到global目录（C:\MySoft\nodejs\node_global\node_modules）下
cnpm install express -g
```
- 重启cmd，验证express是否安装成功
```shell
node
> require('express')
```
- 修改path路径：path = path + %NODE_HOME%\node_global
##### 7，Nodejs + Express + Mysql
- 新建项目文件夹（C:\ChrisLi\MyJava\JavaIde\Nodejs_Workspace\node-mysql）
- 初始化项目配置文件 package.json
```shell
npm init
# 输入项目名称和作者
package name: node-mysql
author: chrislichao

# 一路回车，生成好package.json文件

# 安装cors依赖包
cnpm install cors -g
```
- 查看生成的package.json文件
```json
{
  "name": "node-mysql",
  "version": "1.0.0",
  "description": "node express mysql",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "chrislichao",
  "license": "ISC"
}
```
- 新建一个api.js文件用来写接口代码，内容如下：
```js
// 导入express
const express = require('express');
// 调用express方法实例化一个服务器
const app = express();
// 导入cors
const cors = require('cors');
// 使用cors支持跨域
app.use(cors());
// 调用express的listen()方法开启服务器，端口为9001
app.listen(9001, function() {
	console.log('服务器启动完成！');
});
// 处理客户端GET请求
app.get('/banner', function(req, res) {
	res.send([
		'https://1.jsp',
		'https://2.jsp',
		'https://3.jsp'
	]);
});
```