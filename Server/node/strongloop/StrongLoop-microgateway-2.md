# StrongLoop microgateway使用总结（二）
## 源码引入
### github地址`https://github.com/strongloop/microgateway`
#### 不使用manage管理工具

#### 网关流程图详解,以及各个现有策略简介

1. >由于网关为开源项目, 所有可以在自定义自己想要的策略
    >
    >网关是基于node的express框架
    
2. 网关图表具体展示网关运行流程

    !["网关流程图"](https://raw.githubusercontent.com/strongloop/microgateway/master/images/readme/MicroGatewayArchitecture2.png)

    1. urlrewrite 路由写入中间件,能够在一定条件下简单的修改url,但一般只是作为网关入口
    
    2. context 环境中间件,创建一个上下文的暂存区域,作用在于创建并且存放网关运行的各种变量.任何中间件和策略都可以增加修改删除其中的变量
    
    !["变量图1"](https://raw.githubusercontent.com/strongloop/microgateway/master/images/readme/ContextVariablesPart1.png)
    !["变量图2"](https://raw.githubusercontent.com/strongloop/microgateway/master/images/readme/ContextVariablesPart2.png)

    3. analytics 解析中间件,用于连接外部分析引擎,作用是将上下文中创建的变量传递给外部收集设备

    4. preflow 预留中间件,类似与消息队列, 所有的网关路由在这里排对等待执行, 在这当中可以进行一系列的权限安全验证; 执行与Swagger定义相关联的安全性; 速率限制(限流); 创建程序集所需的一些对象,以便流程引擎使用

    5. Policies assembly flow 策略配置流中间件,工作于流程引擎中, 作用是 执行一系列的策略
        1. operation-swith 条件分支
        2. javascript 在 yaml 扩展文件中使用javascript
        3. invoke 网关转发
        4. set-avariable 设置变量,例如请求头信息等
        5. if 条件判断

    6. postFlow, error_handler 发送流,错误处理中间件, 同时运行在网关处理之后,和preflow一样类似于队列,排队等待返回

    7. datastore 数据仓库, 存放网关所需要的所有数据, 配合loopback可以进行一些权限验证信息的获取等.