---
title: Python
date: 2021-08-28 17:39:54
tags: Python
---
# 字符串

使用单引号或者双引号可以表示一个字符串。

```python
# 单双引号都可以用来表示字符串
s1 = 'hello, world'
s2 = "hello, world"
# 使用三个引号可以直接编辑多行字符串
s3 = '''
hello,
world
'''

print(s1, s2, s3)
```

## 格式化字符串

使用跟C语言类似的格式化方式

```python
a, b = 5, 10
print('%dx%d=%d' %(a, b, a*b))
```

使用字符串提供的方式进行格式化

```python
a, b = 5, 10
print('{0}x{1}={2}'.format(a, b, a*b))
```

在python3.6之后有了更加方便的格式化字符串的方式，

```python
a, b = 5, 10
print(f'{a}x{b}={a * b}', end = '')
```

在字符串结尾默认和使用换行符进行结尾，我们可以自己设定用来结尾的字符通过在结尾加上`end=`参数

## 转义字符

也就是反斜线`\`根据转义字符的不同进行不同的转义，这一点和C语言中的转义字符相同。

在python中如何不想使用转义字符可以在字符串之前加入`r`

```python
s1 = r'\nhello, world'
s2 = r"\n hello, world"

print(s1, s2)
```

## 字符运算

我们可以使用`+`和`*`对字符进行连接和重复。使用`in`和`not in`来判断一个字符串是否在另一个字符串中。

使用`[]`和`[:]`进行切片，切片相当于复制了一个字符串进行操作。`[start:end:step]`，从start开始到end-1结束，步长为1。

同样我们可以将步长改为-1，这样相当于倒序来复制数组。需要注意的是结束的地点为end-1，也就是说`[0:3:1]`，意味着从头开始到3-1的位置停下，每次移动1个，如果是从0到10排列的字符的话，以上分片就可以产生一个0，1，2的字符
如果start或者end中使用的是负数的话代表着倒数第几个，同样end也需要减一。

## 字符索引

字符串中可以使用类似数组的索引方式，但是不能改变字符串
```python
str = 'henny'
str[0] = 'p'
# 以上操作被禁止
```
使用`join()`进行合并，这个函数只能在字符串中使用，因此使用的方式是

```python
str = ','
str.join()
```
使用你str中的字符串来连接join后面的数组。
使用`split()`来分割字符串。在`split()`中传入的字符串是字符串中的符号，以这个符号位标准来分割字符串，将字符串变为一个列表。如果不指定分隔符，则默认使用空格，换行符制表符来分割。
使用`replace()`继续替换，这也是只能在字符串中使用的函数，这个函数传入三个参数，分别是：

1. 用于替换的字符串
2. 被替换的字符
3. 替换的字符个数

## 字符操作（方法）

在字符串中的方法都是以点操作符进行使用的。

### join

1. 参数：内容是字符串的任何容器。字符串、数组、元组等等。

```python
insertStr = ','
targetStr = '123456'
result = insertStr.join(targetStr)
print(result) #1,2,3,4,5

insertStr = ','
targetStr = ['1','23','4','5','67']
result = insertStr.join(targetStr)
print(result) #1,23,4,5,67
```

可以使用插入字符依次插入每一个目标字符或者容器中。

### 大小写切换方法

这之中的所有方法都会获得的一个字符串的复制，也就是并不修改原先的字符串，以第一个开头大写为例。

1. capitalize()：参数无，获得字符串开头大写的字符串的复制（并不修改原先的字符串）

```python
str1 = 'hello, world'
result1 = str1.capitalize()
str2 = 'hello, ' + 'world'
result2 = str1.capitalize()

print(result1) #Hello, world
print(result2) #Hello, world
print(str1) #hello, world
print(str2) #hello, world

