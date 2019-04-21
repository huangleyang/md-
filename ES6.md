# ES6

## 1.变量 let和const

### let

let 用来声明变量 类似于var 

#### 不存在变量名提升

​	但声明的变量只在所在的代码块内有效(不存在变量名提升)
例如在for循环计时器中for(let i =0) 声明的i只能在for循环内有效 而且for(let i =0)和for循环for(let i =0) {let i=1这里面的作用是不同的 在里面}  

```
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```



#### 暂时性死区 

​	在没有let命令yuan变量以前 在声明之前都是yuan的“死区”


		例子var tmp = 123;

	if (true) {
	  tmp = 'abc'; // ReferenceError
	  let tmp;
	}
#### 不予许重复声明

let在同一作用域中不允许重复声明同一变量

```
// 报错
function func() {
  let a = 10;
  var a = 1;
}

// 报错
function func() {
  let a = 10;
  let a = 1;
}
```



#### 块级作用域


​	
		为什么需要块级作用域呢？ ES5只有全局变量和函数作用域 ，没有块级作用域 会有什么多不合理地方
		例如1.内层变量可能会覆盖 外层变量
		    2.用来计数的循环变量 泄露为全局变量
	ES6的块级作用域
	let实际上为javaScript新增了块级作用于吧
	
	例子
		function f1() {
			let n = 5;
			if (true) {	
			let n = 10;
			}
		    console.log(n); // 5
			}
	
	ES6中允许块级作用域的任意嵌套
	
	ES6的块级作用域允许函数的规则，只在使用大括号的情况下成立，如果没有大括号 ，就会报错
### const命令

#### 声明

```
const 声明一个只读的常量 。一旦声明，常量的值就不能改变 且不能声明了不赋值
	const PI = 3.1415;
	PI // 3.1415
	PI = 3; //分配到恒定变量
	let和count两个的作用域是相同的(只在声明的块级作用域有效) 也有暂时性死区 和let差不多 （就是不能重新赋值）
```

#### const变量的指向

```

	const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。
	所以count中的值是可以改变的 但变量的指向是不会变的
	const a = [];
	a.push('Hello'); // 可执行
	a.length = 1;    // 可执行
	console.log(a) // ["Hello"]
	a = ['Dave']; //b报错
	可以进行添加但不可以赋值因为指向是不会改变的
	
```

## 2.变量的解构赋值

#### 1.基本语法

​	以前，为变量赋值，只能直接指定值

```
let a = 1;
let b = 2;
let c = 3;
```

​	新的变量的赋值 

```
	let[a,b,c] = [1,2,3]//a=1 b=2 c= 3
	let[a,,c] = [1,2,3] //a=3 c=3;
	let[x,y,...z] = ["a"] //
	x = "a"	 y=undefined  z=[]
	如果解构不成功 ，变量的值就等于 undefined
	例如 let [foo] = [];
	let [bar, foo] = [1] //bar = 1
	以上两种解构 foo的值都等于undefined  不完全解构
```

​	特例【报错】

```
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
以上语句全部都会报错 ，因为等号右边的值，要么转化为对象以后不具备Iterator(可迭代的，迭代器) （前五个表达式），要不就是不具备Iterator接口(最后一个)
```

#### Iterator接口

```
遍历器（Iterator）是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署Iterator接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。
let [x, y, z] = new Set(['a', 'b', 'c']);
x // "a"
事实上，只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值
```

#### 默认值

``` 
解构赋值允许指定默认值
let [foo = true] = [];
foo // true
let [foo = true] = []
let [x, y = 'b'] = ['a',undefined] //x = 'a' y = 'b'
注意，ES6中内服使用的严格相等运算符（===） ，判断一个位置是否有值，所以，只有当一个数组成员严格等于undefied，默认值才会生效

let [x = 1] = [undefined];
x // 1
let [x = 1] = [null];
x // null
意思就是只有当赋值为===undefined时 默认值才会生效
```

​	惰性求值：当默认值是一个表达式时，这个表达式是惰性求值的，即只有在用到的时候，才会求值

```
function f () {
  console.log('a')
}
let [x = f()] = [1]
上面代码，只有是x取值的时候在会执行f函数
```

​	默认值可以引用解构函数的其他变量，但该变量必须声明

