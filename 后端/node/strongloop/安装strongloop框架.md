# 安装strongloop框架以及集成sequlize

__[install](http://www.cnblogs.com/chen110xi/p/5280517.html "title")__
__[study](http://cnodejs.org/topic/57e5b2859c495dce044f397c "study")__

1. first install node and npm

2. run `npm install -g loopback-cli`

3. run `npm install -g apiconnect`

4. run `npm install -g strongloop`

_if you want to look version, you can run __slc -v___


>if has error not find commond
>
>you need `run npm uninstall -g strongloop`
>
>then run `npm clear cache`
>
>then run `npm install -g strongloop`

## secusse install 

1. create project run `slc loopback [project name]`

2. run `cd [project name]`

3. run `npm install` install nessesery package

4. install StrongLoop MySQL 连接器 `npm install --save loopback-connector-mysql`

5. 配置数据源
    >slc loopback:datasource AccountDb
    >
    >接受默认数据源名称。对于数据库类型，选择 MySQL。

6. 创建数据模型 
        
        slc loopback:model Account
        按照提示配置该数据模型。对于模型的“base class”，使用“PersistedModel”并选择通过 REST API 公开该模型。添加模型属性如下：

        Name:Account 
        Datasource:AccountDb
        Base class:PersistedModel
        Expose via REST:Yes
        Custom plural form:Leave blank
        对于数据模型上的属性，创建以下 3 个属性。

        Property name: email 
        Property type: string
        Required?Not required
        Property name: createdAt 
        Property type: date
        Required?Not required
        Property name: lastModifiedAt 
        Property type: date
        Required?Not required


7. 添加脚本来创建数据库中的模式
    >在您项目的根目录下的 ./server 文件夹中创建一个 bin 目录。
    >
    >cd server
    >
    >mkdir bin
    >
    >在 server/bin 目录中，使用以下代码创建一个名为 automigrate.js 的文件。

        var app = require('../server');

        var accounts = [
            {
                email:'foo@bar.com',
                createdAt: new Date(),
                lastModifiedAt: new Date()
            },
            {
                email:'baz@qux.com',
                createdAt: new Date(),
                lastModifiedAt: new Date()
            }
        ];

        // this loads the accountDb configuration in ~/server/datasources.json
        var dataSource = app.dataSources.AccountDb;

        // this automigrates the Account model 
        dataSource.automigrate('Account', function(err) {
            if (err) throw err;

            // this loads the Account model from ~/common/models/Account.json
            var Account = app.models.Account;
            var count = accounts.length;
            accounts.forEach(function(account) {
            // insert new records into the Account table
                Account.create(account, function(err, record) {
                if (err) return console.log(err);

                console.log('Record created:', record);

                count--;

                if (count === 0) {
                console.log('done');
                dataSource.disconnect();
                }
                });
            });
        });

8. run `node server/bin/automigrate.js`


------------------------------
### extend

1. `slc loopback:property` or `sudo lb property` add new props

2. `sudo lb relation` or `sudo slc loopback:relation` add new relation

3. `sudo lb acl` or `sudo slc loopback:acl` 定义访问权限
------------------------------


#### use sequelize orm
1. `npm install --save sequelize` install sequelize packge
2. `npm install --save mysql2` install mysql2 packge
3. `npm install --save co` install co 异步控制库

4. `npm install -g sequelize-auto` 将数据库映射成model

5. run `sequelize-auto -h localhost -d [database] -u [username] -x [password] -p 3306 -t [tablename]` 