```

2. title()：参数无，将字符串中每个单词的开头都变为大写字符。
3. upper()：参数无，将字符串中所有的字符都变为大写字符。
4. lower()：参数无，将字符串中所有字符变为小写。

### startswith()和endswith()

参数：字符串

分别检查字符串中是否以指定的字符串开始或者结尾。

# 变量

尽量让变量作用在局部，防止对象长期占用内存导致垃圾无法回收。

如果想在函数结束之后依然能够使用它的值可以使用闭包。

## 变量的作用域

在哪里定义就作用在哪里，如果不在函数或者类中定义就定义为全局变量

# 循环控制流
在循环当中也有一些用法，比如说提前终止`break`和跳过本次循环，进行下次循环，但不终止循环的`continue`
## for循环
使用`for...in`循环如果明确知道要循环的次数，或者是需要对一个容器进行循环的时候我们可以使用这种循环方式。
## 使用while循环
如果并不明确知道应该循环的次数，但是有一个终止循环的条件的话，可以使用`while`循环，当条件判否的时候循环便终止。

# 列表（数组）

## 创建列表

列表的值是一种结构化的有顺序的序列，也就是说和字符串类似，有一系列的方法和属性。定义的列表放在`[]`中，多个元素之间使用逗号分隔，同时可以使用for对列表元素进行遍历。

有两种方式创建列表
```python
empty_list = []
empty_list = list()
```
### 使用生成式语法创建列表

```python
f = [x for x in range(10)]
f1 = [x + y 
      for x in range(10)
      for y in range(10)]
f2 = [x + y 
      for x in 'ABCDE'
      for y in '12345']
```

f：生成一个从0到9的数字列表

f1：会生成x1+y1，x1+y2，x1+y3，以此类推的生成列表

f2：会和上面的生成方式类似，但是只是字符的组合，也即是字符串的运算。

### 通过yield将普通函数包装成生成器

我们通过yield包装之后的函数，所表现出来的性质会像一个容器。

> Generator functions allow you to declare a function that behaves like an iterator, i.e. it can be used in a for loop.

```python
# 通过递归的方法实现斐波那契数列
def _fib(n):
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
        yield a
def fib():
    for val in _fib(20): # 生尘器函数表现出容器的特性。
        print(val)
```



## 添加删除元素

### 添加

1. append：在列表末尾添加元素

   参数：1. 元素

2. insert：在列表中插入元素参数

   参数：1. 位置。2. 元素

3. extend：合并两个列表

   参数：1. 列表

   相同的用法还有

   ```python
   list1 = []
   list2 = []
   list2 += list1 # 将list2的列表加到list1后面并且命名为list1
   ```

### 删除

1. `remove()`：删除指定元素

   参数：1. 元素

2. `pop()`：删除指定位置的元素

   参数：1. 位置。不指定位置时删除最后一个元素

   返回值：被删除的元素

3. `clear()`：清空列表

   参数：无

将其他数据类型转换位列表，使用`list()`进行转换

```python
str = list('cat')
aTuple = ('1', '2', '3')
listTuple = list(a_tuple)
```
在列表的切片中，如果只有一个冒号，则表示冒号前为开始位，后位结束如，当有两个冒号的时候，第二个冒号后面的才是步长
使用`append()`添加元素到列表的最后
使用`+=extend()`连接两个列表，

```python
list_1 += list_2
list_1.extend(list_2)
```
## 排序

`sorted()`参数为列表，返回对列表的排序，但是并不会修改原数组，只是产生一个排序列表的副本。

**并不是列表的方法**因此不用点引用。

通过key关键字来指定以何种方式进行排序

```python
list = []
sortedList = sorted(list, reverse = True, key = len)
# reverse：逆向排序
# key：指定按照字符长度进行排序而不是通过默认的字母表。
```

## 打乱原列表的顺序

```python
from random import shuffle

arr = [i for i in range(1, 11)]
shuffle(arr)
```
# 元组

元组是一种不可修改内部元素的一种容器数据类型。

# 函数

在python中没有重载函数，也就是不能定义两个一样名字但是功能不同的函数。
函数同名的时候可以放入模块中分别调用。在调用模块的时候如果不仅有函数还有一些代码行的时候，可以使用
```python
def foo():
    pass
    
if __name__ == '__main__':
    do something