```
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2 因为这个 付给y的值不是undefined
let [x = y, y = 1] = [];     // ReferenceError: y is not defined
```

#### 2.对象的解构赋值

不仅只有数组可以解构 对象也可以

```
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。
let { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined
因为第二个属性值没有取到，所以为undefined
还可这样写
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world' 但是属性名必须得匹配 
foo是匹配的模式，baz才是变量。真正被赋值的是变量baz，而不是模式foo。

```

​	对象也可以设置默认值 条件也是严格等于undefined

```
var {x = 3} = {};
x // 3

var {x, y = 5} = {x: 1};
x // 1
y // 5

var {x: y = 3} = {1,2};
x// 1
y // 2

var {x: y = 3} = {x: 5};
y // 5
```

#### 3.字符串的赋值

```
字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。
let {length : len} = 'hello';
len // 5
```

#### 4.函数参数的解构赋值

```
[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ] 将数字当做 一个参数来解析
```

#### 5.不能使用圆括号

```
// 全部报错
let [(a)] = [1];

let {x: (c)} = {};
let ({x: c}) = {};
let {(x: c)} = {};
let {(x): c} = {};

let { o: ({ p: p }) } = { o: { p: 2 } };
```

## 3.字符串的扩展

#### 1.字符的Unicode表示法

​	Javascript允许用\uxxxx形式表示一个字符串，其中xxxx表示字符的Unicode码点。

```
"\u0061"
// "a"
ES6中可以使用
"\u{20BB7}"
// "𠮷"

'\u{1F680}' === '\uD83D\uDE80'
// true
大括号可以表示多个字节 十六进制存在
'\z' === 'z'  // true
'\172' === 'z' // true
'\x7A' === 'z' // true
'\u007A' === 'z' // true
'\u{7A}' === 'z' // true
```

#### 2.字符串的遍历接口

​	ES6中为字符串添加了遍历接口(Iterator) 使的字符串恶意被for ---of遍历

```
for(let codePoint of 'foo') {
	console.log(codePoint)
	//'f'
	//'0
	//'0
}
```

#### 3.padStart() padEnd 补全

​	在ES2017引入了字符串补全长度的功能。如果某个字符串不够指定长度，可以在头部或者尾部补全

```
其中x是元字符串 使用padStart 的第二个参数进行补全
头部补全
	'x'.padStart(5, 'ab') // 'ababx'
	'x'.padStart(4, 'ab') // 'abax'
尾部补全
	'x'padEnd(5,'ab') // 'xabab'
	'x'.padEnd(4, 'ab') // 'xaba'
如果原字符串的长度，等于或大于最大长度，则字符串补全不生效，返回原字符串。
	'xxx'.padStart(2, 'ab') // 'xxx'
	'xxx'.padEnd(2, 'ab') // 'xxx'
如果第二个参数不写 默认使用空格来补全长度
	'x'.padStart(4) // '   x'
	'x'.padEnd(4) // 'x   '
用途
	'1'.padStart(10, '0') // "0000000001"
	'12'.padStart(10, '0') // "0000000012"
	'123456'.padStart(10, '0') // "0000123456"
另一个用途是提示字符串格式
	'12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
	'09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
```

#### 4.startsWith() endsWith

startsWith() 判断字符串以某某开头 返回值为true false ，endsWith()判断字符串以某某结尾返回值为true 和false

```
let str = '你是猪吗'
console.log(str.startsWith('你'))
//true
console.log(str.endsWith('吗'))
//true
```

#### 

## 4.正则的扩展方法

#### 1.RegExp构造函数

​	第一种情况 第一个参数字符串 第二个参数表示正则表达式的修饰符

```
var regex = new RegExp('ysh','i')
推荐这样写
var regex = /ysh/i
其中i是忽略大小写
m是进行多行匹配
g是全局搜索
```

#### 2.正则方法【重点】

```
test()匹配 
RegExpObject.test(string) 返回值 匹配返回一个布尔值

exec()提取
RegExpObject.exec(string) 返回值 返回一个数组， 其中存放匹配的结果。如果未找到匹配，则返回值为null
```

#### 3.字符串的正则方法

​	字符串一个有4个方法 可以使用正则表达式

​	字符串方法中都不可能改变字符串 **因为字符串是不可变的**

