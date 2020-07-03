# Vue

##### Vue实战开发
- 开发工具：Webstorm
- 项目名称：vue-demo
- 前置操作：
  - 安装cnpm
  - 安装vue脚手架：`cnpm install vue-cli -g`
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

- [相关教程]()

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