```
只有当单独运行文件时，名字才会是`__main__`，如果传入其他文件中作为模块的时候名字为其文件名。
# 内置函数

这些函数都是python内置的函数，可以直接调用。

## del函数

在容器中索引并删除。

## len函数

这个函数返回容器的长度，比如数组、字符串。

## zip函数
将可迭代元素作为参数将参数打包成一个元组。如果每个迭代器的元素个数不相同，那么返回的长度与最短的对象相同。
```python
zip([iterable, ...])
```
在python3中返回值是一个对象，如果要显示可以使用`list(zipped)`。这时我们要将转换之后的对象解包可以使用`zip(*)`，但是解包之后返回的是元组，如果我们将数组zip操作，会返回一个元组，我们可以通过如下方法将元组转换数组。
```python
a = [1, 2, 3, 4]
b = [5, 6, 7, 8]

zipped = zip(a, b)
a1, a2 = zip(*zipped)
a1, a2 = list(a1), list(a2)

print(a1)
```

# 系统

在os模块中提供了许多关于系统的函数，导入这个模块来使用

## 文件

## 打开文件

1. 手动打开但是也要手动关闭

对于文件首先要打开，这里有两种方式进行打开

```python
f = open('Great_Expectations.txt', 'r', encoding='utf-8')
print(f.read())
f.close() # f.close()是必须要执行用来释放外部资源。
```

使用`open`打开的文件可以压入列表中。并且可以将元组作为文件名

```python
filename = ('1.txt', '2.txt', '3.txt')
fs_list = []
fs_list.append(open(filename, 'w', encoding='utf-8'))
```

2. 通过上下文自动释放

第二种是使用上下文语法进行打开文件，这样在指定文件离开上下文的时候会自动释放

```python
with open('Great_Expectations.txt', 'r', encoding='utf-8') as f:
    print(f.read())

```

这样在结束上下文之后程序会自动释放文件。

## 读入文件

使用`f.read()`来的读入文件，同时可以使用`print()`将文件显示在终端上

我们开可以使用`for...in`循环来将文件进行逐行读入，并且是设置`sleep()`时间来间隔显示。最后使用`print()`来打印下一行。

```python
import time

with open('Great_Expectations.txt', 'r', encoding='utf-8') as f:
    for line in f:
        print(line, end=' ')
        time.sleep(1)
print()
```

使用`f.readlines()`逐行读取到列表中

```python
with open('Great_Expectations.txt', 'r', encoding='utf-8') as f:
    lines = f.readlines()
    print(lines)
```

## 写入文件

使用`open`打开的时候将文件的属性改为w，但是如果文件不存在会报错，或者追加写入a，此时如果文件不存在会自动创建文件。

如果想要想文件写入可以使用`f.write()`函数参数的内容就是要向文件写入的内容。

## 操作二进制文件



### 创建文件与删除文件

使用open来创建一个文件，并且使用close关闭这个文件。

```python
file = open('oops.txt', 'wt')
file.close()
```
当文件创建之后可以使用`remove()`来删除文件
```python
import os
os.remove()
```
### 检查文件是否存在
使用`exitsts()`来检查文件是否存在，本级目录和上级目录都可以检查，并且都存在
```pythonn
os.path.exists('oops.txt')
```
### 检查是否为文件，目录，是否是绝对路径
这些函数都在`os.path`下，使用的时候要
```python
os.path.isfile()
```
使用`isfile()`来检查是否一个文件。
使用`isdir()`来检查是否是目录。
使用`isabs()`来检查是否是绝对路径
上述函数都需要传入一个字符串
### shutil模块
`copy()`函数来自`shutil`模块中，在使用的时候需要导入此模块
```python
import shutil
shutil.copy('oops.txt', 'ah.txt')
```
将oops.txt复制到ah.txt，第一个参数复制到第二个参数中

`move()`这个函数会删除源文件，并且复制一个文件
`rename()`将文件重命名
### 获取路径
`abspath()`将相对路径扩展为绝对路径
`realpath()`如果创建了符号连接，使用此函数会获取到符号连接的真实路径
## 使用enumerate生成索引序列
将一个可遍历的数据结构传入函数，返回索引和值，值是每个可遍历结构的值。当使用`[start = ]`参数的时候可以指定开始的索引，比如`[start = 1]`，将从1开始将进行索引
```python
arr = [range(1, 11, 1)]
for count, value in enumerate(arr):
	print(count)
	print(value)
