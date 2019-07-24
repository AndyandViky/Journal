# wepy学习(二)

1. config详解
   
        config = {
          pages: [  // 页面路径定义，也就相当于路由的定义，wepy默认使用pages下的文件名作为路由，但是需要在此处注册
            'pages/articles',
          ],
          window: { // 初始化定义小程序相关样式，以及是否启用相关功能
            backgroundTextStyle: 'dark',
            navigationBarBackgroundColor: '#fff',
            navigationBarTextStyle: 'black',
            enablePullDownRefresh: true,
            backgroundColor: '#EFEFEF'
          },
          tabBar: { // 底部导航栏的设置
            color: '#999999',
            selectedColor: '#ff6a3c',
            backgroundColor: '#ffffff',
            borderStyle: 'black',
            list: [
              {
                pagePath: 'pages/articles',
                text: '首页',
                iconPath: 'static/images/icon_home.png',
                selectedIconPath: 'static/images/icon_home_active.png'
              },
              {
                pagePath: 'pages/github',
                text: 'github',
                iconPath: 'static/images/icon_classify.png',
                selectedIconPath: 'static/images/icon_classify_active.png'
              }
            ]
          }
        };
2. 相关方法详解
   1. 组件的循环渲染

          // 在wepy下需要使用辅助组件进行循环渲染
          <repeat for="{{articles}}" index="index" key="index" item="item">
            <articleList class="a_item" :article.sync="item"></articleList>
          </repeat>
   2. computed 计算属性

          // 注意！！ 只要是组件中有任何数据发生了改变，那么所有计算属性就都会被重新计算
          computed = {
            aPlus () {
                return this.a + 1;
            }
          }
   3. watcher 监听器

          // 和vue使用差不多
          watch = {
            num (newValue, oldValue) {
                console.log(1);
            }
          }
   4. event 事件处理

          // 在vue中，此类事件全部写在methods里
          // 父组件接收子组件emit
          // 子组件接收父组件的广播
          events = {
            'request-search': querys => {
              console.log(querys);
            }
          };
  
3. 组件的交互
   1. props 传值
      1. 静态传值（只能传string类型的值）

              <child name="andy"></child>
      2. 动态传值

              // 普通传值不绑定
              <child :name="user.name"></child>
              
              // 父组件绑定子组件
              <child :user.sync="user"></child>
              
              // 子组件绑定父组件
              <child :user="user"></child>
              // 在child.wpy中
              user: {
                  type: Object,
                  default: {}',
                  twoWay: true // 将此属性设为true时，属性在子组件改变的同时会改变父组件的值
              }
   2. 组件自定义事件处理函数

          // .default默认，绑定小程序冒泡型事件，如bindtap，.default后缀可省略不写
          <child @tap="doTap"></child>

          // .stop: 绑定小程序捕获型事件，如catchtap
          <child @tap.stop="catchTap"></child>

          // .user: 绑定用户自定义组件事件，通过$emit触发。注意，如果用了自定义事件，则events中对应的监听函数不会再执行。
          <child @getEvent.user="getEvent"></child>
          // 此时的getEvent函数不再需要写入events，写在methods中即可

4. slot 组件内容分发插槽

        // 小程序组件中不允许插入其他内容
        <child @tap="doTap">123123</child> // 其中的 123123 内容将会被忽略

        // 在child.wpy中使用slot
        <view>
          <slot name="testData"></slot>
        </view>
        // 在引用该组件的文件中
        <child @tap="doTap">
          <view slot="testData">123123</view>  // 此时的内容会被渲染
        </child>
5. Mixin 混合
   1. 默认式混合
      > 对于组件data数据，components组件，events事件以及其它自定义方法采用默认式混合,即如果组件未声明该数据，组件，事件，自定义方法等，那么将混合对象中的选项将注入组件之中。对于组件已声明的选项将不受影响。
   2. 兼容式混合
      > 对于组件methods响应事件，以及小程序页面事件将采用兼容式混合，即先响应组件本身响应事件，然后再响应混合对象中响应事件(此顺序和vue相反)
6. WePY数据绑定方式

        // 原生小程序,类似于react
        this.setData({title: 'this is title'})

        // wepy
        this.title = 'this is title';

        // wepy异步情况下
        this.title = 'this is title';
        this.$apply(); // 需要手动更新
