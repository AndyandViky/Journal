# StrongLoop microgateway使用总结（一）
## 源码引入
### github地址`https://github.com/strongloop/microgateway`
#### 不使用manage管理工具

1. 拉取源码
        
        git clone https://github.com/strongloop/microgateway

2. `npm install` 安装依赖项

3. 在根目录创建 __app__ 文件,用于存放网关api

4. 增加 __env.yaml__ 文件

        DIR_CONFIG: "./app" // 监测app文件下的所有api操作
        PORT: 5000 // 网关启动端口
        ... // 其他配置请自行添加
        
5. 增加 __id_rsa__ 文件,存放的为ssh密钥

6. 简单例子:

    在app下创建 __sample__ 文件,在sample文件下创建 __sample_1.0.0__ 文件

        # sample_1.0.0.yaml
        #
        info:
        version: 1.0.0
        title: sample
        description: sample laptop yaml
        basePath: /sample
        swagger: '2.0'
        paths:
        /echo:
            get:
            responses:
                '200':
                description: 200 OK
        x-ibm-configuration:
        assembly:
            execute:
            - javascript:
                title: write a small json object
                source: |
                message.body = { text : 'Hello World' };
        schemes:
        - http

7. 启动网关 `node .`

8. 此时网关便启动,执行 `curl http://localhost:5000/sample/echo` 发现启动成功

        返回: { text : 'Hello World' }