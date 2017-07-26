# Camunda BPMN2.0 Task 
## 1.User Task 用户任务
用户任务是需要人员角色去完成的任务，这个人员角色可以是单个用户、用户组、用户组列表。这个人员角色可以直接指定，也可以使用表达式动态的指定，这个表达式也指得是流程变量。

**这里需要注意区分参与者（assignee）和候选人（candicate）的区别！**

> 1. 当任务角色是单个用户(assignee)，那么这个用户就是这个任务的参与者，他将是实际执行这个任务的人员。
> 2. 当任务角色是用户组(Candicate Users)，那么用户组中的每个成员都是候选人（candicate）,代表着其中每个成员都将可能是任务的参与者，也就是实际执行者。在流程到达次任务时，可以其中成员认领（claim）任务，也就是指定谁去完成。（完成时指定人员是为了记录历史方便审阅，这不是必要的。）
> 3. 当任务角色是用户组列表（Candicate groups），那么列表中的每个组中的成员都将是候选人，当流程到达此任务时，需要建立用户组，建立用户，然后创建用户和组之间的映射关系。完成任务时同样可以指定候选人中谁去完成。


## 2.Manual Task 手动任务（人工任务）
手动任务指的是线下任务，人工任务。它不会影响流程的执行，是一个直通任务。

## 3.Script Task 脚本任务
脚本任务是自动化的活动。当流程到达脚本任务时，执行相应的脚本。通过指定脚本和scriptFormat来定义脚本任务。scriptFormat属性的值必须是与JSR-223（Java Platform的脚本）兼容的名称。`eg.Javascript,JRuby,Groovy,Jython`

脚本任务的用途：
1. 作为序列流（Sequence Flow）的条件表达式，和El表达式写法类似。
2. 作为执行监听器，详细参见：https://docs.camunda.org/manual/7.7/user-guide/process-engine/delegation-code/#execution-listener
3. 作为任务监听器，详细参见：https://docs.camunda.org/manual/7.7/user-guide/process-engine/delegation-code/#task-listener
4.作为inputOutPut参数，详细参见：https://docs.camunda.org/manual/7.7/user-guide/process-engine/scripting/

**这里需要注意的是：在我们业务系统使用的过程中，需要执行的业务相关的脚本使用自定义的方式去执行，而不要去交给脚本任务。脚本任务只用于控制和监听业务流程。**

## 4.Service Task 服务任务
在Camunda中它使用java代码来实现，或者使用External Tasks外部任务实现。  
> 说明：由于我们在此次项目中不使用JavaAPI，采用RestAPI的方式，所以我们服务任务的实现将采用外部任务的方式。

外部任务：  
1. 当一个Service Task服务任务的Implementation设置为External，并设置Topic的值，部署时会创建一个外部任务实例到外部服务列表。
2. 当到达外部任务的时候，流程引擎会为当前的外部任务创建一个执行对象实例
3. 通过JavaApi／RestApi获取外部任务。（这个步骤在流程流转中不是必须的）
4. 抓取并锁定外部任务。在完成外部任务之前，我们需要将外部任务获取并锁定，这两个动作将同时进行。锁定指的是，通过Topic的值给当前的外部任务指定workerId，并设置锁定时间lockDuration。在锁定的时间内，其他用户无法查询到此项外部任务。
5. 完成外部任务，通过外部任务ID和传入匹配的workerId才能完成此项外部任务。
6. 外部任务完成，流程继续往下执行，此时为外部任务创建的执行对象将会结束，正在执行的外部任务实例也会结束。
 

## 5.Send Task 发送任务
发送任务被用来发送消息。它和Service Task具有相同的行为。同样采取和服务任务相同的实现。

## 6.Receive Task 接收任务
接收任务是等待某个消息到达的简单任务。当流程执行到达接收任务时，流程状态被提交给持久性存储。这意味着该流程将保持在该等待状态，直到引擎接收到特定消息，这将触发穿过接收任务的继续执行流程。

> 说明：由于接收任务中的消息和message event消息事件息息相关
在嵌入式流程引擎当中，Camunda引擎没有实现消息的接收部分。例如连接到JMS（Java消息服务）队列/主题或处理Web服务或REST请求，这些将会成为环境中需要的依赖和平台所需的特定活动。因此，接收消息是必须手动实现的，作为嵌入流程引擎的应用程序或基础设施的一部分。收到消息后，可以选择是使用引擎的内置关联还是明确传递消息以启动新的流程实例或触发等待执行。

综上所述，我们在项目中可以用Recieve Task接收任务去描述一个Actitiy活动，只是为了更准确的表达业务的流程意义，但没有实际没有此方面的功能。我们需要自己去实现消息通知等服务，以便控制我们流程将如何流转。

当流程到达Recieve Task接收服务的时候，我们通过执行对象实例ID来穿过接收任务，继续下一步流程的执行。  
相关API：`POST /execution/{id}/signal`   
参考地址：https://docs.camunda.org/manual/7.7/reference/rest/execution/post-signal/


## 7.Business Rule Task 业务规则任务
由于此类任务使用的是Camunda DMN引擎，所以这里不做讨论。

## Task Markers 任务标记
除了各种类型的任务，我们可以将任务标记为循环，多个实例或compensations。标记可以与任务类型组合。  
详细参阅：https://docs.camunda.org/manual/7.7/reference/bpmn20/tasks/task-markers/

## 获取任务实例的方式扩展
除了直接使用RestAPI获取任务实例外，这里介绍一种获取任务实例有效的间接方式。

获取正在执行的活动：
1. 根据流程实例Id获取正在执行的活动实例  
对应API：`GET process-instance/{id}/activity-instances`
2. 由返回的活动实例ActivityId去查询正在执行的任务或者外部任务
