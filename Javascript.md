## 面向对象

### 特性

- 封装性
- 继承性
- 多态性

### 优点

易维护，容复用，易扩展，由于面向对象有封装性、继承、多态性的特性，可以设计出低耦合的系统，使系统，更加灵活，更加维护

### 缺点

性能比面向过程低

思维特点

1. 抽取（抽象）对象共用的属性和行为组织（封装）成一个类（模板）
2. 对类进行实例化，获取类的对象

面向对象编程我们考虑的是有那些对象，按照面向对象的思维特点，不断的创建对象，使用对象，指挥对象做事情.

### 对象

在 Javascript 中，对象是一组无序的相关属性，所有的事物都是对象，例如：字符串、数值、数组、函数等.

对象是由属性和方法组成的：

- 属性：属性的特性，在对象中用属性来表示（常用名词）
- 方法：事物的行为，在对象中用方法来表示（常用动词）


## 构造函数

### 概述

在典型的 OOP 的语言中 如（java），都存在类的概念，类就是对象的模板，对象就是类的实例，但在 ES6之前，JS 中并没有引入类的概念

ES6，全称 ECMAScript 6.0 ，2015.06 发版，但是目前浏览器的 JavaScript 是 ES5 版本，大多数高版本的浏览器也支持 ES6，不过只是实现了 ES6 的部分特性和功能

在 ES6 之前，对象不是基于类创建的，而是用一种称为 构造函数 的特殊函数来定义对象和它们的特征

构造函数是一种特殊的函数，主要是来初始化对象，即为对象成员变量赋初始值，它总于 new 一起使用，我们可以把对象中一些公共的属性和方法抽取出来，如何封装到这个函数里面

在 JS 中，使用构造函数时要注意以下两点

1. 构造函数用于创建某一类对象，其首字母要大写
2. 构造函数要和 new 一起使用 才有意义

```js
function Star(name, age) {
        this.name = name;
        this.age = age
        this.send = function () {
           console.log(this.name + this.age);
        }
    }
let ldh = new Star('刘德华',18);
console.log(ldh);
```

JavaScript 的构造函数可以添加一些成员，可以在构造函数本身上添加，也可以在构造函数内部的 this 上添加，通过这两种方法添加的成员，分别称为 静态成员，和实例成员

静态成员：在构造函数本身上添加的成员称为静态成员，只能由构造函数本身来访问

实例成员：在构造函数内部创建的对象成员称为实例成员，只能由实例化对象来访问	

### 原型

构造函数通过原型分配的函数是所有对象所共享的

JavaScript 规定，每一个构造函数都有一个 prototype 属性，指向另一个对象，注意这个 **prototype**  就是一个对象，这个对象的所有属性和方法，都会被构造函数所拥有

我们可以把那些不变的方法，直接定义在 prototype 对象上，这样所有的对象实例就可以共享这些方法，就解决了**构造函数浪费内存**的问题

```js
   function Star(name, age) {
            this.name = name;
            this.age = age
        }
        Star.prototype.send = function name() {
            console.log('发送成功！');
        }
        let ldh = new Star('刘德华', 18);
        ldh.send()
```

### 对象原型 __proto__

对象都会有一个属性 __proto__ 指向构造函数的 prototype 原型对象，之所以我们对象可以使用构造函数 prototype 原型对象的属性和方法，就是因为 有 __proto__ 原型的存在

- __proto__ 对象原型和原型对象 prototype  是等价的
- __proto__ 对象原型的意义就是在于为对象的查找机制提供一个方向，或者说是一条线路，但是它是一个非标准属性，因此在实际开发中，不可以使用这个属性，它只能内部指向原型对象 prototype 

![image-20210608105335161](C:\Users\xiaoke\AppData\Roaming\Typora\typora-user-images\image-20210608105335161.png)

### constructor 构造函数

原型对象 ( __proro__ ) 和构造函数 ( prototype ) 原型对象里面都有一个属性 constructor 属性，constructor 我们称为构造函数，因为它指回构造函数本身

constructor 主要用于记录该对象引用于那个构造函数，它可以让原型对象重新指向原来的构造函数

### 原型链

![image-20210608160510212](C:\Users\xiaoke\AppData\Roaming\Typora\typora-user-images\image-20210608160510212.png)



