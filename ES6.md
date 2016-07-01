##ES6 入门
##2.let 和 const
###let命令
ES6新增了**let**命令，用来声明变量。它的用法类似于var，但是所声明的变量，只在let命令所在的代码块内有效。
```
{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
```
不存在变量提升
```
console.log(foo); // 输出undefined
console.log(bar); // 报错ReferenceError

var foo = 2;
let bar = 2;
```
只要块级作用域内存在let命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。
let不允许在相同作用域内，重复声明同一个变量。
###const命令
**const**声明一个只读的常量。一旦声明，常量的值就不能改变。
常量foo储存的是一个地址，这个地址指向一个对象。不可变的只是这个地址，即不能把foo指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。
```
const foo = {};
foo.prop = 123;

foo.prop
// 123

foo = {}; // TypeError: "foo" is read-only

//另一个例子
const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // 报错
```
如果真的想将对象冻结，应该使用Object.freeze方法。
```
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
//除了将对象本身冻结，对象的属性也应该冻结。下面是一个将对象彻底冻结的函数。
var constanize = (obj) => {
    Object.freeze(obj);
    Object.keys(obj).forEach((key, value) => {
        if(typeof obj[key] === 'object'){
            constanize(obj[key]);
        }
    })
}
```

为了保持兼容性，var命令和function命令声明的全局变量，依旧是全局对象的属性；另一方面规定，let命令、const命令、class命令声明的全局变量，不属于全局对象的属性。也就是说，从ES6开始，全局变量将逐步与全局对象的属性脱钩。

##3.变量的解构赋值
```
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```

```
只要某种数据结构具有Iterator接口，都可以采用数组形式的解构赋值。
1.数组
var [v1, v2, ..., vN ] = array;
let [v1, v2, ..., vN ] = array;
const [v1, v2, ..., vN ] = array;
2.Set结构
let [x, y, z] = new Set(["a", "b", "c"]);
x // "a"
3. genegate Iterator 接口函数
function* fibs() {
  var a = 0;
  var b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

var [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5
上面代码中，fibs是一个Generator函数，原生具有Iterator接口。解构赋值会依次从这个接口获取值。
理解上述函数：
function* foo(){
  var index = 0;
  while (index <= 2)
    yield index++;
}
var iterator = foo();
console.log(iterator.next()); // { value: 0, done: false }
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: undefined, done: true }

来自 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield)

//允许赋值默认值
var [foo = true] = [];
foo // true

[x, y = 'b'] = ['a']; // x='a', y='b'
[x, y = 'b'] = ['a', undefined]; // x='a', y='b'

当一个值 '==='' 于下为undefined时 才可取默认值， null时会被赋值为null。

//可用于对象
var { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

var { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined


let { log, sin, cos } = Math;
//Math对象中的log,sin,cos属性被赋值出去
let {length : len} = 'hello';
len // 5 ---类似 字符串对象的length属性
```

###用途
```
1.交换变量的值
[x, y] = [y, x];
2.从函数返回多个值
// 返回一个数组

function example() {
  return [1, 2, 3];
}
var [a, b, c] = example();

// 返回一个对象

function example() {
  return {
    foo: 1,
    bar: 2
  };
}
var { foo, bar } = example();
3.函数参数的定义
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
4.提取JSON数据
var jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
5.函数参数的默认值
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
}) {
  // ... do stuff
};
```


##4.字符串的扩展
