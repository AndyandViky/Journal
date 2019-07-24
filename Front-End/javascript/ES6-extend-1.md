# ES6相关扩展 (一)

1. 字符串的扩展
    * codePointAt() 能够正确处理 4 个字节储存的字符，返回一个字符的码点。

            let s = '𠮷a';
            s.codePointAt(0).toString(16) // "20bb7"
            s.codePointAt(2).toString(16) // "61"
    * String.fromCodePoint() 用于从码点返回对应字符
 
            String.fromCodePoint(0x20BB7)
            // "𠮷"
    * 字符串的遍历器接口 这种方法能够正确处理双字，for...of循环遍历。

            for (let codePoint of 'foo') {
                console.log(codePoint)
            }
    * includes(), startsWith(), endsWith()

            includes()：返回布尔值，表示是否找到了参数字符串。
            startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
            endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。
            
            let s = 'Hello world!';

            s.startsWith('world', 6) // true
            s.endsWith('Hello', 5) // true
            s.includes('Hello', 6) // false
    * repeat() 返回一个新字符串，表示将原字符串重复n次。

            'x'.repeat(3) // "xxx"
            'hello'.repeat(2) // "hellohello"
            'na'.repeat(0) // ""
    * padStart()，padEnd() 字符串补全长度的功能

            'x'.padStart(5, 'ab') // 'ababx'
            'x'.padStart(4, 'ab') // 'abax'

            'x'.padEnd(5, 'ab') // 'xabab'
            'x'.padEnd(4, 'ab') // 'xaba'
    * 模板字符串 是增强版的字符串，用反引号（`）标识

            `${x} + ${y * 2} = ${x + y * 2}`
    * String.raw() 返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串

2. 数值的扩展
    * 二进制和八进制表示法
    * Number.isFinite() 用来检查一个数值是否为有限的, Number.isNaN()
    * Number.parseInt(), Number.parseFloat()
    * Number.isInteger() 
        >数值存储为64位双精度格式，数值精度最多可以达到 53 个二进制位(1 个隐藏位与 52 个有效位）,超出时会产生误判
    * Number.EPSILON 是 JavaScript 能够表示的最小精度
    * 安全整数和 Number.isSafeInteger()
    * Math 对象的扩展

            Math.trunc() 方法用于去除一个数的小数部分
            Math.sign() 判断一个数到底是正数、负数、还是零
            Math.cbrt() 用于计算一个数的立方根。
            Math.clz32() 方法将参数转为 32 位无符号整数的形式，然后这个 32 位值里面有多少个前导 0。
            Math.imul() 方法返回两个数以 32 位带符号整数形式相乘的结果
            Math.fround() 方法返回一个数的32位单精度浮点数形式。
            Math.hypot() 方法返回所有参数的平方和的平方根
            Math.expm1(x) 返回 ex - 1 即Math.exp(x) - 1。
            Math.log1p(x) ****方法返回1 + x的自然对数，即Math.log(1 + x)。如果x小于-1，返回NaN。
            Math.log10(x) 返回以 10 为底的x的对数。如果x小于 0，则返回 NaN。
            Math.log2(x) 返回以 2 为底的x的对数。如果x小于 0，则返回 NaN。
    * 指数运算符

            let a = 1.5;
            a **= 2;
            // 等同于 a = a * a;

            let b = 4;
            b **= 3;
            // 等同于 b = b * b * b;
            // V8 引擎的指数运算符与Math.pow的实现不相同，对于特别大的运算结果，两者会有细微的差异。

3. 函数的扩展
    * 函数参数的默认值
    * rest 参数  ...values
    * 严格模式
    * name 属性
    * 箭头函数
    * 双冒号运算符
    * 尾调用优化
    * 函数参数的尾逗号
  
4. 数组的扩展
    * es5数组方法： `https://www.cnblogs.com/z-dl/p/8242960.html`
       * Array.isArray()  方法用来判断一个值是否为数组。它可以弥补typeof运算符的不足
       * valueOf() 方法返回数组本身
       * toString() 方法返回数组的字符串形式
       * push() 方法用于在数组的末端添加一个或多个元素，并返回添加新元素后的数组长度。注意，该方法会改变原数组。
       * pop() 方法用于删除数组的最后一个元素，并返回该元素。注意，该方法会改变原数组。
       * join() 方法以参数作为分隔符，将所有数组成员组成一个字符串返回。如果不提供参数，默认用逗号分隔。
       * concat() 方法用于多个数组的合并。它将新数组的成员，添加到原数组成员的后部，然后返回一个新数组，原数组不变。
       * shift() 方法用于删除数组的第一个元素，并返回该元素。注意，该方法会改变原数组。
       * unshift() 方法用于在数组的第一个位置添加元素，并返回添加新元素后的数组长度。注意，该方法会改变原数组。
       * reverse() 方法用于颠倒数组中元素的顺序，返回改变后的数组。注意，该方法将改变原数组。
       * slice() 方法用于提取原数组的一部分，返回一个新数组，原数组不变。
       * splice() 方法用于删除原数组的一部分成员，并可以在被删除的位置添加入新的数组成员，返回值是被删除的元素。注意，该方法会改变原数组。splice的第一个参数是删除的起始位置，第二个参数是被删除的元素个数。如果后面还有更多的参数，则表示这些就是要被插入数组的新元素。
       * sort() 方法对数组成员进行排序，默认是按照字典顺序排序。排序后，原数组将被改变。
       * map() 方法对数组的所有成员依次调用一个函数，根据函数结果返回一个新数组。map方法接受一个函数作为参数。该函数调用时，map方法会将其传入三个参数，分别是当前成员、当前位置和数组本身。
       * forEach() 方法与map方法很相似，也是遍历数组的所有成员，执行某种操作，但是forEach方法一般不返回值，只用来操作数据。如果需要有返回值，一般使用map方法。
       * filter() 方法的参数是一个函数，所有数组成员依次执行该函数，返回结果为true的成员组成一个新数组返回。该方法不会改变原数组。
       * some()，every() 这两个方法类似“断言”（assert），用来判断数组成员是否符合某种条件。
       * reduce()，reduceRight()reduce方法和reduceRight方法依次处理数组的每个成员，最终累计为一个值。
          >-reduce：是从左到右处理（从第一个成员到最后一个成员）
          >
          >-reduceRight：则是从右到左（从最后一个成员到第一个成员），其他完全一样。
       * indexOf()，lastIndexOf() 
          >indexOf 方法返回给定元素在数组中第一次出现的位置，如果没有出现则返回-1。
          >
          >lastIndexOf 方法返回给定元素在数组中最后一次出现的位置，如果没有出现则返回-1。
    * 扩展运算符  ...[2, 3, 4]
    * Array.from()
        >Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。

            let arrayLike = {
                '0': 'a',
                '1': 'b',
                '2': 'c',
                length: 3
            };

            // ES5的写法
            var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

            // ES6的写法
            let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
    * Array.of() Array.of方法用于将一组值，转换为数组。

            Array.of(3, 11, 8) // [3,11,8]
            Array.of(3) // [3]
            Array.of(3).length // 1
    * 数组实例的 copyWithin()
        >数组实例的copyWithin方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员）

            -target（必需）：从该位置开始替换数据。如果为负值，表示倒数。
            -start（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示倒数。
            -end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数。
            [1, 2, 3, 4, 5].copyWithin(0, 3) // [4, 5, 3, 4, 5]
    * 数组实例的 find() 和 findIndex()
        >数组实例的find方法，用于找出第一个符合条件的数组成员

            [1, 4, -5, 10].find((n) => n < 0) // -5
        >数组实例的findIndex方法的用法与find方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1。

            [1, 5, 10, 15].findIndex(function(value, index, arr) {
                return value > 9;
            }) // 2
    * 数组实例的 fill() fill方法使用给定值，填充一个数组。

            ['a', 'b', 'c'].fill(7)
            // [7, 7, 7]

            new Array(3).fill(7)
            // [7, 7, 7]
    * 数组实例的 entries()，keys() 和 values()
        >ES6 提供三个新的方法——entries()，keys()和values()——用于遍历数组。它们都返回一个遍历器对象（详见《Iterator》一章），可以用for...of循环进行遍历，唯一的区别是keys()是对键名的遍历、values()是对键值的遍历，entries()是对键值对的遍历。

            for (let index of ['a', 'b'].keys()) {
                console.log(index);
            }
            // 0
            // 1

            for (let elem of ['a', 'b'].values()) {
                console.log(elem);
            }
            // 'a'
            // 'b'

            for (let [index, elem] of ['a', 'b'].entries()) {
                console.log(index, elem);
            }
            // 0 "a"
            // 1 "b"
    * 数组实例的 includes() 表示某个数组是否包含给定的值

            [1, 2, 3].includes(2)     // true
            [1, 2, 3].includes(4)     // false
            [1, 2, NaN].includes(NaN) // true
    * 数组实例的 flat()，flatMap() 
        >flat()用于将嵌套的数组“拉平”，变成一维的数组。该方法返回一个新数组，对原数据没有影响。
 
            [1, 2, [3, 4]].flat() // [1, 2, 3, 4]
            [1, 2, [3, [4, 5]]].flat(2) // [1, 2, 3, 4, 5], 参数2代表拉平两层
        >flatMap()方法对原数组的每个成员执行一个函数（相当于执行Array.prototype.map()），然后对返回值组成的数组执行flat()方法。该方法返回一个新数组，不改变原数组， 默认只能展开一层。
  
            函数原型:
                arr.flatMap(function callback(currentValue[, index[, array]]) {
                    // ...
                }[, thisArg])
            方法的参数是一个遍历函数，该函数可以接受三个参数，分别是当前数组成员、当前数组成员的位置（从零开始）、原数组。
            第二个参数，用来绑定遍历函数里面的this
    * 数组的空位 
        >ES6 明确将空位转为undefined。

            Array.from(['a',,'b']) // [ "a", undefined, "b" ]
            // 由于空位的处理规则非常不统一，所以建议避免出现空位。