### 成员查找机制

1. 当访问一个对象的属性（包括方法时），首先查找这个对象自身有没有该属性
2. 如果没有就查找它的原型（也就是 __proto__ 指向 prototype 原型对象）
3. 如果还没有就查找原型对象的原型（Object 的原型对象）
4. 依次类推一直找到 Object 为止（ null ）
5. __proto__ 对象原型的意义就在于为对象成员查找提供一个方向，或者说一条路线

### 扩展内置对象

可以提供原型对象，对原来的内置对象进行扩展自定义的方法，比如给数组增加自定义求偶数和的功能

```js
        // 扩展内置对象
        Array.prototype.sum = function () {
            let sum = 0;
            for (let i = 0; i < this.length; i++) {
                sum += this[i];
            }
            return sum;
        }
        var arr = [5, 8, 9, 10, 20, 60];
        console.log(arr.sum());
```

### 继承

ES6之前并没有给我们提供 extends 继承，我们可以通过 构造函数+原型对象 模拟实现继承，被称为组合继承

#### call

1. 可以调用这个函数
2. 可以改变这个函数的 this 指向

```js
        function Father(uname, age) {
            this.uname = uname;
            this.age = age;
        }
        Father.prototype.send = function () {
            console.log('发送成功！');
        }
        function Son(uname, age) {
            Father.call(this, uname, age)
        }
        Son.prototype = new Father();
        Son.prototype.constructor = Son;
        Son.prototype.login = function () {
            console.log('登陆成功!');
        }

        let son = new Son('子构造函数', 15);
        son.send()
        console.log(son);
```

## ES5 新增方法

### 数组方法

#### forEach()

```js
array.forEach((item, index, arr) => { })
```

- item 数组当前项的值
- index 数组的当前索引
- arr 数组对象本身

#### filter()

```js
let arr = [1, 2, 5, 6, 9, 10]
let arr2 = arr.filter((item, index, arr) => {
    return item > 5;
})
console.log(arr2);
```

- filter() 方法 创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素，主要用于筛选数组
- 注意它直接返回一个新数组

#### some()

- 使用方法和上面相同
- some() 方法用于检测数组中的元素是否满足指定条件，查找数组是否有满足条件的元素
- 注意它返回的值布尔值，如果查找到这个元素，就返回 true ，否则就返回 false
- 如果找的第一个满足条件的元素，则终止循环，不在继续循环

### 字符串方法

#### trim()

```javascript
str.trim();
```

- 删除字符串两端的空白字符，中间的字符不受影响
- trim() 方法并不影响原字符串本身，它返回的是一个新的字符串

### 对象方法

#### Object.keys()

```js
Object.keys(obj)
```

- 效果类似 for in
- 返回应该由属性名组成的数组

#### Object.defineProperty()

```js
Object.defineProperty(obj,prop,descriptor)
```

- 定义对象中的新属性或修改原有的属性
- obj 目标对象 必须
- prop 需要定义或修改的属性名字 必须
- descriptor 目标属性所拥有的特性 以对象形式 传递
  1. value 设置属性的值 默认为 undefined
  2. writable 值是否可以重写 true | false 默认为 false
  3. enumerable 目标属性是否可以被枚举，true | false 默认为 false
  4. configurable 目标属性是否可以被删除或是否可以再次修改特性， true | false 默认为 false

## this 指向

这些 this 的指向，是当我们调用函数的时候确定的，调用方式的不同决定了 this 的指向不同

一般指向我们调用者

| 调用方式     | this 指向                                  |
| ------------ | ------------------------------------------ |
| 普通函数调用 | window                                     |
| 构造函数调用 | 实例对象，原型对象里面的方法也指向实例对象 |
| 对象方法调用 | 该方法所属对象                             |
| 事件判断方法 | 判断事件对象                               |
| 定时器函数   | window                                     |
| 立即执行函数 | window                                     |

### 改变函数内部的 this 指向

#### apply()

```js
fun.apply(thisArg,[argsArray]);
```

- thisArg 在 fun 函数运行时指定的 this 值
- argsArray 传递的值，必须包含在数组里面
- 返回值就是函数的返回值，因为它就是在调用函数

#### bind()

```
fun.bind(thisArg,arr1,arr2);
```