```
regexp 这个正则表达式的意思
第一个参数是字符串 第二个参数 是正则表达式

math()  查找
stringObject.match(regexp) 在stringObject字符串中检索指定的值
找到一个或多个正则匹配的值

replace() 替换
 stringObj.replace(regexp/substr,replacement)
 第一个参数：必需。字符串中要替换的子串或正则RexExp；
 第二个参数：必需，一个字符串值，规定了替换文本或生成替换文本的函数
 返回值：注意它的返回值是一个新的字符串，并没有更改原有字符串，是用 replacement 替换了 regexp 的第一次匹配或所有匹配之后得到的
 
 search() 检索
 stringObject.search(regexp)
 返回值： stringObject 中第一个与 regexp 相匹配的子串的起始位置
		如果没有找到任何匹配的子串，则返回 -1。
		
split() 切割
stringObject.split(separator,howmany)
第一个参数 字符串或者正则 从这个指定的地方进行切割
第二个参数是长度 可以不写
```

#### 4.具名组匹配

​	正则表达式使用圆括号进行匹配。

```
使用exec方法，就可以将这三组匹配结果提取出来。
const RE_DATE = /(\d{4})-(\d{2})-(\d{2})/;

const matchObj = RE_DATE.exec('1999-12-31');
const year = matchObj[1]; // 1999
const month = matchObj[2]; // 12
const day = matchObj[3]; // 31
```

#### 5.循环取出多项匹配成功的值 String.prototype.matchAll 

​	ES5中的方法

```

var regex = /t(e)(st(\d?))/g;
var string = 'test1test2test3';

var matches = [];
var match;
while (match = regex.exec(string)) {
  matches.push(match);
}

matches
// [
//   ["test1", "e", "st1", "1", index: 0, input: "test1test2test3"],
//   ["test2", "e", "st2", "2", index: 5, input: "test1test2test3"],
//   ["test3", "e", "st3", "3", index: 10, input: "test1test2test3"]
// ]

前四项是 正则匹配成功返回的值 index是这想开始的索引 input是匹配的字符
```

​	ES6 中新增的方法 只是一个提案 没有推广

​	String.prototype.matchAll方法，可以一次性取出所有匹配。不过，它返回的是一个遍历器（Iterator），而不是数组。

​	实际操作中matchAll()  ---  报错 matchAll()不是一个函数 

​	解决方法 未知

```
const string = 'test1test2test3';

// g 修饰符加不加都可以
const regex = /t(e)(st(\d?))/g;

for (const match of string.matchAll(regex)) {
  console.log(match);
}
// ["test1", "e", "st1", "1", index: 0, input: "test1test2test3"]
// ["test2", "e", "st2", "2", index: 5, input: "test1test2test3"]
// ["test3", "e", "st3", "3", index: 10, input: "test1test2test3"]

```

​	数组中Array.from方法可以将遍历器转化为数组

```
// 转为数组方法一
[...string.matchAll(regex)]

// 转为数组方法二
Array.from(string.matchAll(regex));
```

## 5.数值的扩展

#### 1.Number.isFinite(),Number.isNaN()

​	在ES6

​	新增了Number.isFInite()用来检查一个数值是否为有限的  返回值时一个布尔类型

​	**注意**必须是一个数值，不然返回的是false

​	Number.isNaN()判断是不是NaN

```
Number.isFinite(15); // true
Number.isFinite(0.8); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false
Number.isFinite('foo'); // false
Number.isFinite('15'); // false 字符串不是数值的所以是false
Number.isFinite(true); // false
```

#### 2.Number.isNaN()判断是不是NaN 【】

​	模板化 易辨别 （可以不写）

```
ES6 将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变
// ES5的写法
parseInt('12.34') // 12
parseFloat('123.45#') // 123.45

// ES6的写法
Number.parseInt('12.34') // 12
Number.parseFloat('123.45#') // 123.45

这样做的目的，是逐步减少全局性方法，使得语言逐步模块化。
Number.parseInt === parseInt // true
Number.parseFloat === parseFloat // true
```

#### 3.判断是不是整数Number.isInteger()

​	返回值时布尔类型

```
Number.isInteger(25) // true
Number.isInteger(25.1) // false

在JavaScript内部，整数和浮点数采用的是同样的储存方式 所以25和25.0是同一个值
Number.isInteger(25) // true
Number.isInteger(25.0) // true
```

​	**注意点**

