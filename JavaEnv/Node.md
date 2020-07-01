# Node

##### 1，安装Nodejs

- 存放路径：C:\MySoft\nodejs

##### 2，改变原有的环境变量

- 配置npm的全局模块存放路径，cache的路径
```
npm config set prefix "C:\MySoft\nodejs\node_global"
npm config set cache "C:\MySoft\nodejs\node_cache"
```

- 配置NODE_HOME：C:\MySoft\nodejs
- 配置NODE_PATH：C:\MySoft\nodejs\node_global\node_modules
- path：path + %NODE_HOME%

##### 3，安装组件
- 安装express
```
# -g”表示安装到global目录下
npm install express -g
```

- 重启cmd，验证express是否安装成功
```
node
> require('express')
```

- 安装cnpm
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

- 修改path路径：path = path + %NODE_HOME%\node_global
