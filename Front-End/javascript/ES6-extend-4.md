
# ES6相关扩展 (四)

1. Proxy
   * 概述
        >Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程.Proxy 实际上重载（overload）了点运算符，即用自己的定义覆盖了语言的原始定义

            var obj = new Proxy({}, {
                get: function (target, key, receiver) {
                    console.log(`getting ${key}!`);
                    return Reflect.get(target, key, receiver);
                    // return target[property]; 另一种写法
                },
                set: function (target, key, value, receiver) {
                    console.log(`setting ${key}!`);
                    return Reflect.set(target, key, value, receiver);
                }
            });
            obj.count = 1
            //  setting count!
            ++obj.count
            //  getting count!
            //  setting count!
            //  2
        >Proxy 支持的拦截操作一览
        * get(target, propKey, receiver)：拦截对象属性的读取，比如proxy.foo和proxy['foo']。
        * set(target, propKey, value, receiver)：拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。
        * has(target, propKey)：拦截propKey in proxy的操作，返回一个布尔值。
        * deleteProperty(target, propKey)：拦截delete proxy[propKey]的操作，返回一个布尔值。
        * ownKeys(target)：拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、* Object.keys(proxy)、for...in循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。
        * getOwnPropertyDescriptor(target, propKey)：拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
        * defineProperty(target, propKey, propDesc)：拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。
        * preventExtensions(target)：拦截Object.preventExtensions(proxy)，返回一个布尔值。
        * getPrototypeOf(target)：拦截Object.getPrototypeOf(proxy)，返回一个对象。
        * isExtensible(target)：拦截Object.isExtensible(proxy)，返回一个布尔值。
        * setPrototypeOf(target, proto)：拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
        * apply(target, object, args)：拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
        * construct(target, args)：拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。
    * Proxy.revocable() 返回一个可取消的 Proxy 实例
           
            let target = {};
            let handler = {};

            let {proxy, revoke} = Proxy.revocable(target, handler);

            proxy.foo = 123;
            proxy.foo // 123

            revoke();
            proxy.foo // TypeError: Revoked
    * this 问题
        >虽然 Proxy 可以代理针对目标对象的访问，但它不是目标对象的透明代理，即不做任何拦截的情况下，也无法保证与目标对象的行为一致。主要原因就是在 Proxy 代理的情况下，目标对象内部的this关键字会指向 Proxy 代理。
            
            const _name = new WeakMap();

            class Person {
                constructor(name) {
                    _name.set(this, name);
                }
                get name() {
                    return _name.get(this);
                }
            }

            const jane = new Person('Jane');
            jane.name // 'Jane'

            const proxy = new Proxy(jane, {});
            proxy.name // undefined

            // 解决办法
            const target = new Date('2015-01-01');
            const handler = {
                get(target, prop) {
                    if (prop === 'getDate') {
                    return target.getDate.bind(target);
                    }
                    return Reflect.get(target, prop);
                }
            };
            const proxy = new Proxy(target, handler);

            proxy.getDate() // 1
2. Reflect
    * 概述
        >（1） 将Object对象的一些明显属于语言内部的方法（比如Object.defineProperty），放到Reflect对象上。现阶段，某些方法同时在Object和Reflect对象上部署，未来的新方法将只部署在Reflect对象上。也就是说，从Reflect对象上可以拿到语言内部的方法。
        
        >（2） 修改某些Object方法的返回结果，让其变得更合理。比如，Object.defineProperty(obj, name, desc)在无法定义属性时，会抛出一个错误，而Reflect.defineProperty(obj, name, desc)则会返回false。
       
            // 老写法
            try {
                Object.defineProperty(target, property, attributes);
                // success
            } catch (e) {
                // failure
            }

            // 新写法
            if (Reflect.defineProperty(target, property, attributes)) {
                // success
            } else {
                // failure
            }
        >（3） 让Object操作都变成函数行为。某些Object操作是命令式，比如name in obj和delete obj[name]，而Reflect.has(obj, name)和Reflect.deleteProperty(obj, name)让它们变成了函数行为。
            
            // 老写法
            'assign' in Object // true

            // 新写法
            Reflect.has(Object, 'assign') // true
        >（4）Reflect对象的方法与Proxy对象的方法一一对应，只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法。这就让Proxy对象可以方便地调用对应的Reflect方法，完成默认行为，作为修改行为的基础。也就是说，不管Proxy怎么修改默认行为，你总可以在Reflect上获取默认行为。
    
    * 静态方法
        * Reflect.apply(target, thisArg, args)
        * Reflect.construct(target, args)
        * Reflect.get(target, name, receiver)
        * Reflect.set(target, name, value, receiver)
        * Reflect.defineProperty(target, name, desc)
        * Reflect.deleteProperty(target, name)
        * Reflect.has(target, name)
        * Reflect.ownKeys(target)
        * Reflect.isExtensible(target)
        * Reflect.preventExtensions(target)
        * Reflect.getOwnPropertyDescriptor(target, name)
        * Reflect.getPrototypeOf(target)
        * Reflect.setPrototypeOf(target, prototype)