​	

```
注意，由于 JavaScript 采用 IEEE 754 标准，数值存储为64位双精度格式，数值精度最多可以达到 53 个二进制位（1 个隐藏位与 52 个有效位）。如果数值的精度超过这个限度，第54位及后面的位就会被丢弃，这种情况下，Number.isInteger可能会误判。

Number.isInteger(3.0000000000000002) // true
上面代码中，Number.isInteger的参数明明不是整数，但是会返回true。原因就是这个小数的精度达到了小数点后16个十进制位，转成二进制位超过了53个二进制位，导致最后的那个2被丢弃了
```

#### 4.Math.sign()判断数字类型

​	有五中返回值

​		参数为正数的时候 ， 返回+1

​		参数为负数的时候 ， 返回-1

​		参数为0时候 ， 返回 0

​		参数为-0 ， 返回-0

​		其他值， 返回NaN

```
Math.sign(-5) // -1
Math.sign(5) // +1
Math.sign(0) // +0
Math.sign(-0) // -0
Math.sign(NaN) // NaN
```

​	Math.sign()存在隐式转换

```
Math.sign('')  // 0
Math.sign(true)  // +1
Math.sign(false)  // 0
Math.sign(null)  // 0
Math.sign('9')  // +1
Math.sign('foo')  // NaN
Math.sign()  // NaN
Math.sign(undefined)  // NaN
```

**`Math.hypot`方法返回所有参数的平方和的平方根**

**`Math.cbrt`方法用于计算一个数的立方根**



## 6.函数的扩展

#### 1.函数参数的默认值

​	ES6中允许函数形参设置默认值 惰性求值

```
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello

简介了语法
function Point(x = 0, y = 0) {
  this.x = x;
  this.y = y;
}

const p = new Point();
p // { x: 0, y: 0 }
```

**注意** 设置参数的默认值后不能再次声明 因为他会默认在函数内部声明

```
function foo(x = 5) {
  let x = 1; // error
  const x = 2; // error
}
```

使用参数默认值时不能使用同名参数

```
// 不报错
function foo(x, x, y) {
  // ...
}

// 报错
function foo(x, x, y = 1) {
  // ...
}
// SyntaxError: Duplicate parameter name not allowed in this context
```

#### 2.函数的length属性

函数的length长度是没有默认值的参数的个数 (当参数有默认值的时候，参数就不算函数的长度了)

```
(function (a) {}).length // 1
(function (a = 5) {}).length // 0
(function (a, b, c = 5) {}).length // 2
```

**注意** 如果设置了默认值的参数不是尾参数，默认值之后的参数也不算length长度

```
(function (a = 0, b, c) {}).length // 0
(function (a, b = 1, c) {}).length // 1
```

**作用域**

一旦设置了参数默认值。函数进行声明初始时，参数会形成一个单独的作用域。等初始化结束，作用域就会消失  

```
var x = 1;
function f(x, y = x) { //这个x创建了一个作用域所以y先指向作用域中的x而不是全局的x
  console.log(y);
}
f(2) // 2
```

#### 3.rest参数

​	ES6 引入了rest参数 (形式为*...变量名*)，用于获取函数的多余参数 ，这样就不需要使用arguments对象了。rest参数搭配的变量是一个数组，该变量将多余的参数放入数组中

```
function add(...values) { //...values 代表形参 无论传几个
  let sum = 0;
  for (var val of values) { //of遍历
    sum += val;
  }
  return sum;
}

add(2, 5, 3) // 10
```

​	下面是一个 rest 参数代替`arguments`变量的例子。

```
// arguments变量的写法
function sortNumbers() {
  return Array.prototype.slice.call(arguments).sort();
}

// rest参数的写法
const sortNumbers = (...numbers) => numbers.sort(); 
//sort()排序 里面是一个函数 
```

**注意**  rest参数之后不能再有其他参数（即只能是最后一个参数），否则会报错

​	还有就是函数的length长度不算..values

```
// 报错
function f(a, ...b, c) {
  // ...
}
```

#### 4.name属性

函数的name属性，返回该函数的函数名

```
function foo() {}
foo.name // "foo"
```

Function构造函数返回的函数实例，name属性的值为anonymous

```
(new Function).name // "anonymous"
```

如果bing返回的函数， name属性值会加上bound前缀。

