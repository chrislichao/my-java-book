# Vue
#### Vue全家桶
- vue-router
- vuex
- axios
- vue-cli

##### Vue脚手架（vue-cli）
- 前置条件
  - 安装Nodejs（版本是8.0.0以上）
  - 全局安装cnpm
  - 全局安装webpack `cnpm install webpack -g`
  - 全局安装webpack-cli `cnpm install webpack webpack-cli -g`
  - 全局安装vue-cli `cnpm install vue-cli -g`
  - 全局卸载已安装的vue-cli `cnpm uninstall vue-cli -g`
  - 全局安装新版vue-cli `cnpm install -g @vue/cli`
  - 全局安装指定版本的vue-cli `cnpm install -g @vue/cli@3.11.0`

##### Vue-cli生成的项目的目录结构
- 项目名称
  - dist （`npm run build`指令编译打包好的待发布的项目文件）
  - node_modules（存放项目所需要的第三方依赖）
  - public（公用文件）
  - src（资源文件夹，用来新建组件，编写业务逻辑）
    - main.js（整个项目的入口文件）
    - assets（用来存放图片，图标，字体等）
    - components（用来存放公共组件）
    - views（用来存放页面）
  - package-lock.json（防止多人协助时版本不一致）
  - package.json（项目包依赖配置文件）
  - vue.config.js（配置文件，项目启动时，读取该配置文件，覆盖原有的配置）
```js
module.exports = {
	devServer: {
		open: true,
		host: '127.0.0.1',
		port: 8081
	}
}
```
[参考文件](https://cli.vuejs.org/zh/)

##### Vue-cli常用命令
- vue create（命令行创建项目）
- vue ui （ui界面创建项目）
- vue add （为已有项目新增一个插件）


##### Vue2.9.6实战开发
- 开发工具：Webstorm
- 项目名称：vue-demo
- 命令行进入项目路径下，执行指令：
```
vue init webpack

? Generate project in current directory? Yes
? Project name vue-demo
? Project description A Vue.js project
? Author chrislichao <799317504@qq.com>
? Vue build standalone
? Install vue-router? Yes
? Use ESLint to lint your code? No
? Set up unit tests No
? Setup e2e tests with Nightwatch? No
? Should we run `npm install` for you after the project has been created? (recommended) npm
```

- 完成后，执行`cnpm install`将依赖的包下到工程目录中
- 完成后，执行`npm run dev`测试是否成功：  Your application is running here: http://localhost:8080
- 访问：http://localhost:8080

- [相关教程](https://jspang.com/detailed?id=28)

- 文件介绍：
  - components：组件
  - router：路由
- 挑选图标：
  - [阿里图标库](https://www.iconfont.cn/)
  - 下载或者在线显示
- 使用element组件
  - [官方网站](https://element.eleme.cn/#/zh-CN)
  - 项目中安装element-ui
  ```
  cnpm install element-ui --save
  ```

- 使用axios从远程读取数据
  - 安装Axios
  ```
  cnpm install axios --save
  ```
  
  - 发生POST请求
  ```javascript
  let postParam = {
      "param": {
          "startPage": 1,
          "pageSize": 20
      }
  };
  axios.post('http://localhost:9000/user/getUserVoPageList', postParam)
       .then(response => {
           if (response.status == 200 && response.data.success {
               this.oftenGoods = response.data.datas.list;
           }
       })
       .catch(error => {
       });
  ```
  
  - 项目打包和上线
    - 1，将绝对路径改为相对路径
      - config/index.js中有一个build属性，里面是打包的基本配置
      - 可以修改打包目录，打包文件名
      - 一定要把绝对路径改为相对路径：`assetsPublicPath:'./'`
    - 2，命令行中使用`npm run build`进行打包