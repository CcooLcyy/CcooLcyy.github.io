---
title: JavaSrcipt
date: 2021-06-27 23:06:35
tags: JS
---

这一部分是廖雪峰官网的JavaScript全栈教程，在此感谢大神的教程。

# 什么JavaScript（后称JS）

[toc]

js是在浏览器运行的脚本语言，是一种解释型语言。

js可以直接嵌在网页的任何地方，但是我们通常将代码放到`<head>`中。或者单独写一个.js的文件，然后通过

```html
<script src=""></script>
```

进行引入。通常我们非常建议使用包含代码文件的方式进行代码的使用，这样我们可以在多个网页中复用我们的代码。

同时需要注意的是，在标签中可能有个属性是`type=""`这里没必要写，因为浏览器默认的就是js

# 语法

语句以每个分号结尾，语句块用{}，尽量让所有的语句都有分号。

## 赋值

`var`

## 注释

`//`

# 数据类型和变量

js能够处理的数据，但是不仅仅是数字，也包括文本或者其他东西。

## Number

js不区分整型或者浮点型。

Number可以直接进行四则运算和模运算。

## 字符串

字符串可以使用单引号或者是双引号括起来的任何文本。

## 布尔值

布尔值只有两个值

`true`

``false`

同时我们还要逻辑运算

```js
&&	//与运算
||	//或运算
!	//非运算
```

## 比较运算符

可以用于对Number进行比较。

比较的结果是一个布尔值

### 是否相等

js中有两种比较，

一种是`==`这种会自动转换数据类型然后进行比较

另一种是`===`，这种情况不会自动转换数据类型，但是如果数据类型不同的时候会返回`false`，只有当数据类型是相同的时候才会进行比较

### 是否不相等

`!==`用于测试两个值是否不相等，返回的是一个布尔类型。

### 浮点数比较

和其他语言类似，浮点数，尤其是无限不循环小数在计算机中无法被正确表示，只能计算他们之差的绝对值。

### NaN

这个Number比较特别，他与任何值都不相等，包括他自己，也就是说

```js
NaN === NaN;
//false
```

唯一能判断NaN的方法是通过函数`isNaN()`

```js
isNaN(NaN); //true
```

## null

表示空，与`''`和0不同，他就代表没有，0是一个数字，而`''`代表长度为0的字符串。

## undefined

通常情况下，这个东西没什么用，但是他是有意义的。undefined仅仅在判断函数仓鼠是否传递的情况下才有用。

## 变量

变量名可以是大小写英文，数字`$_`的组合，不能用数字开头，不能是js的关键字，这些在其他语言中都有类似的要求。

赋值使用`=`进行。`var`只是声明一个变量，

### 静态语言与动态语言。

主要区别就是在于，变量的类型是否固定，顾名思义，固定的就是静态语言，不固定的就是静态语言。在js中我们声明了一个变量之后并没有固定他的数据类型。但是像在c或者是java中他是有固定的类型的，在Java中数据类型赋值错误会报错的。

## strict模式

在一开始js并不要求必须使用`var`来声明变量，但是不适用`var`来声明变量的话会导致变量被定义为全局变量，但是一旦出现变量重名的情况，问题解决起来会很麻烦。

通过`var`声明的变量只是局部变量，这种变量离开函数就消失。

我们可以通过开启strict模式来强制检查是否使用`var`来定义了变量。

方法就是在js代码的第一行写上

```js
'use strict';
```

# 字符串

```js
// 使用'' 或者""括起来的都是字符串
// 如果句子中既有’又有”那么可以用转义字符来表示
'I \'m \"OK\";
```

## 多行字符

在最新标准下可以使用

```js
`多行
字符
的
写法`;
```

多行字符可以使用反引号表示。

## 模板字符串

有很多字符串变量，可以使用加号进行链接，但是如果有很多需要填写我们可以使用模板来进行替换。

```js
var name = 'Daniel';
var age = '20';
var message = `你好，${name}，你今年${age}岁了！`；
```

## 操作字符串

### 获取字符串长度

我们可以使用`.length`来获取字符串长度。

```js
var s = 'hello, world';
s.length;
```

因为字符串本质就是一个字符串数组，他也同样可以通过数组来进行访问。

同时我们需要注意的就是，字符串数组不可以被赋值。

js提供了很多操作字符串的方法，但是我们调用这些方法的时候并不会改变字符串内容，会返回一个新的字符串。

### toUpperCase()

讲一个字符串全部变为大写

### toLowerCase()

将所有的字符变为小写。

### indexOf()

搜索指定的字符串出现的位置。返回一个数字，如果没有找到返回-1

```js
var s = 'hello, world';
s.indexOf('world'); // 返回7
s.indexOf('World'); // 没有找到指定的子串，返回-1