- thisArg 在fun函数运行时指定的 this 值
- arg1,arg2 传递的其他参数
- 返回由指定的 this 值和初始化参数改造的原函数拷贝

## 严格模式

JavaScript 除了提供正常模式外，还提供了严格模式，ES5 的严格模式是采用具有限制性 JavaScript 变体的一种方式，即在严格的条件下运行 JavaScript 代码

严格模式对正确的 JavaScript 语义做了一些更改

1. 消除了  JavaScript 语法的一些不合理、不严谨之处、减少了一些怪异行为
2. 消除代码运行的一些不安全之处，保证代码运行的安全
3. 提高编译器效率，增加运行速度
4. 禁用了在，ECMAScript 的未来版本中可能会定义的一些语法，为未来新版本的 JavaScript 做好铺垫，比如说保留字有：class、enum、export、extends、import、super 不能做变量名

### 开启

严格模式可以应用到整个脚本或个别函数中，因此在使用时，我们可以将严格模式分为 为脚本开启严格模式和为函数开始严格模式两种情况

### 为脚本开启严格模式

有的 script 基本是严格模式，有的 script 脚本是正常模式，这样不利用文件合并，所以可以将整个脚本文件放一个立即执行的匿名函数之中，这样独立创建一个作用域而不影响其他的script脚本

```js
<script>
(function)(){
    "use strict"
})();
</script>
```

### 为函数开启严格模式

要给某个函数开启严格模式，需要把 " use strict "; 声明放在函数体使用语句之前

### 规则

1. 变量必须先声明才能使用
2. 不能随意删除已经声明好的变量
3. 全局作用域中函数中的 this 是 undefined
4. 规则函数不加 new 调用，this 指向会报错
5. 定时器里面的 this 指向还是 window
6. 函数中不能有重名的参数
7. 函数必须声明在顶层，新版本的 JavaScript 会引入 块级作用域 ，为了于新版本接轨，不允许在非函数的代码内声明函数

## 闭包

闭包指有权访问另一个函数作用域中变量的函数

简单理解就是，一个作用域可以访问另一个函数内部的变量

```js
 // 闭包应用-计算打车价格 
        // 打车起步价13(3公里内),  之后每多一公里增加 5块钱.  用户输入公里数就可以计算打车价格
        // 如果有拥堵情况,总价格多收取10块钱拥堵费
        // function fn() {};
        // fn();
        var car = (function() {
            var start = 13; // 起步价  局部变量
            var total = 0; // 总价  局部变量
            return {
                // 正常的总价
                price: function(n) {
                    if (n <= 3) {
                        total = start;
                    } else {
                        total = start + (n - 3) * 5
                    }
                    return total;
                },
                // 拥堵之后的费用
                yd: function(flag) {
                    return flag ? total + 10 : total;
                }
            }
        })();
        console.log(car.price(5)); // 23
        console.log(car.yd(true)); // 33

        console.log(car.price(1)); // 13
        console.log(car.yd(false)); // 13
```



## 递归

如果一个函数在内部可以调用本身，那么这个函数就是递归函数

递归函数的作用和循环效果一样

由于递归容易发生 栈溢出 错误 stack overflow 所以必须加退出条件 return

```js
  var data = [{
            id: 1,
            name: '家电',
            goods: [{
                id: 11,
                gname: '冰箱',
                goods: [{
                    id: 111,
                    gname: '海尔'
                }, {
                    id: 112,
                    gname: '美的'
                }, ]
            }, {
                id: 12,
                gname: '洗衣机'
            }]
        }, {
            id: 2,
            name: '服饰'
        }];
        // 我们想要做输入id号,就可以返回的数据对象
        // 1. 利用 forEach 去遍历里面的每一个对象
        function getID(json, id) {
            var o = {};
            json.forEach(function(item) {
                // console.log(item); // 2个数组元素
                if (item.id == id) {
                    // console.log(item);
                    o = item;
                    // 2. 我们想要得里层的数据 11 12 可以利用递归函数
                    // 里面应该有goods这个数组并且数组的长度不为 0 
                } else if (item.goods && item.goods.length > 0) {
                    o = getID(item.goods, id);
                }

            });
            return o;
        }
        console.log(getID(data, 1));
        console.log(getID(data, 2));
        console.log(getID(data, 11));
        console.log(getID(data, 12));
        console.log(getID(data, 111));
```

