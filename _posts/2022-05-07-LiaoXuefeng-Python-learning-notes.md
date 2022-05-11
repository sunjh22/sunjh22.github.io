---
title:  "廖雪峰Python3教程学习笔记"
date:   2022-05-07
categories: [Python3]
tags: [LearingNotes]
render_with_liquid: false
---

## Python基础

### 字符串和编码
`ASCII`编码：编码英文
`Unicode`编码：编码所有字符
`UTF-8`编码：可变长编码编码所有字符

最早的计算机在设计时采用8个比特（bit）作为一个字节（byte），所以，一个字节能表示的最大的整数就是255（二进制11111111=十进制255）

`ASCII`编码和`Unicode`编码的区别：ASCII编码是1个字节，而Unicode编码通常是2个字节

本着节约的精神，又出现了把Unicode编码转化为“可变长编码”的`UTF-8`编码,UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节

UTF-8编码有一个额外的好处，就是ASCII编码实际上可以被看成是UTF-8编码的一部分，所以，大量只支持ASCII编码的历史遗留软件可以在UTF-8编码下继续工作。

由于Python源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行：

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

用`format()`格式化字符串：`"{:d}.format(24)"`，更多例子如下

![format格式化](../../assets/img/format.png){: .normal w="600" h="600" }

### 数据结构
`list`: 常用方法 `append`, `insert`, `pop`

`tuple`: 类似于list，但其中元素不可变

`dict`: 一个内置方法可以判断key是否存在 - dict.get('a',-1),若不存在返回-1

`set`: 相当于一个没有value的字典，其中元素不可重复且无序，故可以进行交集并集等操作

列表是可变对象，对其进行的操作会直接改变其中的内容，元组和字符串还有dict的key是不可变对象

## 函数
可以import同一个文件夹中.py文件中的函数：`from file.py import a_function`

数据类型检查可以用内置函数`isinstance()`实现

Python的函数返回多值其实就是返回一个tuple

### 参数
必选参数

默认参数
> 默认参数：定义默认参数要牢记一点：默认参数必须指向不变对象！
{: .prompt-warning}

可变参数：在参数前面加一个`*`号，允许传入0个或多个参数，这些可变参数在函数调用时自动组装为一个tuple。`*nums`表示把`nums`这个list的所有元素作为可变参数传进去

关键字参数：在参数前面加一个`**`号，允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。`**extra`表示把`extra`这个dict的所有key-value用关键字参数传入到函数的`**kw`参数，`kw`将获得一个dict，注意`kw`获得的dict是`extra`的一份拷贝，对`kw`的改动不会影响到函数外的`extra`

命名关键字参数：含参数名的关键字参数，使用命名关键字参数时，要特别注意，如果没有可变参数，就必须加一个`*`作为特殊分隔符，如果有可变参数，就不用`*`分隔符，这个参数可以设置默认值，例子如下
```python
def person0(name, age, *, city, job):
    print(name, age, city, job)

def person1(name, age, *args, city, job):
    print(name, age, args, city, job)

def person2(name, age, *, city='Beijing', job):
    print(name, age, city, job)
```

在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数

对于任意函数，都可以通过类似`func(*args, **kw)`的形式调用它，无论它的参数是如何定义的
> 虽然可以组合多达5种参数，但不要同时使用太多的组合，否则函数接口的可理解性很差
{: .prompt-info}

### 递归函数

使用递归函数的优点是逻辑简单清晰，缺点是过深的调用会导致栈溢出。

针对尾递归优化的语言可以通过尾递归防止栈溢出。尾递归事实上和循环是等价的，没有循环语句的编程语言只能通过尾递归实现循环。

Python标准的解释器没有针对尾递归做优化，任何递归函数都存在栈溢出的问题。

一个经典的递归函数问题：[汉诺塔](https://baike.baidu.com/item/%E6%B1%89%E8%AF%BA%E5%A1%94/3468295)，尝试一下用递归函数实现吧！

## 高级特性
始终牢记，代码越少，开发效率越高

切片

迭代: `enumerate`函数

列表生成式: 在一个列表生成式中，`for`前面的`if ... else`是表达式，而`for`后面的`if`是过滤条件，不能带`else`

### 生成器(generator)
列表生成器：把列表生成式中的`[]`改成`()`即可

生成器函数：`yield`关键字

用生成器写一个[杨辉三角](http://baike.baidu.com/view/7804.htm)吧！

```python
def triangles():
	L = [1]
	while True:
		yield L
		L = [1] + [L[n] + L[n+1] for n in range(len(L)-1)] + [1]
```

### 迭代器
凡是可作用于`for`循环的对象都是`Iterable`类型

可以被next()函数调用并不断返回下一个值的对象称为迭代器：`Iterator`。

把`list`、`dict`、`str`等`Iterable`变成`Iterator`可以使用`iter()`函数


## 函数式编程
函数就是面向过程的程序设计的基本单元

函数式编程就是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量，因此，任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为没有副作用。而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，同样的输入，可能得到不同的输出，因此，这种函数是有副作用的。

函数式编程的一个特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数！

### 高阶函数
函数本身也可以赋值给变量，即：变量可以指向函数

既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为**高阶函数**

编写高阶函数，就是让函数的参数能够接收别的函数。

#### map/reduce
`map()`函数接收两个参数，一个是函数，一个是`Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回

`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算

一个将字符串转化成整数的函数
```python
from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def char2num(s):
	return DIGITS[s]


def str2in(s):
	return reduce(lambda x, y: x * 10 + y, map(char2num, s))
```

一个将字符串转化成浮点数的函数
```python
from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def char2num(s):
	return DIGITS[s]


def str2float(s):
	i = s.index('.')
	n = len(s) - i - 1
	f1 = reduce(lambda x, y: x * 10 + y, map(char2num, s[:i]))
	f2 = reduce(lambda x, y: x * 10 + y, map(char2num, s[i+1:]))
	return f1 + f2 / (10 ** n)
```