```

### substring()

与上一个函数相反，这个函数是通过数字来索引这区间的字符串。

```js
var s = 'hello, world'
s.substring(0, 5); // 从索引0开始到5（不包括5），返回'hello'
s.substring(7); // 从索引7开始到结束，返回'world'
```

# 数组

数组是按照一定顺序排列的集合，每个集合的值都称为元素。

一个数组实际上也是一个对象，他的每个元素的索引被视为一个属性。

js中数组可以是任意数据类型的。

```js
[1, 2, 3.14, 'hello', null, true];
```

用`[]`来表示数组，并用`,`来间隔数组

## 创建数组

```js
//通过函数的方式来创建数组
new Array(1,2,3);
```

通过定义的方式创建数组。

```js
var arr = [1,2,3,4];
```

数组的索引和其他语言也很类似，从0开始数

`arr[0]`其实是第一个元素。

## 返回数组的长度

直接使用.length方法

```js
var arr = [1,2,3,4];
arr.length
```

对数组的返回值进行赋值，会增加数组的元素。同时我们还可以通过索引修改数组的值。

同时，直接通过索引进行赋值会改变数组的大小。

大多数语言不允许直接改变数组的大小，越界进行访问的时候会报错，但是js并不会因为数组越界报错，他会直接扩展数组。

## indexOf()

通过这个函数来搜索指定元素的位置。

同时要注意区分字符串和数字。

```js
var arr = [10, 20, '30', 'xyz'];
arr.indexOf(10); // 元素10的索引为0
arr.indexOf(20); // 元素20的索引为1
arr.indexOf(30); // 元素30没有找到，返回-1
arr.indexOf('30'); // 元素'30'的索引为2
```

## slice()

截取数组的部分元素然后返回一个新的数组。

```js
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];
arr.slice(0, 3); // 从索引0开始，到索引3结束，但不包括索引3: ['A', 'B', 'C']
arr.slice(3); // 从索引3开始到结束: ['D', 'E', 'F', 'G']]
```

包含开始索引但是不包含结束索引。

如果不传递参数，那么将会复制数组。

## push()和pop()

push()将若干数组压入栈，pop()将单个数组弹出栈。

## shift() unshift()

`unshift()`可以向头部加入若干元素。

`shift()`将第一个元素删掉

## sort()

数组排序，直接修改当前元素的位置。

## reverse()

将数组从后往前进行排列。

## splice()

可以删除数组也可以添加数组。

```js
var arr = [1,2,3,4,5,6];
arr.splice(2,3,'7','8');
```

从索引二开始向后删除3个元素，并且从索引2开始插入字符7和字符8。

同时我们可以只添加不删除，只要将向后删除的数量改为0则可。

## concat()

将当前的数组和其他数组链接起来。

concat方法并没有修改数组，只是返回了个新数组

他可以接收任意个元素和数组，就算传入的参数有数组也会合并成为一个数组。

## join()

他可以用特定的字符将各个元素连接起来

```js
var arr = ['A', 'B', 'C', 1, 2, 3];
arr.join('-'); // 'A-B-C-1-2-3'
```

## 多维数组

当数组中还有数组就可以形成一个多维数组。

# 对象

js对象就是一组键值对组合成的无序集合，跟c中的结构体类似，或者是Python中的元组。

对象的键都是字符串类型，但是值可以是任何类型。

```js
var person = {
    name: "Daneil",
    age: 20,
    city: 'Qingdao'
}
```

每一组中间用英文逗号隔开，键值之间用冒号对应。

并且最后一个键值对后面不要加逗号了，低版本IE会报错。

## 引用方法

要获得一个对象的方法可以使用`对象变量.属性名`进行引用。

当属性名有特殊字符的时候。

```js
var xiaoming = {
    name: '小明'，
    'middle-school': 'No.1 Middle School'
}
```

这个'middle-school'并不是一个有效的变量，因此必须要用引号括起来，同时，访问的时候也不能用点来访问

`xiaoming['middle-school']`这也告诉我们，在写变量的时候一定要尽量使用标准的变量名，这样我们可以通过点式来访问。

当我们访问了一个不存在的属性会返回`undefined`虽然不会报错但是会返回未定义。

## 动态

js的对象是动态的，可以自由给一个对象添加和删除对象或属性。

同时我们可以使用`in`来查看某一对象是否拥有某一属性。

```js
var xiaoming = {
    name: '小明',
    birth: 1990,
    school: 'No.1 Middle School',
    height: 1.70,
    weight: 65,
    score: null
};
'name' in xiaoming; // true
'grade' in xiaoming; // false
```

但是我么也要注意，不一定`in`来判断在就一定是，因为可以从别的地方继承。

要判断就是本身拥有的而不是继承得到的要用`hasOwnProperty()`

# 循环控制流

## 条件判断

经典的if判断，太经典了，跟c语言里的差不多。

包括多行条件判断。

## for循环

也非常经典，与c语言中的使用无差。

### for...in

他可以将对象所有的属性依次循环出来。

同时我们可以使用`hasOwnProperty()`来实现过滤继承的属性。

```js
var o = {
    name: 'Jack',
    age: 20,
    city: 'Beijing'
};
for (var key in o) {
    if (o.hasOwnProperty(key)) {
    	console.log(key); // 'name', 'age', 'city'   
    }
}
```

我们不仅可以直接循环对象，也可以直接循环数组。

但是使用`for...in`对数组进行循环之后得到的是String而不是Number。

## while循环与do...while循环

这两个东西没什么新鲜的，都是很经典。

# Map

在js对象默认表示是通过花括号，可以视为其他语言中的map或者dictionary，但是在js中的键必须要求是字符串，尽管将Number作为键也十分合理，因此，在ES6中，引入了最新的Map类型

## 初始化Map

```js
var m = new Map(); // 空Map
m.set('Adam', 67); // 添加新的key-value
m.set('Bob', 59);
m.has('Adam'); // 是否存在key 'Adam': true
m.get('Adam'); // 67
m.delete('Adam'); // 删除key 'Adam'
m.get('Adam'); // undefined
```

一个key只能对应一个value所以多次对key放入value时会替换前面的值。

# Set

与Map类似，但是只存放key，并且不能存储重复的key。我们可以通过数组输入，或者直接创建一个空Set

```js
var s1 = new Set()	//空Set
var s2 = new Sew([1,2,3,4,4,5])	//只有一个4，因为无法放入重复的。
```

我们可以使会用`add(key)`添加新元素到Set中。`delete(key)`删除元素。

# iterable

数组可以使用下标循环，但是Map和Set不行，为了统一集合类型，引入iterable类型，数组，Map和Set都属于iterable类型。

他可以用`for...of`来循环遍历

`for...in`遍历的实际上是对象的属性名称，而不是对应的值，数组的每一个元素也被视为一个属性。当我们用`for...in`循环时

```js
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x in a) {
    console.log(x); // '0', '1', '2', 'name'
}
```

而`for...of`循环的是本身的元素。

在iterable中内置了`forEach`，他接收一个函数每次迭代自动回调该函数

```js
'use strict';
var a = ['A','B','C'];
a.forEach(function (element, index, array) {
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
    console.log(element + ', index = ' + index);
});