```
function foo() {};
foo.bind({}).name // "bound foo"

(function(){}).bind({}).name // "bound "
```

#### 5.箭头函数 （this指向一直指向该函数）

ES6允许使用“箭头”（=>）定义函数

```
var f = v => v;

// 等同于
var f = function (v) {
  return v;
};
```

如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数部分

```
声明 函数名 = 形参 => 返回值
var f = () => 5;
// 等同于
var f = function () { return 5 };

var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};
```

如果箭头函数的代码块部分多出一条语句，就要使用大括号将它们包起来，并且使用return语句返回

```
var sum = (num1, num2) => { return num1 + num2; } 
```

​	那么问题来了如果代码块中的是对象怎么办呢

​	当代码块中有对象的时候  对象用中括号包裹

```
// 报错
let getTempItem = id => { id: id, name: "Temp" };

// 不报错
let getTempItem = id => ({ id: id, name: "Temp" });
```

### 使用注意点

箭头函数有几个使用注意点。

（1）函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象。

（2）不可以当作构造函数，也就是说，不可以使用`new`命令，否则会抛出一个错误。

（3）不可以使用`arguments`对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

（4）不可以使用`yield`命令，因此箭头函数不能用作 Generator 函数

**this指向问题**  不能被 bind() call() apply()改变this指向

```
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

var id = 21;

foo.call({ id: 42 }); //默认调用的window 如果this指向改变的话 this.id=21.
// id: 42
```

上面代码中，`setTimeout`的参数是一个箭头函数，这个箭头函数的定义生效是在`foo`函数生成时，而它的真正执行要等到 100 毫秒后。如果是普通函数，执行时`this`应该指向全局对象`window`，这时应该输出`21`。但是，箭头函数导致`this`总是指向函数定义生效时所在的对象（本例是`{id: 42}`），所以输出的是`42`。





这个中的this指向都是foo  

```
function foo() {
  return () => {
    return () => {
      return () => {
        console.log('id:', this.id);
      };
    };
  };
}

var f = foo.call({id: 1});

var t1 = f.call({id: 2})()(); // id: 1
var t2 = f().call({id: 3})(); // id: 1
var t3 = f()().call({id: 4}); // id: 1
```

#### 不适用的场合

​	由于箭头函数使得`this`从“动态”变成“静态”，下面两个场合不应该使用箭头函数

第一个场合就是定义函数方法的时候，且该方法内部包括this

```
const cat = {
  lives: 9,
  jumps: () => {
    this.lives--;
  }
}
```

第二个是需要动态this的时候

```
var button = document.getElementById('press');
button.addEventListener('click', () => {
  this.classList.toggle('on');
}); //报错
```

因为 button的监听函数是一个箭头函数，导致里面的this指向为window。（且不会改变）

**斐波那契数列**

```
function Fibonacci (n) {
  if ( n <= 1 ) {return 1};

  return Fibonacci(n - 1) + Fibonacci(n - 2);
}

Fibonacci(10) // 89
Fibonacci(100) // 堆栈溢出
Fibonacci(500) // 堆栈溢出
```

## 7.数组的扩展

#### 1.扩展运算符

​	扩展运算符（spread）是三个点（`...`）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。

```
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5
```

该运算符主要用于函数调用

```
function add(x, y) {
  return x + y;
}
const numbers = [4, 38];
add(...numbers) // 42
```

​	扩展运算符与正常的函数参数可以结合使用

```
function f(v, w, x, y, z) { }
const args = [0, 1];
f(-1, ...args, 2, ...[3]);
```

扩展运算符后面还可以放置表达式

```
const arr = [
  ...(x > 0 ? ['a'] : []), //三元表达式
  'b',
];
```

如果扩展运算符后面是一个空数组，则不产生任何效果。



**注意** 扩展运算符如果放在括号中，JavaScript引擎就会认为这是函数调用，如果不是函数调用，就会报错

```
(...[1, 2])
// Uncaught SyntaxError: Unexpected number

console.log((...[1, 2]))
// Uncaught SyntaxError: Unexpected number

console.log(...[1, 2])
// 1 2
```

**扩展运算符可以将数组转化为函数的参数**