```
## 错误与异常处理
使用`try:`来测试程序，如果没有发生异常，则在执行完try之后就结束程序，如果发生异常，则根据异常执行`except`中的程序。

# 面向对象编程

"把一组数据结构和处理它们的方法组成对象（object），把相同行为的对象归纳为类（class），通过类的封装（encapsulation）隐藏内部细节，通过继承（inheritance）实现类的特化（specialization）和泛化（generalization），通过多态（polymorphism）实现基于对象类型的动态分派。"

类是对象的蓝图和模板，对象是类的实例，当我们将拥有共同属性和行为的对象抽取出来就构成了类。

面向对象有三大支柱：封装、继承、多态。

## 面向对象的理解

1. 封装：将能够隐藏的隐藏起来，只向外界暴露接口。我们知道调用之后会有什么结果就好，无需知道内部的数据结构是如何定义以及如何操作的。
1. 继承：我们可以在已有的类的基础上创建一个新类，让这个类从另一个类中继承方法和属性。提供信息的成为父类，超类，基类。继承的类称为子类或者派生类、衍生类。
1. 多态：继承来的子类可以增加新的属性和方法，也可以对父类的方法进行重写。所以，不同的子类可以表现出不同的行为，这就是多态。

## 访问可见性

属性的权限通常是所有和受保护的，也就是能否被外界访问。

对象的方法通常是公开的，公开的方法使对象能够接受到消息。

我们大多数时候不需要直接访问属性，但是需要直接访问方法。如果希望属性是私有的时候可以使用两个下划线作为开头。不过这种方法只是换了个名字阻止访问属性，并没有从语法层面阻止访问属性。

大部分程序员认为开放的比封闭的好。在实际开发中属性不建议设置为私有，这样很可能导致子类无法访问到。

这里有一个命名习惯，就是使用单下划线。这种仍然可以被外界访问到，但是在本类之外访问时要保持慎重。这更多的是一种暗示。

## 使用class类

```python
class Student():
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def foo(self):
        pass
```

在类中的函数我们成为对象的方法。

我们定义好类之后可以使用类创建实例，实例可以做类中的事。

## 使用@porperty装饰器

虽然不建议将属性设置为私有，但是直接访问属性可能会造成一些问题。我们可以使用装饰器来进行安全方便的访问属性。

```python
class Person():
    def __init__(self, name, age):
        self._name = name
        self._age = age
    @property # 访问器：使用getter方法访问self._name
    def name(self):
        return self._name
    @property
    def age(self):
        return self._age
    @age.setter # 修改器：使用setter方法来修改self._age的值
    def age(self, age):
        self._age = age
    def play(self):
        if self._age <= 16:
            print('%s正在玩飞行棋.' % self._name)
        else:
            print('%s正在玩斗地主.' % self._name)
            
def main2
    person = Person('Daniel', 12)
    person.play() #Daniel正在玩飞行棋.
    person.age = 21
    person.play() #Daniel正在玩斗地主.
```

通过这种操作我们可以直接修改属性的值，而且不会造成什么问题。

通过`@property`装饰器之后外界可以访问到加单下划线的属性，但是依然无法修改，使用`@name.setter`装饰器之后就可以对属性就行修改了。

## 使用`__slots__`绑定新属性和方法

```python
class Person():
    __slots__ = (_name, _age, _gender)
    def __init__(self, name, age):
        self._name = name
        self._age = age
        
person = Person('daniel', 21)
person._gender = 'man'
print(person._gender) # man
```

限定了Person对象只能绑定`_name _age _gender`属性。并且限定只能对当前类的对象起作用，不对子类起作用。绑定之后我们可以对属性或方法进行操作.

## 静态方法

我们可以在类中定义不属于任何对象只属于类的方法。也就是静态方法。

例：在构造三角形的类中，我们需要传入三条边，但是三条边并不一定能组成三角形，所以在构成三角形之前就需要判断能否构成三角形，此时还未构成三角形，因此还没使用类。也就是说这个方法属于三角形类的，而不属于三角形对象的。

```python
from math import sqrt

