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
// 部署流程定义1
Deployment deploy = respositoryService.createDeployment()
.name("请假流程001")
.addClasspathResource("HelloWorld.bpmn")
.addClasspathResource("HelloWorld.png").deploy();

// 部署流程定义2:流程图的文件必须是.zip结尾
// 修改流程图后重新部署，只要key不变，它的版本号会+1
InputStream stream = this.getClass().getResourceAsStream("/HelloWorld.zip");
ZipInputStream zipInputStream = new ZipInputStream(stream);
Deployment deploy = respositoryService.createDeployment()
.name("请假流程001")
.addZipInputStream(zipInputStream).deploy();

// 查询流程部署
respositoryService.createDeploymentQuery().deploymentId(id).orderByDeploymentId().asc().list();
respositoryService.createDeploymentQuery().deploymentName(name).listPage(firstResult, maxResults);
respositoryService.createDeploymentQuery().deploymentNameLike(nameLike).singleResult();
respositoryService.createDeploymentQuery().deploymentNameLike(nameLike).count();

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