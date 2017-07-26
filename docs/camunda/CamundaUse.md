# Camunda工作流REST API服务快速使用指南

## 一、软件环境

这里阐述的软件指的是能够调用远程Rest API的开发环境皆可。

工作中我们要做的是下载**Camunda Modeler**建模工具来构建我们的业务流程，产出相应的`.bpmn`文件。

### Camunda modeler安装和下载

官网下载地址：https://camunda.org/download/modeler/  

内网下载地址：\\172.18.3.26\write\zq\工作流\camunda_modeler

解压文件，运行camunda-modeler.exe即可。

这里附上使用**camunda modeler**构建BPMN业务流程时的参考链接：

* BPMN教程：https://camunda.org/bpmn/tutorial/
* BPMN2.0最佳实践：https://camunda.org/bpmn/examples/
* BPMN建模参考：https://camunda.org/bpmn/reference/


## 二、使用Camunda Modeler前置知识

### 1.[BPMN2.0术语表](../bpmn/BPMN2术语表.docx)

熟悉BPMN2.0中的专业术语是我们在团队协作中良好沟通的前提，减少不必要的分歧，提高工作效率。

### 2.BPMN2.0中的重要概念简单介绍
详细文档信息参见：[BPMN2.0](../bpmn/bpmn2_0.md)
#### (1)3类基本执行要义（Actitities活动，Gateways网关，Events事件）
Activities活动：  
在工作流中所有具备生命周期状态的都可以成为“活动”，如原子级任务（Task），序列流（Sequence flow），以及子流程（Sub-Process）等。

Gateways网关:  
网关用于控制序列流在流程内的合并和拆分的相互作用，用来控制流程流转的指向的。

Events事件:  
事件在实际的每个业务流程中都会使用。 事件让业务建模工具用很自然的方式描述业务流程。

> 比如 ‘当我接收到客户的订单，这个流程就启动’， ‘如果两天内任务没结束，就终止流程’ 或者‘当我收到一封取消邮件，当流程在运行时， 使用子流程处理邮件’。

### (2)需要关注和常用执行要义
（1）Activities活动：Sequence Flow序列流、Task任务（UserTask用户任务、Manunal Task手动任务、Script Task脚本任务，Send Task发送任务、Service Task服务任务、Receive Task接收任务、External Task外部任务）、Sub-Process子流程(内嵌子流程)

（2）Gateways网关：Exclusive Gateway排它网关、Exclusive Gateway并行网关、Inclusive Gateway包容网关

（3）Events事件：Start Event开始事件、End Event空结束事件、Terminate End Event终止结束事件

（4）Process Variables流程变量：流程变量的作用通常分为两个方面：
1. 用于流程执行的过程中，数据在不同的活动节点中共享，这个变量可以传递，更改，和流转。比如，我们可以为一个用户任务动态的指定assignee等；
2. 用于控制流程的流转方向，当流程到达类似于GateWay网关的时候，我们需要设置我们的流程变量，以便于流程引擎判断将要往哪条路径继续执行流程。

相关文档链接：  
* [Task任务参考文档](tasks.md)
* [BPMN2.0](../bpmn/bpmn2_0.md)

## 三、实际应用-在项目中调用远程服务管理业务流程
这里阐述我们在项目中如何使用Camunda BPM平台给我们提供的Rest API去管理我们的业务流程。

### 1.处理业务流程的一般步骤
1. 建模。即创建业务流程模型，使用Modeler画业务流程图
2. 部署。即将我们画好的流程图部署到我们的工作流服务中。
3. 启动。部署完成后不会自动自动流程，而需要我们手动启动，启动后将会开始执行流程。需要注意的是，我们通常使用流程定义的key来启动，这个Key对应着我们保存BPMN文件时的Id。
4. 查看活动。这里可以查看当前流程正处在哪个活动节点，正在执行什么任务。如果是个用户任务，可以通过assignee查看。
5. 完成任务。根据不同的任务类型，使用对应的方式完成它。
6. 重复4.5步骤，只到流程结束。

### 2.步骤补充说明

在我们处理业务流程的过程中会涉及到引擎中的一些重要概念，这里需要事先了解，以便我们在调用相关API来控制我们的流程的时候，造成混乱和错误。

**相关概念：**
> 1. 流程部署对象Process Deployment：即我们在将BPMN文件部署到工作流服务中的时候，Camunda引擎会产生一个流程部署对象Deployement，每次部署都会产生一个部署对象。
> 2. 流程定义 Process Definition：通俗的讲，我们的一个BPMN文件就可以看成一个流程定义，当我们多次部署了一个相同key的的流程定义时，原先的流程定义不会消失，只是现部署的流程定义版本会在原来的基础上+1.
> 3. 流程实例 Process Instance：当我们启动一个流程时，流程引擎就会产生一个流程实例，直到这个流程执行完，流程实例也相应结束。由于一个流程定义可能有多条执行路径，所以每次启动都会产生一个新的流程实例。
> 4. 流程执行对象 Process Excution：即实际执行当前流程实例的对象。在流程执行过程中，一般情况下，一个流程实例对应一个执行对象。但是如果出现了异步任务，如发送任务，接收任务，服务任务，外部任务。此时会针对当前任务创建另一个执行对象去执行这些任务，直到任务结束，执行对象也随之消失。
> 5. 活动实例：即流程定义中的每一个活动节点，当流程到达这个节点时，流程引擎会创建一个活动实例，这个活动实例可以是Sequence Flow，Task，Subprocess，直达当前活动结束，活动实例也相应结束。

**它们之间的关系：**

> 一个流程定义可能有多个流程实例(可以比作Java中类Class和对象Object的关系)  
> 每个流程实例对应一个或者多个流程执行对象  
> 每个执行对象对应多个当前流程实例中的活动实例  

### 3.步骤中涉及到Rest API地址
* 部署：`POST /deployment/create`  
参考网址：https://docs.camunda.org/manual/latest/reference/rest/deployment/post-deployment/
* 启动：`POST /process-definition/key/{key}/start`  
参考网址：https://docs.camunda.org/manual/latest/reference/rest/process-definition/post-start-process-instance/
* 查看任务：  
参考网址：https://docs.camunda.org/manual/latest/reference/rest/task/
* 查看正在进行的活动：`GET process-instance/{id}/activity-instances`    
参考网址：https://docs.camunda.org/manual/latest/reference/rest/process-instance/get-activity-instances/
* 完成任务：  
参考网址：https://docs.camunda.org/manual/latest/reference/rest/task/post-complete/
* 查看完成外部任务：  
参考网址：https://docs.camunda.org/manual/latest/reference/rest/external-task/
* 穿过任务（如Recieve Task）：  
参考网址：https://docs.camunda.org/manual/latest/reference/rest/execution/post-signal/

## 三、[实际例子参考（请假流程）]()