```

Set回调函数的前两个参数都是元素本身

```js
'use strict';
var s = new Set(['A', 'B', 'C']);
s.forEach(function (element, sameElement, set) {
    console.log(element);
});
```

Map的回调函数依次为value、key、map本身

同时，因为js的函数调用不要求参数必须一致，因此可以忽略他们。只要array和element。

# 函数

函数定义

```js
function xx(x) {
    
}

var xx = function(x){
    
}
```

第一种情况是函数名为xx指向该函数的变量。

第二种情况是指定了一个匿名函数，但是将这个函数赋值给了xx。通过xx调用函数，这两中方式完全等价。

在调用时只需要按照参数顺序传入参数即可。

同时，如果没有参数传入也不影响，函数会接收到一个undefined，计算结果为NaN。要避免收到undefined可以对函数进行检查。

## argument

利用argument可以获得调用者传入的参数。他的用法和数组很类似，但是并不是一个数组。

最常用的就是用来判断传入参数的个数。

```js
// foo(a[, b], c)
// 接收2~3个参数，b是可选参数，如果只传2个参数，b默认为null：
function foo(a, b, c) {
    if (arguments.length === 2) {
        // 实际拿到的参数是a和b，c为undefined
        c = b; // 把b赋给c
        b = null; // b变为默认值
    }
    // ...
}
```

## rest

rest可以让我们更容易获得额外的参数。

```js
function foo(a, b, ...rest) {
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
// 结果:
// a = 1
// b = 2
// Array [ 3, 4, 5 ]

foo(1);
// 结果:
// a = 1
// b = undefined
// Array []
```

rest参数只能写在最后，前面要用那个...来标识，多余的参数通过数组的形式传入的rest中。

# 作用域和解构赋值

通过var声明的变量是有作用域的。在函数体内声明作用域为这个函数体。

这个有意思的一点就是变量提升

```js
'use strict';

function foo() {
    var x = 'Hello, ' + y;
    console.log(x);
    var y = 'Bob';
}

foo();
```

尽管我们使用了strict模式，但是因为y在后面声明了，可是提升的仅仅是y的声明，并不会提升y的赋值，也就是说，在上面，y仍然为undefined。

## 全局作用域

不在任何函数内定义的变量就是全局作用域，默认有一个全局对象`window`，实际上是被绑定到了`window`上的一个属性。

同时，以变量方式定义的函数也是一个全局变量。

js实际只有一个全局作用域，任何变量包括函数，如果没有在函数作用域中找到，就会向上找，如果在全局作用域中也没找到，那么就返回`ReferenceError`

## 命名空间

如果不同的文件使用了相同的全局变量，或者定义了名字的顶层函数会造成命名的冲突，通常我们会把自己所有的变量和函数绑定到一个全局变量中。

```js
// 唯一的全局变量MYAPP:
var MYAPP = {};

// 其他变量:
MYAPP.name = 'myapp';
MYAPP.version = 1.0;

// 其他函数:
MYAPP.foo = function () {
    return 'foo';
};
```

这样会大大减少全局变量冲突的可能。

## 局部作用域

变量作用域实际是作用在函数内部的，为了解决块级作用域，可以使用`let`，用let替代var可以声明一个块级作用域变量。

```js
'use strict';

function foo() {
    var sum = 0;
    for (let i=0; i<100; i++) {
        sum += i;
    }
    // SyntaxError:
    i += 1;
}
```

## 常量

常量使用`const`来定义，同时他也支持块级作用域

## 解构赋值

对多个变量直接赋值

之前的做法是

```js
var array = ['hello', 'JavaScript', 'ES6'];
var x = array[0];
var y = array[1];
var z = array[2];
```

现在可以

```js
'use strict';

var [x, y, z] = ['hello', 'JavaScript', 'ES6'];

// x, y, z分别被赋值为数组对应元素:
console.log('x = ' + x + ', y = ' + y + ', z = ' + z);

```

对数组元素进行解构赋值的时候多个变量要使用`[]`括起来。并且如果数组本身有嵌套，要注意嵌套层次和位置要保持一致。

```js
let [x, [y, z]] = ['hello', ['JavaScript', 'ES6']];
x; // 'hello'
y; // 'JavaScript'
z; // 'ES6'
```

而且解构还可以忽略对某些元素的赋值

```js
let [, , z] = ['hello', 'JavaScript', 'ES6']; // 忽略前两个元素，只对z赋值第三个元素
z; // 'ES6'
```

从对象中取出若干属性，也能使用解构，可以获取制定的属性。也可以对嵌套对象进行赋值

```js
'use strict';

var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school'
};
var {name, age, passport} = person;
// name, age, passport分别被赋值为对应属性:
console.log('name = ' + name + ', age = ' + age + ', passport = ' + passport);

