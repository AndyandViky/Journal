# ES6相关扩展 (三)

1. Symbol
    * 概述
        >它是 JavaScript 语言的第七种数据类型，前六种是：undefined、null、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。

            let s = Symbol();
            typeof s // "symbol"

            // 没有参数的情况
            let s1 = Symbol();
            let s2 = Symbol();
            s1 === s2 // false

            // 有参数的情况
            let s1 = Symbol('foo');
            let s2 = Symbol('foo');
            s1.toString() // "Symbol(foo)"
            s1 === s2 // false
    * 作为属性名的 Symbol

            const PROP_NAME = Symbol()
            const PROP_AGE = Symbol()

            let obj = {
            [PROP_NAME]: "一斤代码"
            }
            obj[PROP_AGE] = 18

            obj[PROP_NAME] // '一斤代码'
            obj[PROP_AGE] // 18
    * 属性名的遍历
        >Symbol类型的key是不能通过Object.keys()或者for...in来枚举的，它未被包含在对象自身的属性名集合(property names)之中。所以，利用该特性，我们可以把一些不需要对外操作和访问的属性使用Symbol来定义。
        >JSON.stringify()将对象转换成JSON字符串的时候，Symbol属性也会被排除在输出内容之外

            let obj = {
                [Symbol('name')]: '一斤代码',
                age: 18,
                title: 'Engineer'
            }

            Object.keys(obj)   // ['age', 'title']

            for (let p in obj) {
                console.log(p)   // 分别会输出：'age' 和 'title'
            }

            Object.getOwnPropertyNames(obj)   // ['age', 'title']
    * 作为常量的 Symbol

            const TYPE_AUDIO = Symbol()
            const TYPE_VIDEO = Symbol()
            const TYPE_IMAGE = Symbol()
            function handleFileResource(resource) {
                switch(resource.type) {
                    case TYPE_AUDIO:
                        playAudio(resource)
                        break
                    case TYPE_VIDEO:
                        playVideo(resource)
                        break
                    case TYPE_IMAGE:
                        previewImage(resource)
                        break
                    default:
                        throw new Error('Unknown type of resource')
                }
            }
    * Symbol.for() 重新使用同一个 Symbol 值
        >它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建并返回一个以该字符串为名称的 Symbol 值。
           
            let s1 = Symbol.for('foo');
            let s2 = Symbol.for('foo');

            s1 === s2 // true
    * Symbol.keyFor() 返回一个已登记的 Symbol 类型值的key

            let s1 = Symbol.for("foo");
            Symbol.keyFor(s1) // "foo"

            let s2 = Symbol("foo");
            Symbol.keyFor(s2) // undefined

2. Set 和 Map 数据结构
    * Set
        >新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

            const s = new Set();
            [2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
            for (let i of s) {
                console.log(i);
            }
            // 2 3 5 4

            const set = new Set([1, 2, 3, 4, 4]); 传入数组作为参数
            [...set]
            // [1, 2, 3, 4]
            // 这种方法可以用去去除数组中的重复值 例： [...new Set('ababbc')].join('')
        >属性和方法
        * Set.prototype.constructor：构造函数，默认就是Set函数。
        * Set.prototype.size：返回Set实例的成员总数。
        * Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。下面先介绍四个操作方法。
        * add(value)：添加某个值，返回 Set 结构本身。
        * delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
        * has(value)：返回一个布尔值，表示该值是否为Set的成员。
        * clear()：清除所有成员，没有返回值。
        >遍历操作
        * keys()：返回键名的遍历器
        * values()：返回键值的遍历器
        * entries()：返回键值对的遍历器
        * forEach()：使用回调函数遍历每个成员
    * WeakSet
        >WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别。首先，WeakSet 的成员只能是对象，而不能是其他类型的值。
    * Map // 键值对的集合

        >属性和方法
        * Map.prototype.constructor：构造函数，默认就是Set函数。
        * Map.prototype.size：返回Map实例的成员总数。
        * Map 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。下面先介绍四个操作方法。
        * set(key, value)：设置键名key对应的键值为value，然后返回整个 Map 结构
        * get(key): 读取key对应的键值，如果找不到key，返回undefined。
        * delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
        * has(value)：返回一个布尔值，表示该值是否为Map的成员。
        * clear()：清除所有成员，没有返回值。
        >遍历操作 // 需要特别注意的是，Map 的遍历顺序就是插入顺序
        * keys()：返回键名的遍历器
        * values()：返回键值的遍历器
        * entries()：返回所有成员的遍历器。
        * forEach()：遍历 Map 的所有成员。
        >与其他数据结构的互相转换
        * Map 转为数组

                const myMap = new Map()
                .set(true, 7)
                .set({foo: 3}, ['abc']);
                [...myMap]
                // [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
        * 数组 转为 Map
            
                new Map([
                    [true, 7],
                    [{foo: 3}, ['abc']]
                ])
        * Map 转为对象

                function strMapToObj(strMap) {
                    let obj = Object.create(null);
                    for (let [k,v] of strMap) {
                        obj[k] = v;
                    }
                    return obj;
                }

                const myMap = new Map()
                .set('yes', true)
                .set('no', false);
                strMapToObj(myMap)
                // { yes: true, no: false }
        * 对象转为 Map

                function objToStrMap(obj) {
                    let strMap = new Map();
                    for (let k of Object.keys(obj)) {
                        strMap.set(k, obj[k]);
                    }
                    return strMap;
                }

                objToStrMap({yes: true, no: false})
                // Map {"yes" => true, "no" => false}
    * WeakMap // WeakMap只接受对象作为键名