```
	例子
// ES5 的写法
Math.max.apply(null, [14, 3, 77])

// ES6 的写法
Math.max(...[14, 3, 77])
 
// 等同于
Math.max(14, 3, 77);
//77 Math.max求最大值

运用...扩展符可以直接添加数组
// ES5的 写法
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
Array.prototype.push.apply(arr1, arr2);

// ES6 的写法
let arr1 = [0, 1, 2];
let arr2 = [3, 4, 5];
arr1.push(...arr2);
```

##### 克隆

在ES6中提供了简介复制数组的方法

```
const a1 = [1, 2];
// 写法一
const a2 = [...a1]; 理解第一种即可
// 写法二
const [...a2] = a1; 
```

##### 合并数组

```
const arr1 = ['a', 'b'];
const arr2 = ['c'];
const arr3 = ['d', 'e'];

// ES5 的合并数组
arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]
concat（）连接两个或者多个数组
// ES6 的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]
```

**注意**

这两种都是浅拷贝

#### 2.深拷贝 

1. 当value是**基本数据类型**，比如String，Number，Boolean时，是可以使用拓展运算符进行深拷贝的。比如：
2. 但是，当value是**引用类型**，比如Object，Array，这时使用拓展运算符进行深拷贝，得到的结果是和深拷贝的概念有矛盾的。主要是因为引用类型进行深拷贝也只是拷贝了引用地址。比如：.
3. 【value是属性值 】

第一种是递归 

```
function deepClone(obj){
    let objClone = Array.isArray(obj)?[]:{};
    if(obj && typeof obj==="object"){
        for(key in obj){
            if(obj.hasOwnProperty(key)){
                //判断ojb子元素是否为对象，如果是，递归复制
                if(obj[key]&&typeof obj[key] ==="object"){
                    objClone[key] = deepClone(obj[key]);
                }else{
                    //如果不是，简单复制
                    objClone[key] = obj[key];
                }
            }
        }
    }
    return objClone;
}    
let a=[1,2,3,4],
    b=deepClone(a);
a[0]=2;
console.log(a,b);
```

第二种是运用JSON中的parse和stringify方法

```
function deepClone(obj){
    let _obj = JSON.stringify(obj),
        objClone = JSON.parse(_obj);
    return objClone
}    
let a=[0,1,[2,3],4],
    b=deepClone(a);
a[0]=1;
a[2][0]=1;
console.log(a,b);
```

第三种 运用jq中的方法

```
let a=[0,1,[2,3],4],
    b=$.extend(true,[],a);
a[0]=1;
a[2][0]=1;
console.log(a,b);4
```

##### 与结构复制结合

```
const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

const [first, ...rest] = [];
first // undefined
rest  // []

const [first, ...rest] = ["foo"];
first  // "foo"
rest   // []
```

##### 实现了 Iterator 接口的对象  伪数组转化为数组

可以将伪数组转化为数组

```
let nodeList = document.querySelectorAll('div');
let array = [...nodeList]; 注意这里需要数组括号包裹

使用from方法
let nodeList = document.querySelectorAll('div');
var xixi = Array.from(nodeList)
console.log(xixi)
```

拓展可以使用

##### from方法

```

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
```

#### 3.Array.of()

Array.of方法用于将一组值，转化为数组

```
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```

为什么要有这个方法呢  

为什么不直接使用Array创建一个数组呢

```
Array() // []
Array(3) // [, , ,] length为3
Array(3, 11, 8) // [3, 11, 8]
```

Array只有当参数不少于2个的时候Array（）方法在会将参数组成的新的数组，参数只有一个的时候，实际上是指定数组的长度

#### 4.数组实例的copyWithin（）

有三个参数 替换的意思 参数（索引）

​	第一个（必需） 替换的开始位置

​	第二个（可选） 从哪一个元素开始

​	第三个（可选） 到哪一个结束 不写默认到最后

```
[1, 2, 3, 4, 5].copyWithin(0, 3)
// [4, 5, 3, 4, 5]
// 将3号位复制到0号位
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)
// [4, 2, 3, 4, 5]

// -2相当于3号位，-1相当于4号位
[1, 2, 3, 4, 5].copyWithin(0, -2, -1)
// [4, 2, 3, 4, 5]

// 将3号位复制到0号位
[].copyWithin.call({length: 5, 3: 1}, 0, 3)
// {0: 1, 3: 1, length: 5}
```

#### 5.数组实例的find()和findindex()