```

```js
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school',
    address: {
        city: 'Beijing',
        street: 'No.1 Road',
        zipcode: '100001'
    }
};
var {name, address: {city, zip}} = person;
name; // '小明'
city; // 'Beijing'
zip; // undefined, 因为属性名是zipcode而不是zip
// 注意: address不是变量，而是为了让city和zip获得嵌套的address对象的属性:
address; // Uncaught ReferenceError: address is not defined
```

当要使用的变量名和属性名不一致的时候可以使用

`let {name, passport:id} = persion`这样我们就将passport的属性赋值给变量id，同时我们查看passport的值的时候会发现是未定义的。

对没有的属性使用默认值

```js
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678'
};

// 如果person对象没有single属性，默认赋值为true:
var {name, single=true} = person;
name; // '小明'
single; // true
```

当把开头的{当做块的时候，不允许使用=可以使用圆括号括起来解决

`({x, y} = {name = 'x', x: 100, y: 200});`

解构可以简化代码，比如当要交换两个变量的时候

```js
var x = 1, y = 2;
[x, y] = [y, x];
```

快速获取当前页面内的域名和路径

```js
var {hostname:domain, pathname:path} = location;
```

将对象作为函数接收的参数时，可以直接使用解构将对象的属性绑定到对应的变量中。

```js
function buildDate({year, month, day, hour=0, minute=0, second=0}) {
    return new Date(year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':' + second);
}

