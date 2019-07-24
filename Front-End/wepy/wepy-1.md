# wepy学习(一)

1. 项目目录

        ├── dist               小程序运行代码目录（该目录由WePY的build指令自动编译生成，请不要直接修改该目录下的文件）
        ├── node_modules           
        ├── src                代码编写的目录（该目录为使用WePY后的开发目录）
        |   ├── components     WePY组件目录（组件不属于完整页面，仅供完整页面或其他组件引用）
        |   |   ├── com_a.wpy  可复用的WePY组件a
        |   |   └── com_b.wpy  可复用的WePY组件b
        |   ├── pages          WePY页面目录（属于完整页面）
        |   |   ├── index.wpy  index页面（经build后，会在dist目录下的pages目录生成index.js、index.json、index.wxml和index.wxss文件）
        |   |   └── other.wpy  other页面（经build后，会在dist目录下的pages目录生成other.js、other.json、other.wxml和other.wxss文件）
        |   └── app.wpy        小程序配置项（全局数据、样式、声明钩子等；经build后，会在dist目录下生成app.js、app.json和app.wxss文件）
        └── package.json       项目的package配置

2. 生命周期
   
        // app.wpy文件中
        // onLaunch, onShow 方法会返回一个参数对象， 里面包含了三个参数 ， path,query和scene ，path是打开小程序的路径，query是打开小程序页面url的参数，scene是打开小程序的场景值
        onLaunch: function (options) {
          console.log("app.js ---onLaunch---" + JSON.stringify(options));
        },
        onShow:function(){
          console.log("app.js ---onShow---");
        },
        onHide:function(){
          console.log("app.js ---onHide---");
        },
        onError: function (msg){
          console.log("app.js ---onError---" + msg);
        },
        globalData: {
          userInfo: null
        }
        config = {}  // 对应 app.json 文件


        // page页面
        onLoad: function (options) {   // 在Page和Component共用的生命周期函数
          console.log("page ---onLoad---");
        },
        onReady: function () {
          console.log("page ---onReady---");
        },
        onShow: function () {
          console.log("page ---onShow---");
        },
        onHide: function () {
          console.log("page ---onHide---");
        },
        onUnload: function () {
          console.log("page ---onUnload---");
        },
        config = {};  // 只在Page实例中存在的配置数据，对应于原生的page.json文件

3. 其他属性和方法
   
        customData = {}  // 自定义数据

        customFunction ()　{}  //自定义方法

        data = {};  // 页面所需数据均需在这里声明，可用于模板数据绑定

        components = {};  // 声明页面中所引用的组件，或声明组件中所引用的子组件

        mixins = [];  // 声明页面所引用的Mixin实例

        computed = {};  // 声明计算属性

        watch = {};  // 声明数据watcher

        methods = {};  // 声明页面wxml中标签的事件处理函数。注意，此处只用于声明页面wxml中标签的bind、catch事件，自定义方法需以自定义方法的方式声明

        events = {};  // 声明组件之间的事件处理函数

4. interceptor 拦截器
   
        // 在app.wpy中
        constructor () {
            super();
            // 拦截request请求
            this.intercept('request', {
                // 发出请求时的回调函数
                config (p) {
                    // 对所有request请求中的OBJECT参数对象统一附加时间戳属性
                    p.timestamp = +new Date();
                    console.log('config request: ', p);
                    // 必须返回OBJECT参数对象，否则无法发送请求到服务端
                    return p;
                },

                // 请求成功后的回调函数
                success (p) {
                    // 可以在这里对收到的响应数据对象进行加工处理
                    console.log('request success: ', p);
                    // 必须返回响应数据对象，否则后续无法对响应数据进行处理
                    return p;
                },

                //请求失败后的回调函数
                fail (p) {
                    console.log('request fail: ', p);
                    // 必须返回响应数据对象，否则后续无法对响应数据进行处理
                    return p;
                },

                // 请求完成时的回调函数(请求成功或失败都会被执行)
                complete (p) {
                    console.log('request complete: ', p);
                }
            });
        }