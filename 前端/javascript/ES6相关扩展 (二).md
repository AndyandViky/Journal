# ES6相关扩展 (二)

1. 对象的扩展
    * 属性的可枚举性和遍历
        >对象的每个属性都有一个描述对象（Descriptor），用来控制该属性的行为。Object.getOwnPropertyDescriptor方法可以获取该属性的描述对象。

            let obj = { foo: 123 };
            Object.getOwnPropertyDescriptor(obj, 'foo')
            //  {
            //    value: 123,
            //    writable: true,
            //    enumerable: true,
            //    configurable: true
            //  }
        >描述对象的enumerable属性，称为“可枚举性”，如果该属性为false，就表示某些操作会忽略当前属性。
        >目前，有四个操作会忽略enumerable为false的属性。

            for...in循环：只遍历对象自身的和继承的可枚举的属性。
            Object.keys()：返回对象自身的所有可枚举的属性的键名。
            JSON.stringify()：只串行化对象自身的可枚举的属性。
            Object.assign()： 忽略enumerable为false的属性，只拷贝对象自身的可枚举的属性。
        >ES6 一共有 5 种方法可以遍历对象的属性。

            （1）for...in
            for...in循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。

            （2）Object.keys(obj)
            Object.keys返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。

            （3）Object.getOwnPropertyNames(obj)
            Object.getOwnPropertyNames返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。

            （4）Object.getOwnPropertySymbols(obj)
            Object.getOwnPropertySymbols返回一个数组，包含对象自身的所有 Symbol 属性的键名。

            （5）Reflect.ownKeys(obj)
            Reflect.ownKeys返回一个数组，包含对象自身的所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。

            以上的 5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。

            首先遍历所有数值键，按照数值升序排列。
            其次遍历所有字符串键，按照加入时间升序排列。
            最后遍历所有 Symbol 键，按照加入时间升序排列。
    * super 关键字 指向当前对象的原型对象。

            const proto = {
                foo: 'hello'
            };

            const obj = {
                foo: 'world',
                find() {
                    return super.foo;
                }
            };

            Object.setPrototypeOf(obj, proto);
            obj.find() // "hello"
    * 对象的扩展运算符 ...value

            // ...value必须是最后一个位置
            let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
            x // 1
            y // 2
            z // { a: 3, b: 4 }

2. 对象新增的方法
    * `Object.is()` 用来比较两个值是否严格相等

            Object.is('foo', 'foo')
            // true
            Object.is({}, {})
            // false

            与 === 的不同之处：一是+0不等于-0，二是NaN等于自身
            +0 === -0 //true
            NaN === NaN // false
            Object.is(+0, -0) // false
            Object.is(NaN, NaN) // true
    * `Object.assign()` 用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象

            const target = { a: 1 };

            const source1 = { b: 2 };
            const source2 = { c: 3 };

            Object.assign(target, source1, source2);
            target // {a:1, b:2, c:3}
    * `Object.getOwnPropertyDescriptors()` 返回指定对象所有自身属性（非继承属性）的描述对象。
        >ES5 的Object.getOwnPropertyDescriptor()方法会返回某个对象属性的描述对象

            const obj = {
                foo: 123,
                get bar() { return 'abc' }
            };

            Object.getOwnPropertyDescriptors(obj)
            // { foo:
            //    { value: 123,
            //      writable: true,
            //      enumerable: true,
            //      configurable: true },
            //   bar:
            //    { get: [Function: get bar],
            //      set: undefined,
            //      enumerable: true,
            //      configurable: true } }
    * `__proto__属性`，`Object.setPrototypeOf()`，`Object.getPrototypeOf()`
    * `Object.keys()`，`Object.values()`，`Object.entries()`

            let {keys, values, entries} = Object;
            let obj = { a: 1, b: 2, c: 3 };

            for (let key of keys(obj)) {
                console.log(key); // 'a', 'b', 'c'
            }

            for (let value of values(obj)) {
                console.log(value); // 1, 2, 3
            }

            for (let [key, value] of entries(obj)) {
                console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
            }
    * `Object.fromEntries()` 方法是Object.entries()的逆操作，用于将一个键值对数组转为对象。

            Object.fromEntries([
                ['foo', 'bar'],
                ['baz', 42]
            ])
            // { foo: "bar", baz: 42 }