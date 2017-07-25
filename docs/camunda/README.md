# Camunda

## [一、Camunda基础](CamundaBasic.md)

* 1.基本介绍
* 2.Camunda BPM平台架构
* 3.Web应用程序架构
* 4.支持的环境
* 5.社区和企业扩展 
* 6.用户操作日志
* 7.数据格式(XML、JSON其他)

## [二、工作流流程引擎](CamundaProEngine.md)

* 1.流程引擎的架构
* 2.流程引擎依赖的第三方库
* 3.Process Engine流程引擎中一些重要的概念
    + （1）Process Definition 流程定义
    + （2）Process Instance 流程实例
    + （3）Process Excution 流程执行
    + （4）Activiti Instance 活动实例
    + （5）Job And Job Definition 任务和任务定义
    + （6）Process Variables 流程变量
* 4.Process Engine 流程引擎配置
    + （1）Process Engine 配置选项
    + （2）ProcessEngineConfiguration Bean 流程引擎配置类
    + （3）应用程序管理中配置流程引擎三种不同的方式

## [三、Camunda Modeler 建模工具](CamundaModeler.md)

* 1.Camunda Modeler 定义
* 2.Camunda Modeler 下载和安装
* 3.Camunda Modeler 页面布局
* 4.使用Camunda Modeler创建一个流程模型
* 5.使用自定义任务扩展Modeler
* 6.camunda Modeler依赖于的第三方库

## [四、DataBase 数据库介绍](CamundaDataBase.md)、

* DataBase Support 支持的数据库
* DataBase Schema 数据库架构
* Entity Relationship Diagrams 实体关系图
* Database Configuration 数据库配置
* Additonal database schema configuration 附加数据库模式配置

## [五、API](CoreAPI.md)

* 1.流程引擎API
    + ProcessEngines
    + Service API
        - RepositoryService
        - RuntimeService
        - TaskService
        - IdentityService
        - FormService
        - HistoryService
        - ManagementService
    + Query API
* 2.Public API 公共API
* 3.BPMN model API 模型API


## [六、配置文件]()

## [七、第三方库]()
## [八、测试]()
## [九、数据格式(XML、JSON其他)]()

## [十、流程应用程序](http://172.18.3.103/aneid/docs/tree/master/03_技术准备/支撑服务/工作流/docs/camunda/ProcessApplication.md)

## [十一、运行容器集成](http://172.18.3.103/aneid/docs/tree/master/03_技术准备/支撑服务/工作流/docs/camunda/RuntimeContainerIntegration.md)

## [十二、Spring框架集成](http://172.18.3.103/aneid/docs/tree/master/03_技术准备/支撑服务/工作流/docs/camunda/SpringFrameworkIntegration.md)

## [十三、Camunda Cockpit 驾驶舱]
## [十四、Camunda 工作流服务使用](http://172.18.3.103/aneid/docs/tree/master/03_技术准备/支撑服务/工作流/docs/camunda/CamundaUse.md)

* 准备软件环境
    + 软件环境介绍
    + 相关资源下载
    + 安装Camunda建模工具
* 准备开发环境
* 相关API的调用
* 使用Camunda管理业务流程
    + 管理流程定义
    + 流程实例，任务的执行
    + 流程变量
    + 流程执行历史记录
* BPMN2.0流程元素在Camunda中的使用