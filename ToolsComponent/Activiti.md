#####  1，工作流的概念
- 工作流：业务过程的部分或者整体在计算机应用环境下的自动化，使在多个参与者之间按照某种预定义的规则传递文档，信息或任务的过程自动进行，从而实现某个预期的业务目标，或促使某个目标实现。
- 工作流引擎：ProcessEngine对象，负责生成流程运行时的各种实例和数据，监控和管理流程的运行。
- 生活中常用的工作流：请假，报销，申请转户口，购物等。

---
##### 2，数据库介绍
- 简介
  - ACT_RE_XX：RE表示respository，这个前缀的表包含流程定义和流程静态资源，如图片，规则等；
  - ACT_RU_XX：RU表示runtime，这个前缀的表包含正在运行中的流程实例，任务，变量，异步任务等数据，流程结束后会删除这些数据，提高运行效率；
  - ACT_ID_XX：ID表示identity，这个前缀的表包含身份信息，如用户，分组等等；
  - ACT_HI_XX：HI表示history，这个前缀的表包含历史流程实例，变量，任务等待；
  - ACT_GE_XX：GE表示general，这个前缀的表用于不同场景下存放资源文件；
- 资源库流程规则表
  - ACT_RE_DEPLOYMENT：部署信息表
  - ACT_RE_MODEL：流程设计模型部署表
  - ACT_RE_PROCDEF：流程定义数据表
- 运行时数据库表
  - ACT_RU_EXECUTION：运行时流程执行实例表
  - ACT_RU_IDENTITYLINK：运行时流程人员表，主要存储任务节点与参与者的相关信息
  - ACT_RU_TASK：运行时任务节点表
  - ACT_RU_VARIABLE：运行时流程变量数据表
- 历史数据库表
  - ACT_HI_ACTINST：历史节点表
  - ACT_HI_ATTACHMENT：历史附件表
  - ACT_HI_COMMENT：历史意见表
  - ACT_HI_IDENTITYLINK：历史流程人员表
  - ACT_HI_DETAIL：历史详情表，提供历史变量的查询
  - ACT_HI_PROCINST：历史流程实例表
- 组织机构表
  - ACT_ID_GROUP：用户组信息表
  - ACT_ID_INFO：用户扩展信息表
  - ACT_ID_MEMBERSHIP：用户与用户组对应信息表
  - ACT_ID_USER：用户信息表
- 通用数据表
  - ACT_GE_BYTEARRAY：二进制数据表
  - ACT_GE_PROPERTY：属性数据表存储整个流程引擎级别的数据，初始化表结构时会默认插入三条数据

---
##### 3，核心类和相关API
- ProcessEngine
  - Activiti中最核心的类，其他类都是由他而来
  - RepositoryService
    - 流程图的部署，修改，删除
    - 相关数据库表
      - ACT_GE_BYTEARRAY
      - ACT_RE_DEPLOYMENT
      - ACT_RE_MODEL
      - ACT_RE_PROCDEF
  - RuntimeService，TaskService
    - 流程的运行
    - 相关数据库表
      - ACT_RU_XX
  - HistoryService
    - 历史数据操作
    - 相关数据库表
      - ACT_HI_XX
  - IdentityService
    - 对工作流用户管理的表
    - 相关数据库表
      - ACT_ID_XX
  - ManagementService
    - 管理器
  - ProcessDefinition
    - 流程定义类，可以从这里获得资源文件等，当流程图被部署之后，查询出来的数据就是流程定义的数据
    - 相关数据库表
      - ACT_RU_EXECUTION
  - ProcessInstance
    - 流程定义的执行实例，一个流程实例包括了所有的运行节点，流程实例表示一个流程从开始到结束的最大的流程分支，即一个流程中流程实例只有一个
    - 如果是单例流程，执行对象ID就是流程实例ID
    - 如果一个流程有分支和聚合，那么执行对象ID和流程实例ID就不同
    - 一个流程中，流程实例只有一个，执行对象可以存在多个
  - Execution
    - 执行对象，用来描述流程执行的每一个节点，没有并发的情况下，Execution等同于ProcessInstance，流程按照流程定义的规则执行一次的过程，就可以表示执行对象Execution
    - 相关数据库表
      - ACT_RU_EXECUTION：正在执行的信息
      - ACT_HI_PROCINST：已执行完的历史流程实例信息
      - ACT_HI_ACTINST：存放历史所有完成的活动
  - TaskInstance
    - 任务实例
    - 相关数据库表
      - ACT_RU_TASK：正在执行的任务信息
      - ACT_HI_TASKINST：已经执行完的历史任务信息

