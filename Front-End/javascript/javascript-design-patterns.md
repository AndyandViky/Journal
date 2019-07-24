# js常用设计模式


1. 单例模式
	>目的：保证一个对象只生成一个实例，并提供一个全局访问口.
作用：所有使用者使用同一个实例，确保数据一致性，操作的同步.
用例：app音乐播放器，所有操作均使用同一个实例播放，保证不出现连播两首歌曲的情况。弹窗，无论点击多少次都只弹出一次.
	>主要实现方法: 如果存在实例就返回，如果不存在就创建一个新的对象再返回
     >
        // 编写主要对象
        class CreateUser{
            constructor(name){
                this.name = name;
                this.getName();
            }
            getName = function (){
                return this.name;
            }
	        }
        //代理实现单例模式
        var ProxyMode = (function(){
            var object = null;
            return function(name){
                if(!object){
                    object = new CreateUser(name);
                }
                return object;
            }
        })();



2. 策略模式
	>主要思想： 策略模式的目的就是将算法的使用算法的实现分离开来。
实现方法： 定义一系列的算法，把他们一个个封装起来，并且使他们可以相互替换。

        /*策略类*/
        var levelOBJ = {
            "A": function(money) {
                return money * 4;
            },
            "B" : function(money) {
                return money * 3;
            },
            "C" : function(money) {
                return money * 2;
            } 
        };
        /*环境类*/
        var calculateBouns =function(level,money) {
            return levelOBJ[level](money);
        };
        console.log(calculateBouns('A',10000)); // 40000

3. 代理模式
    >代理模式的定义： 为一个对象提供一个代用品或占位符，以便控制对它的访问。
    >使用场景： 图片的懒加载，先通过一张loading图占位，然后通过异步的方式加载图片，等图片加载好了再把完成的图片加载到img标签里面。

        var imgFunc = (function() {
            var imgNode = document.createElement('img');
            document.body.appendChild(imgNode);
            return {
                setSrc: function(src) {
                    imgNode.src = src;
                }
            }
        })();
        var proxyImage = (function() {
            var img = new Image();
            img.onload = function() {
                imgFunc.setSrc(this.src);
            }
            return {
                setSrc: function(src) {
                    imgFunc.setSrc('./loading,gif');
                    img.src = src;
                }
            }
        })();
        proxyImage.setSrc('./pic.png');

4. 中介者模式
    >中介者模式的定义：通过一个中介者对象，其他所有的相关对象都通过该中介者对象来通信，而不是相互引用，当其中的一个对象发生改变时，只需要通知中介者对象即可。通过中介者模式可以解除对象与对象之间的紧耦合关系。
    >中介者模式适用的场景：例如购物车需求，存在商品选择表单、颜色选择表单、购买数量表单等等，都会触发change事件，那么可以通过中介者来转发处理这些事件，实现各个事件间的解耦，仅仅维护中介者对象即可。

        var goods = {   //手机库存
            'red|32G': 3,
            'red|64G': 1,
            'blue|32G': 7,
            'blue|32G': 6,
        };
        //中介者
        var mediator = (function() {
            var colorSelect = document.getElementById('colorSelect');
            var memorySelect = document.getElementById('memorySelect');
            var numSelect = document.getElementById('numSelect');
            return {
                changed: function(obj) {
                    switch(obj){
                        case colorSelect:
                            //TODO
                            break;
                        case memorySelect:
                            //TODO
                            break;
                        case numSelect:
                            //TODO
                            break;
                    }
                }
            }
        })();
        colorSelect.onchange = function() {
            mediator.changed(this);
        };
        memorySelect.onchange = function() {
            mediator.changed(this);
        };
        numSelect.onchange = function() {
            mediator.changed(this);
        };
5. 装饰者模式
	>装饰者模式的定义：在不改变对象自身的基础上，在程序运行期间给对象动态地添加方法。

	>例如：现有4种型号的自行车分别被定义成一个单独的类，如果给每辆自行车都加上前灯、尾灯、铃铛这3个配件，如果用类继承的方式，需要创建4*3=12个子类。但如果通过装饰者模式，只需要创建3个类。

	>装饰者模式适用的场景：原有方法维持不变，在原有方法上再挂载其他方法来满足现有需求；函数的解耦，将函数拆分成多个可复用的函数，再将拆分出来的函数挂载到某个函数上，实现相同的效果但增强了复用性。

		Function.prototype.before = function(beforefn) {
            var self = this;    //保存原函数引用
            return function(){  //返回包含了原函数和新函数的 '代理函数'
                beforefn.apply(this, arguments);    //执行新函数，修正this
                return self.apply(this,arguments);  //执行原函数
            }
        }
        Function.prototype.after = function(afterfn) {
            var self = this;
            return function(){
                var ret = self.apply(this,arguments);
                afterfn.apply(this, arguments);
                return ret;
            }
        }
        var func = function() {
            console.log('2');
        }
        //func1和func3为挂载函数
        var func1 = function() {
            console.log('1');
        }
        var func3 = function() {
            console.log('3');
        }
        func = func.before(func1).after(func3);
        func();