## 浅拷贝

只是拷贝一层，更深层次对象级别的只拷贝引用

```js
Object.assign(o,obj)
```

## 深拷贝

拷贝多层，每一级别的数据都会拷贝

```js
        // 深拷贝拷贝多层, 每一级别的数据都会拷贝.
        var obj = {
            id: 1,
            name: 'andy',
            msg: {
                age: 18
            },
            color: ['pink', 'red']
        };
        var o = {};
        // 封装函数 
        function deepCopy(newobj, oldobj) {
            for (var k in oldobj) {
                // 判断我们的属性值属于那种数据类型
                // 1. 获取属性值  oldobj[k]
                var item = oldobj[k];
                // 2. 判断这个值是否是数组
                if (item instanceof Array) {
                    newobj[k] = [];
                    deepCopy(newobj[k], item)
                } else if (item instanceof Object) {
                    // 3. 判断这个值是否是对象
                    newobj[k] = {};
                    deepCopy(newobj[k], item)
                } else {
                    // 4. 属于简单数据类型
                    newobj[k] = item;
                }

            }
        }
        deepCopy(o, obj);
        console.log(o);

        var arr = [];
        console.log(arr instanceof Object);
        o.msg.age = 20;
        console.log(obj);
```

## ES6

### 类 class

在 Es6 中新增加了类的概念，可以使用 class 关键字声明一个类，之后这个类来实例化对象

类抽取了对象的公共部分，它泛指某一大类（class）

对象特指某一个，通过类实例化一个具体的对象

创建类

```js
class name {
	// class body
}
```

创建实例

```js
var xx  = new name();
```

注意：类必须使用 new 实例化对象

类 constructor 构造函数

constructor () 方法是类的构造函数（默认方法），用于传递参数，返回实例对象，通过 new 命令生成对象实例时，自动调用该方法，如果没有显示定义，类内部会自动给我们创建一个 constructor()

#### 继承

语法

```js
class Father {} 父类
class Son extends Father {} 子类 
```

#### super 关键字

super 关键字用于访问和调用对象父类上的函数，可以调用父类的构造函数，也可以调用父类的普通函数

```javascript
 class Father {
            constructor() {
                console.log('我是父类');
            }
            money() {
                console.log(100);
            }
        }
        class Son extends Father {
            constructor() {
                super(); // 调用父类的构造函数
                console.log('我是子类');
            }
        }
        new Son().money()
```

### 变量

#### let

ES6 中新增的用于声明变量的关键字

- let 声明的变量只在所处于的块级有效
- 不存在变量提升
- 暂时性死区

#### const

声明常量，常量就是指（内存地址）不能变化的量

- 具有块级作用域
- 声明常量必须赋值
- 赋值后值不能修改（对应的内存地址不能修改）

#### 区别

1. 使用 var 声明的变量，其作用域为该语句所在的函数内，且存在变量提升现象
2. 使用 let 声明的变量，其作用域为该语句所在的代码块内，不存在变量提升
3. 使用 const 声明的常量，在后面出现的代码中不能再修改该常量的值

### 解构赋值

ES6中允许从数组中提取值，按照对应位置，对变量赋值，对象也可以实现解构

#### 数组解构

```js
let [a,b,c]  = [1,2,3];
console.log(a) // 1
console.log(b) // 2
console.log(c) // 3
```

如果解构不成功，变量的值为 undefined

### 对象解构

```js
let person = { name:"xiaoke", age:18 };
let {name , age}  = person;
console.log(name); // xiaoke
console.log(age);  // 18
let { name:myName, age:myAge } = person; // myName myAge 属于别名
console.log(myName) // xiaoke
console.log(myAge) // 18
```

### 箭头函数

```js
const fn =  (name,age) => {
    console.log(name,age)
}
console.log(fn('xiaoke',18)) // xiaoke 18
```

函数体只有一句代码，且代码的执行结果就是返回值，可以省略大括号

```js
const fn = (num1,num2) => num1 + num2 ;
console.log(fn(10,20))  // 30
```

如果形参只有一个，可以省略小括号

```js
const fn = num => num;
console.log(fn(10)); // 10
```