---
##### 4，相关代码
```java
// 得到流程引擎
ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();

// 得到流程部署Service
RepositoryService repositoryService = processEngine.getRepositoryService();

// 部署流程定义1
Deployment deploy = repositoryService.createDeployment()
.name("请假流程001")
.addClasspathResource("HelloWorld.bpmn")
.addClasspathResource("HelloWorld.png").deploy();

// 部署流程定义2:流程图的文件必须是.zip结尾
// 修改流程图后重新部署，只要key不变，它的版本号会+1
InputStream stream = this.getClass().getResourceAsStream("/HelloWorld.zip");
ZipInputStream zipInputStream = new ZipInputStream(stream);
Deployment deploy = repositoryService.createDeployment()
.name("请假流程001")
.addZipInputStream(zipInputStream).deploy();

// 查询流程部署
repositoryService.createDeploymentQuery().deploymentId(id).orderByDeploymentId().asc().list();
repositoryService.createDeploymentQuery().deploymentName(name).listPage(firstResult, maxResults);
repositoryService.createDeploymentQuery().deploymentNameLike(nameLike).singleResult();
repositoryService.createDeploymentQuery().deploymentNameLike(nameLike).count();

// 删除流程定义
repositoryService.deleteDeployment(deploymentId);
repositoryService.deleteDeployment(deploymentId, true);
repositoryService.deleteDeploymentCascade(deploymentId);

// 查询流程图
InputStream inputStream = repositoryService.getProcessDiagram(processDefinitionId);
File file = new File("c:/process.png");
try{
	BufferedOutputStream outputStream = new BufferedOutputStream(new FileOutputStream(file));
	int len = 0;
	byte[] b = new byte[1024];
	while((len = inputStream.read(b)) != -1) {
		outputStream.write(b, 0, len);
		outputStream.flush();
	}
	outputStream.close();
	inputStream.close();
} catch (Exception e) {

}

// 启动流程
runtimeService.startProcessInstanceById(processDefinitionId);
runtimeService.startProcessInstanceById(processDefinitionId, variables);
runtimeService.startProcessInstanceById(processDefinitionId, businessKey);
runtimeService.startProcessInstanceById(processDefinitionId, businessKey, variables);

// 查询个人任务
taskService.createTaskQuery().taskAssignee(userId).processDefinitionId(processDefinitionId).list();

// 办理任务
taskService.complete(taskId);
taskService.complete(taskId, variables);

// 查询历史任务
historyService.createHistoricTaskInstanceQuery().list();
```
##### 5，重要术语
- 流程变量：流程变量在整个工作流中扮演很重要的角色，如请假流程中的请假天数，请假原因等参数都为流程变量的范围，流程变量的作用域只对应一个流程实例，每个流程实例的流程变量互不影响，流程实例结束后，流程变量还保存在数据库中。
  - 存在的形式：key + value
  - 支持的数据类型：string，short，int，long，double，boolean，binary，date，serializable（存放自定义对象）
  - setVariable和setVariableLocal的区别：
    - setVariable：如果流程变量名称相同，后一次的值将覆盖前一次的值
    - setVariableLocal：针对活动节点来设置流程变量，两个活动节点设置同一个流程变量，不会覆盖，而且会有TASK_ID字段赋值
- 连线【SequenceFlow】
  - 场景：员工的需求申请，部门经理审批时，点击不同的按钮决定不同的走向，重要，需要总经理审批，不重要则直接结束流程
  - 在sequenceFlow上增加属性：condition="${outcome=='重要'}"，condition="${outcome=='不重要'}"
- 排他网关【ExclusiveGateway】
  - 可以理解为java的if - else if - else ，使用流程变量决定流程下一步要选择的路径，也称异或网关，基于数据的排他网关，用于为流程中的决策建模
  - 场景：费用报销申请，报销费用低于500财务审批，报销费用大于等于500小于1000部门经理审批，报销费用大于等于1000总经理审批
  - 使用排他网关，并连接三条出口线，分别在sequenceFlow上增加属性：condition="$(money<500)"，condition="$(money>=500&&money<1000)"，condition="$(money>=1000)"
- 并行网关【ParallelGateway】
  - 场景：购物流程，买家付款 - 卖家收款 和 卖家发货 - 卖家收货 是两个并行流程
  - 并行网关分开后最终还要汇合，并行网关进入和外出都是使用相同的节点标识
  - 一个流程中，只有一个流程实例，但是可能有多个执行实例
  - 如果同一个并行网关有多个进入和多个外出顺序流，则它可以同时具有分支和汇聚功能
  - 并行网关不会解析条件，即使定义了条件也会被忽略
- 接收任务【ReceiveTask】
  - 接受任务是一个简单任务，它会等待对应消息到达，当流程达到接受任务，流程状态会保存到数据库中，在任务创建后，意味着流程进入等待状态，直到引擎接收了一个特定的消息，才会触发流程穿过接收任务继续执行
  - 场景：每天统计报表，发短信给总经理
  ```java
  runtimeService.setVariable(executionId, "当前销售额", 10000);
  // 向后执行一步，如果流程处于等待状态，流程继续执行
  runtimeService.signal(executionId);
  ```
- 个人任务
  - 指定处理人的三种方式：
    - 直接写死办理人
    - 通过流程变量，节点向下走时动态指定办理人
    - 使用任务监听器（taskListener）
- 组任务
  - 不是设置办理人，而是设置参与者和候选者
  - 任务需要先被候选者拾取才可以办理，拾取完成后就变成个人任务了
  ```java
  taskService.createTaskQuery().taskCandidateUser("小A").list();
  taskService.claim(taskId, "小A");// 任务拾取
  taskService.setAssignee(taskId, null);// 任务回退
  List<IdentityLink> list =  taskService.getIdentityLinksForTask(taskId);// 查询任务成员
  identityLink.getType();
  taskService.addCandidateUser(taskId, userId);
  taskService.deleteCandidateUser(taskId, userId);
  ```
  - 指定处理人的三种方式：
    - 直接写死参与者和候选者
    - 启动流程时，通过流程变量，动态指定参与者和候选者
    - 使用任务监听器（taskListener）指定参与者和候选者
- 用户和用户组
  - 几乎不用，一般无法满足实际业务需求
  ```java
  identityService.saveGroup(new GroupEntity("部门经理"));
  identityService.saveGroup(new GroupEntity("总经理"));
  identityService.saveUser(new UserEntity("小明"));
  identityService.saveUser(new UserEntity("小张"));
  identityService.createMembership("小明", "部门经理");
  identityService.createMembership("小张", "总经理");
  ```
  