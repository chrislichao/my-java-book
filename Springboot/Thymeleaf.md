# Thymeleaf

##### 1，基础语法 
- 变量表达式`${}`
  - 使用方法：直接使用`th:xx = "${}"`获取对象属性
```html
<input id="username" name="username" th:value="${user.username}" />
```
- 选择变量表达式`*{}`
  - 使用方法：首先通过`th:object`获取对象，然后使用`th:xx = "*{}"`获取对象属性
```html
<form id="userForm" th:object="${user}">
	<input id="username" name="username" th:value="*{username}"/>
</form>
```
- 链接表达式`@{}`
  - 使用方法：通过链接表达式`@{}`直接拿到应用路径，然后拼接静态资源路径
```html
<!-- 路径:resources/static/common/jquery/jquery.js -->
<script th:src="@{/common/jquery/jquery.js}"></script>
<link th:href="@{/common/bootstrap/css/bootstrap.css}" rel="stylesheet" type="text/css">
```
- 片段表达式`~{}`
  - `~{viewName}`：表示引入完整页面
  - `~{viewName::selector}`：表示在指定页面寻找片段，其中selector可为片段名，jquery选择器等
  - `~{::selector}`：表示在当前页面寻找
  - 使用方法：首先通过`th:fragment`定制片段，然后通过`th:replace`填写片段路径和片段名
```html
<!-- /views/common/head.html -->
<head th:fragment="static">
	<script th:src="@{/webjars/jquery/3.3.1/jquery.js}" />
</head>

<!-- /views/your.html -->
<div th:replace="~{common/head::static}"></div>
<div th:replace="common/head::static"></div>
```
- 消息表达式`#{}`
```html
<title th:text="#{user.title}"></title>
```
- 其他表达式
```html
<input name="name" th:value="${'I am'+(user.name!=null?user.name:'chris')}"/>
```

##### 2，进阶
- 七大基础对象：
  - `${#ctx}`：上下文对象，用于获取其他内置对象
  - `${#vars}`：上下文变量
  - `${#locale}`：上下文区域设置
  - `${#request}`：HttpServletRequest对象
  - `${#response}`：HttpServletResponse对象
  - `${#session}`：HttpSession对象
  - `${#servletContext}`：ServletContext对象
- 常用工具类：
  - `#strings`：字符串工具类
  - `#lists`：List工具类
  - `#arrays`：数组工具类
  - `#sets`：Set工具类 
  - `#maps`：Map工具类
  - `#objects`：一般对象类，通常用来判断非空
  - `#bools`：常用的布尔方法
  - `#execInfo`：获取页面模板的处理信息
  - `#messages`：在变量表达式中获取外部消息的方法，与使用#{}语法获取的方法相同
  - `#uris`：转义部分URL/URI的方法
  - `#conversions`：用于执行已配置的转换服务的方法
  - `#dates`：时间操作和时间格式化等
  - `#calendars`：用于更复杂时间的格式化
  - `#numbers`：格式化数字对象的方法
  - `#aggregates`：在数组或集合上创建聚合的方法
  - `#ids`：处理可能重复的id属性的方法
- 常用例子：
```html
<!-- 循环 -->
<div th:each="user:${userList}">
	名称：<input th:value="${user.username}"/>
</div>
<div th:each="user,stat:${userList}" th:class="${stat.even}?'even':'odd'">
	下标：<input th:value="${stat.index}">
	序号：<input th:value="${stat.count}">
	名称：<input th:value="${user.username}"/>
</div>
<div th:each="user:${userList}" th:class="${userStat.even}?'even':'odd'">
	下标：<input th:value="${userStat.index}">
	序号：<input th:value="${userStat.count}">
	名称：<input th:value="${user.username}"/>
</div>

<!-- 条件判断 -->
<div th:if="${userList}">
	<div>存在</div>
</div>
<div th:unless="${userList}">
	<div>不存在</div>
</div>

<!-- 日期格式化 -->
<input type="text" th:value="${#dates.format(user.createTime,'yyyy-MM-dd HH:mm:ss')}">

<!-- 内联写法 -->
<script th:inline="javascript">
	var user = [[${user}]];
	var APP_PATH = [[${#request.getContextPath()}]];
	var LANG_COUNTRY = [[${#locale.getLanguage()+'_'+#locale.getCountry()}]];
</script>
```
##### 3，Springboot整合
- 引入依赖
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```
- 修改application.yml配置文件
```
spring:
  thymeleaf:
    cache: false #关闭缓存
    prefix: classpath:/views/ #调整页面路径
```
- controller编写
```java
@RestController
public class UserController {
    private List<User> userList = new ArrayList<>();
    {
        userList.add(new User("1", "socks", "123456", new Date()));
        userList.add(new User("2", "admin", "111111", new Date()));
        userList.add(new User("3", "jacks", "222222", null));
    }
    @GetMapping("/")
    public ModelAndView index() {
        return new ModelAndView("user/user", "userList", userList);
    }
}

public class User {
    private String id;
    private String username;
    private String password;
    private Date createTime;
    //请读者自行补充 构造器和 get/set方法..
}
```
- 公共页面`src/main/resources/views/common/head.html`
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<!--声明static为页面片段名称-->
<head th:fragment="static">
    <link th:href="@{/webjars/bootstrap/css/bootstrap.css}" rel="stylesheet" type="text/css"/>
    <script th:src="@{/webjars/jquery/jquery.js}"></script>
</head>
</html>
```
- 用户页面
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8"/>
    <title th:text="用户信息">User</title>
    <!--默认拼接前缀路径,开头请勿再添加斜杠,防止部署运行报错！-->
    <script th:replace="common/head::static"></script>
</head>
<body>

<div th:each="user,userStat:${userList}" th:class="${userStat.even}?'even':'odd'">
    序号：<input type="text" th:value="${userStat.count}"/>
    账号：<input type="text" th:value="${user.username}"/>
    密码：<input type="password" th:value="${user.password}"/>
    时间：<input type="text" th:value="${user.createTime}"/>
    时间：<input type="text" th:value="${#dates.format(user.createTime,'yyyy-MM-dd HH:mm:ss')}"/>
</div>

<script th:inline="javascript">
    //通过内联表达式获取用户信息
    var userList = [[${userList}]];
    console.log(userList)
</script>
</body>
</html>
```