<span style="color:#E02433">注意：箭头函数不绑定 this 关键字，箭头函数中的 this，指向的是函数定义位置的上下文 this</span>

### 剩余参数

剩余参数语法允许我们将一个不定数量的参数表示为一个数组

```js
function sum (first, ...args) {
    console.log(first) // 10
    console.log(args) // [20,30]
};
sun(10,20,30);
```

剩余参数和解构配合使用

```js
let students = ['wangwu','zhangsan','lisi'];
let [s1,...s2] = students;
console.log(si); // wangwu 
console.log(s2); // ['zhangsan','lisi']
```

### 扩展运算符

扩展运算符可以将数组或对象转为用逗号分隔的参数序列

```js
let ary = [1,2,3];
..ary // 1,2,3
console.log(..ary) // 1 2 3
```

扩展运算符可以应用于合并数组

```js
let ary1 = [1,2,3];
let ary2 = [3,4,5];
let ary3 = [...ary1,...ary2];
console.log(ary3) // [1,2,3,4,5];
// 方式 2 
ary1.push(...ary2);
console.log(ary1) // [1,2,3,4,5];
```

可以将伪数组或可遍历对象转换为真正的数组 （方法一）

```js
let oDivs = document.getElementsByTagName("div");
oDivs = [...oDivs];
```

### Array 扩展方法

#### from()

可以将伪数组或可遍历对象转换为真正的数组 （方法二）

方法还可以接收第二个参数，作用类似于数组的 map方法，用来对每个元素进行处理，将处理后的值放入返回的数组

```js
let arrayLike = {
    '0' : 'a',
    '1' : 'b',
    '2' : 'c',
    'length' : 3
};
let arr2 = Array.from(arrayLike); // ['a','b','c'] 
// let arr2 = Array.from(arrayLike, item=> item* 2  );
```

#### find()

用于找出第一个符合条件的数组成员，如果没有找到就返回 undefined

```js
let ary = [{
    id: 1,
    name: '张三'
},
{
    id: 2,
    name: '李四'
}];
// 查找 id 为 2 的对象
let target  = ary.find(item=> item.id == 2 )
```

#### findIndex()

用于找出第一个符合条件的数组成员的索引，如果没有找到就返回 -1

```js
let ary  = [1,5,10,15];
let index = ary.findIndex(item=> item > 9 )
console.log(index) // 2 索引
```

#### includes()

表示某个数组是否包含给定的值，返回布尔值

```js
[1,2,3].includes(3) // true
[1,2,3].includes(5) // false
```

### String 扩展方法

#### 模板字符串

ES6新增的创建字符串的方式，使用反引导定义

- 可以解析变量
- 可以换行
- 可以调用函数

```js
const fn = () => '你好啊!' ;
let msg = 'Hello world';
let str = `张三说：${msg},${fn()}`;
console.log(str) // 张三说：Hello world,你好啊!
```

#### startsWith()

表示参数字符串是否在原字符串的头部，返回布尔值

```js
let str = 'Hello World!';
str.startsWith('Hello')  // true
str.startsWith('!')  // false
```

#### endsWith()

表示参数字符串是否在原字符串的尾部，返回布尔值

```js
let str = 'Hello World!';
str.endsWith('Hello')  // false
str.endsWith('!')  // true
```

#### reprat()

将原字符串重复 n 次，返回一个新字符串

```js
'x'.repeat(3); // xxx
'hello'.reqeat(3) // hellohellohello
```

### Set 数据结构

ES6 提供了新的数据结构 Set ，它类似于数组，但是成员的值是唯一的，没有重复的值

Set 本身是一个构造函数，用来生成 Set 数据结构

```js
const s = new Set();
```

Set 函数可以接收一个数组作为参数，用来初始化

```js
const set = new Set([5,10,30,15,20]);
```

利用 Set 数据结构，进行数组去重

```js
const ary = [1, 1, 3, 5, 4, 6, 6, 5, 4];
const ary1 = new Set(ary);
let ary3 = [...ary1]
console.log(ary3); //  [1, 3, 5, 4, 6]
```

#### 实例方法

1. add( value ) 添加某个值，返回 Set结构本身
2. delete( value )  删除某个值，返回一个布尔值，表示是否删除成功
3. has ( value ) 返回一个布尔值，表示该值是否是 Set 的成员
4. clear() 清除所有成员，没有返回值