buildDate([year: 2021, month: 1, day: 1]);
```

# 方法

在对象中绑定函数，这个函数成为这个对象的方法。

```js
var xiaoming = {
    name: '小明',
    birth: 1990,
    age: function () {
        var y = new Date().getFullYear();
        return y - this.birth;
    }
};

xiaoming.age; // function xiaoming.age()
xiaoming.age(); // 今年调用是25,明年调用就变成26了
```

`this`关键字，在方法内部会始终指向当前的对象，`xiaoming`所以调用`this.birth`就等于调用`xiaomimg.birth`。`this`到底指向哪里视情况而定。

如果是以对象为方法调用时，`this`会指向被调用的对象，也就是`xiaomig.age()`中的`xiaoming`但是如果是单独调用函数`this`会指向全局对象，也就是`window`

在strict模式下，让函数的`this`指向undefined，当然这么操作只是将错误暴露出来，并不能解决错误，

```js
'use strict';

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: function () {
        var y = new Date().getFullYear();
        return y - this.birth;
    }
};

var fn = xiaoming.age;
fn(); // Uncaught TypeError: Cannot read property 'birth' of undefined
```

我们可以在age内写一个函数让他包含一个函数，

```js
'use strict';

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: function () {
        function getAgeFromBirth() {
            var y = new Date().getFullYear();
            return y - this.birth;
        }
        return getAgeFromBirth();
    }
};

xiaoming.age(); // Uncaught TypeError: Cannot read property 'birth' of undefined'use strict';
```

这里报错是因为this只有在age方法的函数内指向`xiaoming`这里我们可以在内部函数之外写一个变量捕获`this`

```js
'use strict'
var xiaoming = {
  name: '小明',
  birth: 1990,
  age: function() {
    var that = this;
    function getAgeFromBirth() {
      var y = new Date().getFullYear();
      return y - that.birth;
    }
    return getAgeFromBirth();
  }
}
xiaoming.age();
```

通过`that`捕获`this`之后我们就可以解决这个问题。

## 通过apply()控制this指向

apply()接收的第一个参数需要绑定能的this变量，第二个参数是数组。便是函数本身的参数。

```js
function getAge() {
    var y = new Date().getFullYear();
    return y - this.birth;
}

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: getAge
};

xiaoming.age(); // 25
getAge.apply(xiaoming, []); // 25, this指向xiaoming, 参数为空
```

另一个和apply类似的方法是用`call()`，区别是`apply()`通过把参数打包生array在传入，`call()`将直接把参数按顺序传入。

当对于普通的函数调用的时候，我们会将`this`绑定为`null`

## 装饰器

利用`apply`来改变函数的行为。js的所有函数即使是内置的函数，我们也可以重新指向新的函数。

当我们想知道代码调用了多少次函数的时候，我们可以将所有的函数找出加上`count +=1`当然我们也可以直接对函数加解释器。

```js
'use strict';

var count = 0;
var oldParseInt = parseInt; // 保存原函数

window.parseInt = function () {
    count += 1;
    return oldParseInt.apply(null, arguments); // 调用原函数
};

```

# 高阶函数