find方法 参数是一个回调函数（一般为条件） 所有成员判断是否满足条件

返回值 找到时返回找到的第一个元素 没有找到返回 undefined

```
[1, 4, -5, 10].find((n) => n < 0)
// -5
```

find方法中的回到函数可以接受三个参数， 依次为当前值，当前的位置和原数组

```
[1,5,10,15].find(function(value ,index , arr ){
  return value>9
})
```

数组实例的`findIndex`方法的用法与`find`方法非常类似，返回第一个符合条件的数组成员的位置（索引），如果所有成员都不符合条件，则返回`-1`。

```
[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2
```

这两个方法都可以接受第二个参数，用来绑定回调函数的`this`对象。

```
function f(v){
  return v > this.age;
}
let person = {name: 'John', age: 20};
[10, 12, 26, 15].find(f, person);    // 26 指向
```

#### 6.数组实例的fill()

fill方法使用给定值，填充一个数组

```
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
```

fill有三个参数

​	第一个   填充的元素 不写 就数组都是undefined

​	第二个   开始位置

​	第三个 结束位置

#### 7.数组实例的 entries()，keys() 和 values()

ES6 提供三个新的方法——`entries()`，`keys()`和`values()`——用于遍历数组。它们都返回一个遍历器对象（详见《Iterator》一章），可以用`for...of`循环进行遍历，唯一的区别是`keys()`是对键名的遍历、`values()`是对键值的遍历，`entries()`是对键值对的遍历。



#### 8.数组实例的includes()

`Array.prototype.includes`方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的`includes`方法类似

```
[1, 2, 3].includes(2)     // true
[1, 2, 3].includes(4)     // false
[1, 2, NaN].includes(NaN) // true
```

第二个参数 是起始位置



#### 9.数组实例的flat ，flatMap

flat将嵌套数组进行拉平 二维转化为一维的

flat()默认拉平一次 括号里面可以写拉平的次数

```
[1, 2, [3, 4]].flat() 
// [1, 2, 3, 4]
```

`flatMap()`方法对原数组的每个成员执行一个函数（相当于执行`Array.prototype.map()`），然后对返回值组成的数组执行`flat()`方法。该方法返回一个新数组，不改变原数组。

```
// 相当于 [[2, 4], [3, 6], [4, 8]].flat()
[2, 3, 4].flatMap((x) => [x, x * 2])
// [2, 4, 3, 6, 4, 8]
```

`flatMap()`只能展开一层数组。

#### 10.ES6中空值空值都是用undefined

## 8.对象的扩展

#### 1.属性的简介表示法

ES6允许直接写入变量和函数，作为对象的属性和方法。这样的书写更加简介

属性简写

```
const foo = 'bar';
const baz = {foo};
baz // {foo: "bar"}
其中foo 为键 foo等于的值为对象的值 
（当foo等于的值为对象的时候，输出为 foo：{对象}）
// 等同于
const baz = {foo: foo};
```

方法简写

```
const o = {
  method() {
    return "Hello!";
  }
};
const a = {
getRandom(min,max){  //其中getRandom是字符串 可以写class这样的关键字，不会报错
           return Math.floor(Math.random()*(max-min-1))+min
        }
   }
   console.log(a.getRandom(1,10))
```

#### 2.属性名表达式

JavaScript对象的属性，有两种方法

```
// 方法一
obj.foo = true; //规范 提倡这种写法

// 方法二
obj['a' + 'bc'] = 123;
```

**注意**，属性名表达式如果是一个对象，默认情况下会自动将对象转为字符串`[object Object]`，这一点要特别小心。

```
// 报错
const foo = 'bar';
const bar = 'abc';
const baz = { [foo] };

// 正确
const foo = 'bar';
const baz = { [foo]: 'abc'}; 
//这里会将[foo]自动转换为字符串
```

##### 属性的遍历

ES6 一共有 5 种方法可以遍历对象的属性。

**（1）for...in**

`for...in`循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。

**（2）Object.keys(obj)**

`Object.keys`返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。

**（3）Object.getOwnPropertyNames(obj)**

`Object.getOwnPropertyNames`返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。

**（4）Object.getOwnPropertySymbols(obj)**

`Object.getOwnPropertySymbols`返回一个数组，包含对象自身的所有 Symbol 属性的键名。