#### 遍历

Set 结构的实例于数组一样，也拥有 forEach方法，用于对每个成员执行某种操作，没有返回值

```jsf
s.forEach(item=> console.log(item));
```

### Map  数据结构

ES6 提供了 Map 数据集合，数一种以键值对存储的有序列表（具有唯一性）

```js
let map = new Map()
```

Map  函数可以接收一个二维数组作为参数，用来初始化

```js
let map = new Map([
    ['name', 'xiaoke'],
    ['age', '18']
])
console.log(map); // Map(2) {"name" => "xiaoke", "age" => "18"}
```

#### 实例方法

- set() 方法用添加新的以键值对存储的有序列表，返回值的 Map本身，所以可以多次调用
- has ( value ) 返回一个布尔值，表示该值是否是 Map 的成员
- delete() 删除某个值，返回一个布尔值，表示是否删除成功
- clear() 清空

#### 遍历

```js
map.forEach((item, key, m) => {
    console.log(key + '-' + item);
})
```

### 代理 Proxy 

1. 什么是代理？即：给目标对象封装一层拦截，外界访问必须先通过这层拦截
2. 举个例子：猎头招聘，你自己发布招聘信息会暴露自身信息，而通过中介则安全的多

语法

```js
// 目标对象
let obj = {
    name: "Mr.xiaoke",
    age: 18,
    sex: '男'
}
// 创建一个代理，参数1 拦截的目标对象，参数2 拦截的行为
// 参数2 如果是 空对象，代理会直接调用目标对象
let p = new Proxy(obj, {
    // get 方法用于拦截某个属性的读取操作
    // 这里直接 return 通过代理对象无论访问目标对象的任何属性都是 fail
    get(target, property) {
        return 'fail'
    },
    // set 可以拦截某个属性的赋值操作
    // 目标的数据也会被修改
    set(target, property, value) {
        // 不允许赋值
        // return false
        // 允许赋值 但是会修改原数据
        // target[property] = value
    }
});
console.log(p.name);
```

### Module 模块化

ES6 支持模块化设计，像能其它语言一样，可以使用导入导出的功能

1. 我们首先，创建一个要被导入的模块 module.js，具体如下

   ```js
   export let name = 'Mr.Lee'; //导出这个变量
   ```

2. 再创建一个普通的.js 文件，比如 25.js，然后导入 module.js； 

   ```js
   import {name} from './module.js'; 
   console.log(name);
   ```

3.  最后一步，在.html 文件要加载 25.js 文件，才有小，注意 type 格式

   ```js
   <script type="module" src="js/25.js"></script>
   // PS：注意，这种导入导出的方式属于 ES6 模块，仅支持浏览器模式
   ```

4.  除了导出变量、常量之外，还可以导出函数、类等功能； 

   ```js
   // module.js 内容
   export let name = 'Mr.Lee';
   export function sum(x, y) { return x + y; }
   export class Person { constructor(name) { this.name = name; }
   run() { return 'name : ' + this.name; }
   }
   // 25.js 内容
   import {name, sum, Person} from './module.js';
   console.log(name); console.log(sum(10, 20)); console.log((new Person('Mr.Lee')).run());
   ```

5. 也支持使用*号，将所有导出的内容全部加载进来； 

   ```js
   import * as module from './module.js';
   console.log(module.name); 
   console.log(module.sum(10, 20)); 
   console.log((new module.Person('Mr.Lee')).run());
   ```

6. 支持别名设定，设定别名后，源名即失效了； 

   ```js
   import {name as user} from './module.js'; 
   console.log(user); //name无效了
   ```

7. 统一导出方案，不需要再每一个导出的内容设置 export； 

   ```js
   export { name, sum, Person }
   ```

8. 可以给导出设置一个默认值，导入部分就可以不用花括号了；

   ```js
   export default name;
   import name from './module.js'; 
   import name, {sum, Person} from './module.js';
   ```

#### Node 加载 

Node 下有自己的导出和导入的加载模式：CommonJS 规范； 

```js
let name = 'Mr.Lee';
module.exports = { name : name, };
```

```js
const name = require('./common.js'); 
console.log(name);
```

