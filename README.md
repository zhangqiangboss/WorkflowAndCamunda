# 工作流&Camunda技术预研

## [工作流（WorkFlow）](docs/workflow/workflow.md)
  * 什么是WorkFlow？
  * 工作流管理系统wfms？
  * 工作流管理联盟wfmc？
  * 为什么要使用工作流？
  * 怎么使用工作流？

## [业务流程管理标注（BPMN2.0）](docs/bpmn/bpmn2_0.md)
  * 什么是BPM？
  * 什么是BPMN？
  * BPMN2.0
    + 基本介绍
    + 流程元素
    + 基本结构（流程定义的基本要素）
        + 1.Activities活动
        + 2.Gateways网关
        + 3.Events事件
    + 除了三类基本执行语义的扩展内容 
        + 1.Data数据
        + 2.Swimlanes泳道
        + 3.Artifacts工件   
    + 一个具体的例子
    + BPMN2.0术语
    
## [Camunda](docs/camunda/README.md)
* 1.Camunda基础
* 2.Camunda Process Engine 流程引擎
* 3.Camunda Modeler 建模工具
* 4.DataBase 数据库介绍
* 5.API
* 6.配置文件
* 7.第三方库
* 8.测试
* 9.数据格式(XML、JSON其他)

### [流程应用程序](docs/camunda/ProcessApplication.md)

### [运行容器集成](docs/camunda/RuntimeContainerIntegration.md)

### [Spring框架集](docs/camunda/SpringFrameworkIntegration.md)

### [Camunda Cockpit 驾驶舱](docs/camunda/CamundaCockpit.md)

### [Camunda 工作流服务使用](docs/camunda/CamundaUse.md)

* 准备软件环境
    + 软件环境介绍
    + 相关资源下载
    + 安装Camunda建模工具
* 准备开发环境
* 相关API的调用（REST API）
* 使用Camunda管理业务流程
    + 部署流程定义
    + 启动流程实例
    + 查看并完成任务
    + 设置流程流程变量
    + 管理流程定义（删除，查看历史记录，查看流程图）
