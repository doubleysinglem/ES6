##ES6 入门
[TOC]
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

当一个值 '===' 于下为undefined时 才可取默认值， null时会被赋值为null。

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
###模版字符串
```
var a = 5;
var b = 10;
tag`Hello ${ a + b } world ${ a * b }`;
//函数tag依次会接收到多个参数。
function tag(stringArr, value1, value2){
  // ...
}

// 等同于

function tag(stringArr, ...values){
  // ...
}
tag函数所有参数的实际值如下。

    第一个参数：['Hello ', ' world ', '']
    第二个参数: 15
    第三个参数：50

tag(['Hello ', ' world ', ''], 15, 50)
var a = 5;
var b = 10;

function tag(s, v1, v2) {
  console.log(s[0]);
  console.log(s[1]);
  console.log(s[2]);
  console.log(v1);
  console.log(v2);

  return "OK";
}

tag`Hello ${ a + b } world ${ a * b}`;
// "Hello "
// " world "
// ""
// 15
// 50
// "OK"

//用于更深理解的例子
var total = 30;
var msg = passthru`The total is ${total} (${total*1.05} with tax)`;

function passthru(literals) {
  var result = '';
  var i = 0;

  while (i < literals.length) {
    result += literals[i++];
    if (i < arguments.length) {
      result += arguments[i];
    }
  }

  return result;
}

msg // "The total is 30 (31.5 with tax)"
```

##函数的扩展
尾递归优化的实现

尾递归优化只在严格模式下生效，那么正常模式下，或者那些不支持该功能的环境中，有没有办法也使用尾递归优化呢？回答是可以的，就是自己实现尾递归优化。
它的原理非常简单。尾递归之所以需要优化，原因是调用栈太多，造成溢出，那么只要减少调用栈，就不会溢出。怎么做可以减少调用栈呢？就是采用“循环”换掉“递归”。
下面是一个正常的递归函数。
```
function sum(x, y) {
  if (y > 0) {
    return sum(x + 1, y - 1);
  } else {
    return x;
  }
}

sum(1, 100000)
// Uncaught RangeError: Maximum call stack size exceeded(…)
```
上面代码中，sum是一个递归函数，参数x是需要累加的值，参数y控制递归次数。一旦指定sum递归100000次，就会报错，提示超出调用栈的最大次数。
蹦床函数（trampoline）可以将递归执行转为循环执行。
```
function trampoline(f) {
  while (f && f instanceof Function) {
    f = f();
  }
  return f;
}
```
上面就是蹦床函数的一个实现，它接受一个函数f作为参数。只要f执行后返回一个函数，就继续执行。注意，这里是返回一个函数，然后执行该函数，而不是函数里面调用函数，这样就避免了递归执行，从而就消除了调用栈过大的问题。
然后，要做的就是将原来的递归函数，改写为每一步返回另一个函数。
```
function sum(x, y) {
  if (y > 0) {
    return sum.bind(null, x + 1, y - 1);
  } else {
    return x;
  }
}
//上面代码中，sum函数的每次执行，都会返回自身的另一个版本。

现在，使用蹦床函数执行sum，就不会发生调用栈溢出。
trampoline(sum(1, 100000))
// 100001
```
蹦床函数并不是真正的尾递归优化，下面的实现才是。
```
function tco(f) {
  var value;
  var active = false;
  var accumulated = [];

  return function accumulator() {
    accumulated.push(arguments);
    if (!active) {
      active = true;
      while (accumulated.length) {
        value = f.apply(this, accumulated.shift());
      }
      active = false;
      return value;
    }
  };
}

var sum = tco(function(x, y) {
  if (y > 0) {
    return sum(x + 1, y - 1)
  }
  else {
    return x
  }
});

sum(1, 100000)
// 100001
```
上面代码中，tco函数是尾递归优化的实现，它的奥妙就在于状态变量active。默认情况下，这个变量是不激活的。一旦进入尾递归优化的过程，这个变量就激活了。然后，每一轮递归sum返回的都是undefined，所以就避免了递归执行；而accumulated数组存放每一轮sum执行的参数，总是有值的，这就保证了accumulator函数内部的while循环总是会执行。这样就很巧妙地将“递归”改成了“循环”，而后一轮的参数会取代前一轮的参数，保证了调用栈只有一层。

##类
//定义类
```
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}

--------------------------------
class Point {
  constructor(){
    // ...
  }

  toString(){
    // ...
  }

  toValue(){
    // ...
  }
}

// 等同于

Point.prototype = {
  toString(){},
  toValue(){}
};
```

###getter setter
```
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

inst.prop
// 'getter'
```
###class的静态方法
类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。
```
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: undefined is not a function
```
###Class的静态属性和实例属性
静态属性指的是Class本身的属性，即Class.propname，而不是定义在实例对象（this）上的属性。
```
//有效
class Foo {
}

Foo.prop = 1;
Foo.prop // 1
// 以下两种写法都无效
class Foo {
  // 写法一
  prop: 2

  // 写法二
  static prop: 2
}

Foo.prop // undefined
```
目前，只有这种写法可行，因为ES6明确规定，Class内部只有静态方法，没有静态属性。
类的实例属性可以用等式，写入类的定义之中。 用 **"="**即可

###Mixin模式的实现
```
function mix(...mixins) {
  class Mix {}

  for (let mixin of mixins) {
    copyProperties(Mix, mixin);
    copyProperties(Mix.prototype, mixin.prototype);
  }

  return Mix;
}

function copyProperties(target, source) {
  for (let key of Reflect.ownKeys(source)) {
    if ( key !== "constructor"
      && key !== "prototype"
      && key !== "name"
    ) {
      let desc = Object.getOwnPropertyDescriptor(source, key);
      Object.defineProperty(target, key, desc);
    }
  }
}
class DistributedEdit extends mix(Loggable, Serializable) {
  // ...
}
```
上面代码的mix函数，可以将多个对象合成为一个类。使用的时候，只要继承这个类即可。
