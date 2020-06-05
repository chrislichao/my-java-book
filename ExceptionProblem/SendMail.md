# 发邮件异常
- **JDK版本**：1.7
- **使用框架**：Spring，Spring MVC，MyBatis
- **运行容器**：Tomcat
- **问题描述**：相同的代码，通过本地main方法发送邮件成功，但是通过Tomcat启动后接口调用，代码不报错，但是邮件无法发送，登录发件方邮箱中查看，发送失败的邮件，被标记为：*无主题*，*内容乱码*
- **问题原因**：*mail相关jar包冲突*
- 解决方案：
  - 引入标准的javax.mail
  
    ```
    <dependency>
          <groupId>javax.mail</groupId>
          <artifactId>mail</artifactId>
          <version>1.4.7</version>
    </dependency>
    ```
  
  - 删除冲突的包
    - org.apache.openjb
    - geronimo-javamail_1.4_spec-1.3.jar

