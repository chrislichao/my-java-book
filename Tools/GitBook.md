# GitBook

##### 1，搭建GitBook环境
- 1-1，安装Node.js
  - [下载地址](https://nodejs.org/en/download/)
  - 现在安装Node.js都会默认安装npm（node包管理工具）
- 1-2，安装GitBook

```
npm install -g gitbook-cli
```

##### 2，GitBook使用
- 2-1，推荐 GitBook + Typora + Git
  - [Typora下载地址](https://typora.io/)
  - [Git下载地址](https://git-scm.com/downloads)
- 2.2，创建`my-java-book`文件夹，进入到本文件夹中，然后执行```gitbook init```命令进行初始化

- 2.3，执行完成后会多出两个文件
  - README.md  书籍的介绍
  - SUMMARY.md  书籍的目录
- 2.4，启动Typora来编辑这两个文件
  - 修改SUMMARY.md文件内容

```
# 目录
* [前言](README.md)
* [工具使用](Tools/README.md)
  * [GitBook](Tools/GitBook.md)
```

- 2.5，回到命令行，在`my-java-book`文件夹中再次执行`gitbook init`命令，GitBook会自动创建相关文件和文件夹
- 2.6，执行`gitbook serve`预览本书
- 2.7，完成后，访问`http://localhost:4000`来预览本书

##### 3，常用命令
- `gitbook build` 构建书籍，默认输出到`_book`目录
- `gitbook build [书籍路径] [输出路径]` 构建书籍，指定目录
- `gitbook serve` 预览书籍
- `gitbook serve --port 2333` 预览书籍，并指定端口
- `gitbook pdf ./ ./my-java-book.pdf` 生成PDF格式的电子书
- `gitbook epub ./ ./my-java-book.epub` 生成epub格式的电子书
- `gitbook mobi ./ ./my-java-book.mobi`  生成mobi格式的电子书

##### 4，关联GitHub
- 4.1 登录GitHub，创建远程仓库`my-java-book`
- 4.2 本地仓库初始化，进入本地`my-java-book`文件夹
```
git init
```
- 4.3 关联远程仓库并更新
```
git remote add origin https://github.com/chrislichao/my-java-book.git
git pull https://github.com/chrislichao/my-java-book master
```
- 4.4 提交本地文件
```
git add .
git commit -m '导入GitBook'
git push origin master
```