class Triangle(object):

    def __init__(self, a, b, c):
        self._a = a
        self._b = b
        self._c = c
    # 定义静态方法判断能否组成三角形。
    @staticmethod
    def is_valid(a, b, c):
        return a + b > c and b + c > a and a + c > b

    def perimeter(self):
        return self._a + self._b + self._c

    def area(self):
        half = self.perimeter() / 2
        return sqrt(half * (half - self._a) *
                    (half - self._b) * (half - self._c))

def main():
    a, b, c = 3, 4, 5
    # 静态方法和类方法都是通过给类发消息来调用的
    # 这个时候我们还没创建对象t = Triangle()
    ## 但是如果能组成三角形才会进入if判断
    if Triangle.is_valid(a, b, c):
        t = Triangle(a, b, c)
        print(t.perimeter())
        # 也可以通过给类发消息来调用对象方法但是要传入接收消息的对象作为参数
        # print(Triangle.perimeter(t))
        print(t.area())
        # print(Triangle.area(t))
    else:
        print('无法构成三角形.')


if __name__ == '__main__':
    main()
```

在没有创建对象的时候就调用类中的方法，也就是`Triangle.is_valid(a, b, c)`。同时也可以通过这种方法调用*对象的方法*，但是要在方法中指定给那个对象发送信息。`Trangle.perimeter(t)`，t是实际的对象，对t发送信息需要将t写入括号中。

## 类方法

类其实也是一种对象，在类中创建了一个方法，也就是类方法@classmethod第一个参数默认是cls，也就是类本身。

```python
from time import time, localtime, sleep


class Clock(object):
    """数字时钟"""
    def __init__(self, hour=0, minute=0, second=0):
        self._hour = hour
        self._minute = minute
        self._second = second
    @classmethod
    def now(cls):
        ctime = localtime(time())
        return cls(ctime.tm_hour, ctime.tm_min, ctime.tm_sec)
    def run(self):
        """走字"""
        self._second += 1
        if self._second == 60:
            self._second = 0
            self._minute += 1
            if self._minute == 60:
                self._minute = 0
                self._hour += 1
                if self._hour == 24:
                    self._hour = 0
    def show(self):
        """显示时间"""
        return '%02d:%02d:%02d' % \
               (self._hour, self._minute, self._second)

def main():
    # 通过类方法创建对象并获取系统时间
    clock = Clock.now()
    while True:
        print(clock.show())
        sleep(1)
        clock.run()

if __name__ == '__main__':
    main()

```

上面的例子中，我们需要传入三个参数，分别是小时、分钟、秒，创建类方法之后，我们可以通过类方法创建对象，在类方法中返回的值是当前的系统时间。类方法相当于直接对类进行了操作。

## 抽象类

抽象类也就是无法创建对象实例的类，这种类是专门用来继承的。在python中没有语法层面的支持，可以通过abc模块的ABCMeta元类和abstractmethod包装器来达到抽象类的效果。



# 生成二进制文件
要想让没有安装python的用户使用程序，需要生成一个二进制文件，这里使用python的`pyinstaller`生成。
首先需要安装pyinstaller,`pip install pyinstaller`，之后使用`pyinstaller file.py`进行封装。在封装的时候使用`-w`可以在运行的时候不显示终端，使用`-F`可以只生成一个文件，这样在文件中不会有其他的lib。
但是在打包好之后，如果源代码中有需要的外部文件，就算打包成了一个程序也要将文件放入对应的根目录下，否则会出现资源的缺失。比如说在源文件中需要图片，打包之后并不会将图片直接打包进程序，因此需要将图片放入到对应目录下。

| 参数 | 用途                            |
| --- | ------------------------------ |
| -F   | 只生成一个exe文件               |
| -D   | 会生成许多lib文件，这是默认选项 |
| -c   | 显示控制台，默认选项            |
| -w   | 不显示控制台                    |
| -i   | 改变生成程序的icon图标          |