**（5）Reflect.ownKeys(obj)**

`Reflect.ownKeys`返回一个数组，包含对象自身的所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。

以上的 5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。

- 首先遍历所有数值键，按照数值升序排列。
- 其次遍历所有字符串键，按照加入时间升序排列。
- 最后遍历所有 Symbol 键，按照加入时间升序排列

#### 3.super关键字

我们知道，this关键字总是指向函数所在的当前对象，ES6又新增了另一个类似的关键字super 指向当前对象的原型对象

```
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
setPrototypeOf（第一个参数  新对象，第二个参数  新对象的原型）
//一般不使用这种方法
```

**注意**  super关键字表示原型对象时，只能用在对象的方法之中，用在其他地方会报错



理解  并且看懂这个

```
const proto = {
  x: 'hello',
  foo() {
    console.log(this.x);
  },
};

const obj = {
  x: 'world',
  foo() {
    super.foo();
  }
}

Object.setPrototypeOf(obj, proto);

obj.foo() // "world"
```

#### 4.对象的扩展运算符

相同的套路再来一遍 （数组）

```
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
x // 1
y // 2
z // { a: 3, b: 4 }
```



## 9.对象的新增方法

#### 1.Object.is() 严格相等于===基本一致

```
Object.is('foo', 'foo')
// true
Object.is({}, {})   
// false 因为对象是一个地址 且地址是一定
```

特例 不同之处只有两个：一是`+0`不等于`-0`，二是`NaN`等于自身。

```
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```

#### 2.Object.assign()

Object.assign方法用于对象的合并，将源对象的所有可枚举属性，赋值到目标对象(target)

```
const target = { a: 1 };

const source1 = { b: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3} 目标对象就是第一个对象
```

如果只有一个参数，`Object.assign`会直接返回该参数。

undefined 和null无法转化为对象 ，所以如果他们作为参数，就会报错

#### 3.Object.getOwnPropertyDecriptors()

ES2017 引入了`Object.getOwnPropertyDescriptors()`方法，返回指定对象所有自身属性（非继承属性）的描述对象。

```
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
```

上面的代码中，Object.getOwnPropertyDecriptors()方法返回的一个对象，所有源对象的属性名都是该对象的属性名，对应的属性值就是该属性的'

#### 4.__proto_ _属性，Object.setPrototypeof(),Object.getPrototype

__proto_  - 属性（前后有两个下划线），用来读取设置当前对象的prototype对象目前，所有浏览器 都部署了这个属性

```
es5的写法
const obj = {
	method: function() {...};
	obj.__proto__ = someOtherObj;
}
es6 的写法
var obj = Object.create(someOtherObj)
```

#### 6.Object.fromEntries() Object.entries() 

Object.entries() 方法返回一个数组，成员是参数对象自身的，所有可遍历属性的键值对数组

```
const obj = { foo: 'bar', baz: 42 };
Object.entries(obj)
// [ ["foo", "bar"], ["baz", 42] ]
```

如果原对象的属性名是一个 Symbol 值，该属性会被忽略。

```
Object.entries({ [Symbol()]: 123, foo: 'abc' });
// [ [ 'foo', 'abc' ] ]
```

`Object.entries`方法的另一个用处是，将对象转为真正的`Map`结构。

```
const obj = { foo: 'bar', baz: 42 };
const map = new Map(Object.entries(obj));
map // Map { foo: "bar", baz: 42 }
```

Object.fromEntries()方法是Object.entries()的逆操作，用于将键值对数组转化为对象

```
Object.fromEntries([
  ['foo', 'bar'],
  ['baz', 42]
])
// { foo: "bar", baz: 42 }
```

该方法的主要目的，是将键值对的数据结构还原为对象，因此特别适合将 Map 结构转为对象。

```
// 例一
const entries = new Map([
  ['foo', 'bar'],
  ['baz', 42]
]);

Object.fromEntries(entries)
// { foo: "bar", baz: 42 }

// 例二
const map = new Map().set('foo', true).set('bar', false);
Object.fromEntries(map)
// { foo: true, bar: false }
```

该方法的一个用处是配合`URLSearchParams`对象，将查询字符串转为对象

```
Object.fromEntries(new URLSearchParams('foo=bar&baz=qux'))
// { foo: "bar", baz: "qux" }
```


## 10.class的基本语法

