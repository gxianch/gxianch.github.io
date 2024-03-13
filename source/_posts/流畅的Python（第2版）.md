---
title: 流畅的Python（第2版）
date: 2024-03-01 14:30:13
categories: [python]
toc: true
mathjax: true
top: 98
tags:
  - 技术
  - python
  - ※※※
  - ✰✰✰✰✰
  - OReilly
---

# 前言

本地jupyter打开
```
(base) xian@guofuchengdeMacBook-Pro ~ % conda activate base
(base) xian@guofuchengdeMacBook-Pro ~ % jupyter notebook
```
http://localhost:8888/tree/PycharmProjects/book/example-code-2e

--------------------

# 第 1 章　Python 数据模型

不使用 collection.len()，而是使用 len(collection)？这一点表面上看确实奇怪，而且只是众多奇怪行为的冰山一角，不过知道背后的原因之后，你会发现这才真正符合“Python 风格”。一切的一切都埋藏在 Python 数据模型中。我们平常自己创建对象时就要使用这个 API，确保使用最地道的语言功能。
[2024/2/19 12:20 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/10/1%3A48%29)

--------------------

特殊方法的名称前后两端都有双下划线。例如，在 obj[key] 句法背后提供支持的是特殊方法 __getitem__。为了求解 my_collection[key]，Python 解释器要调用 my_collection.__getitem__(key)。
[2024/2/19 2:45 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/14/1%3A81%29)

--------------------

## 1.2　一摞 Python 风格的纸牌

self._cards = [Card(rank, suit) for suit in self.suits
                                        for rank in self.ranks]

[2024/2/19 4:13 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/38/2/1%3A236%29)

两个for循环可以这样用？

--------------------

虽然 FrenchDeck 类隐式继承 object 类，但是前者的多数功能不是继承而来的，而是源自数据模型和组合模式。通过前面使用 random.choice、reversed 和 sorted 的示例可以看出，实现 __len__ 和 __getitem__ 两个特殊方法后，FrenchDeck 的行为就像标准的 Python 序列一样，受益于语言核心特性（例如迭代和切片）和标准库。__len__ 和 __getitem__ 的实现利用组合模式，把所有工作委托给一个 list 对象，即 self._cards。
[2024/2/19 4:18 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/88/1%3A0%29)

--------------------

## 1.3　特殊方法是如何使用的

我们在编写代码时一般不直接调用特殊方法，除非涉及大量元编程。即便如此，大部分时间也是实现特殊方法，很少显式调用。唯一例外的是 __init__ 方法，为自定义的类实现 __init__ 方法时经常直接调用它调取超类的初始化方法。
[2024/2/19 4:26 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/102/1%3A0%29)

--------------------

### 1.3.2　字符串表示形式

特殊方法 __repr__ 供内置函数 repr 调用，获取对象的字符串表示形式。如未定义 __repr__ 方法，Vector 实例在 Python 控制台中显示为 <Vector object at 0x10e100070> 形式。
[2024/2/19 4:49 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/152/1%3A0%29)

--------------------

与此形成对照的是，__str__ 方法由内置函数 str() 调用，在背后供 print 函数使用，返回对终端用户友好的字符串。

有时，__repr__ 方法返回的字符串足够友好，无须再定义 __str__ 方法
[2024/2/19 4:50 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/160/1%3A0%29)

--------------------

如果你熟悉的编程语言使用 toString 方法，那么你可能习惯实现 __str__ 方法而不是 __repr__ 方法。在 Python 中，如果必须二选一的话，请选择 __repr__ 方法。
[2024/2/19 5:03 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/164/2/2%3A1%29)

--------------------

### 1.3.4　容器 API

图 1-2：基本容器类型的 UML 类图。以斜体显示的方法名称表示抽象方法，必须由具体子类（例如 list 和 dict）实现。其他方法有具体实现，子类可以直接继承
[2024/2/19 5:03 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/184/2/1%3A0%29)

--------------------

Python 不强制要求具体类继承这些抽象基类中的任何一个。只要实现了 __len__ 方法，就说明那个类满足 Sized 接口。

Collection 有 3 个十分重要的专用接口：

Sequence 规范 list 和 str 等内置类型的接口；
Mapping 被 dict、collections.defaultdict 等实现；
Set 是 set 和 frozenset 两个内置类型的接口。
只有 Sequence 实现了 Reversible，因为序列要支持以任意顺序排列内容，而 Mapping 和 Set 不需要。
[2024/2/19 5:11 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/190/1%3A0%29)

--------------------

自 Python 3.7 开始，dict 类型正式“有顺序”了，不过只是保留键的插入顺序。你不能随意重新排列 dict 中的键。
[2024/2/19 5:15 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/198/2/2%3A1%29)

https://zhuanlan.zhihu.com/p/660487408

--------------------

## 1.4　特殊方法概述

表 1-1：特殊方法名称（不含运算符）

分类

方法名称

字符串（字节）表示形式

__repr__ __str__ __format__ __bytes__ __fspath__

转换为数值

__bool__ __complex__ __int__ __float__ __hash__ __index__

模拟容器

__len__ __getitem__ __setitem__ __delitem__ __contains__

迭代

__iter__ __aiter__ __next__ __anext__ __reversed__

可调用对象或执行协程

__call__ __await__

上下文管理

__enter__ __exit__ __aexit__ __aenter__

构造和析构实例

__new__ __init__ __del__

属性管理

__getattr__ __getattribute__ __setattr__ __delattr__ __dir__

属性描述符

__get__ __set__ __delete__ __set_name__

抽象基类

__instancecheck__ __subclasscheck__

类元编程

__prepare__ __init_subclass__ __class_getitem__ __mro_entries__


[2024/2/19 5:16 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/212/2/1%3A0%29)

--------------------

表 1-2：运算符的符号和背后的特殊方法
[2024/2/19 5:16 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/218/2/1%3A0%29)

--------------------

运算符分类

符号

方法名称

一元数值运算符

- + abs()

__neg__ __pos__ __abs__

各种比较运算符

< <= == != > >=

__lt__ __le__ __eq__ __ne__ __gt__ __ge__

算术运算符

+ - * / // % @ divmod() round() ** pow()

__add__ __sub__ __mul__ __truediv__ __floordiv__ __mod__ __matmul__ __divmod__ __round__ __pow__

反向算术运算符

（交换算术运算符的操作数）

__radd__ __rsub__ __rmul__ __rtruediv__ __rfloordiv__ __rmod__ __rmatmul__ __rdivmod__ __rpow__

增量赋值算术运算符

+= -= *= /= //= %= @= **=

__iadd__ __isub__ __imul__ __itruediv__ __ifloordiv__ __imod__ __imatmul__ __ipow__

按位运算符

& | ^ << >> ~

__and__ __or__ __xor__ __lshift__ __rshift__ __invert__

反向按位运算符

（交换按位运算符的操作数）

__rand__ __ror__ __rxor__ __rlshift__ __rrshift__

增量赋值按位运算符

&= |= ^= <<= >>=

__iand__ __ior__ __ixor__ __ilshift__ __irshift__
[2024/2/19 5:17 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/220/2/2/2/2/1%3A0%29)

--------------------

## 1.5　len 为什么不是方法

把 abs 和 len 看作一元运算符，说不定你更能接受它们表面上看似对函数的调用
[2024/2/19 5:18 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/16/2/4/230/2/2%3A18%29)

--------------------

# 第 2 章　丰富的序列

## 2.2　内置序列类型概览

Python 标准库用 C 语言实现了丰富的序列类型，列举如下。

容器序列

　　可存放不同类型的项，其中包括嵌套容器。示例：list、tuple 和 collections.deque。

扁平序列

　　可存放一种简单类型的项。示例：str、bytes 和 array.array。


[2024/2/19 5:29 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/30/1%3A0%29)

--------------------

图 2-1：图中展示的是一个元组和一个数组的内存简图，它们各有 3 项。灰色方块（未按比例绘制）表示各个 Python 对象的内存标头。元组中的每一项都是引用，引用的是不同的 Python 对象，对象中还可以存放其他 Python 对象的引用，例如那个包含两个项的列表。相比之下，Python 中的数组整体是一个对象，存放一个 C 语言数组，包含 3 个双精度数


[2024/2/19 5:27 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/44/2/1%3A0%29)

--------------------

因此，扁平序列更加紧凑，但是只能存放原始机器值，例如字节、整数和浮点数。
[2024/2/19 5:28 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/46/1%3A0%29)

--------------------

还可按可变性对序列类型分类。

可变序列

　　例如 list、bytearray、array.array 和 collections.deque。

不可变序列

　　例如 tuple、str 和 bytes。
[2024/2/19 5:29 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/50/1%3A3%29)

--------------------

记住不同序列类型的共同点：有些是可变的，有些是不可变的；有些是容器，有些是扁平的。这有助于你把相关概念延伸到不太熟悉的序列类型上。

list 是最基本的序列类型，是一种可变容器。
[2024/2/19 5:30 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/68/1%3A0%29)

--------------------

## 2.3　列表推导式和生成器表达式

### 2.3.1　列表推导式对可读性的影响

2.3.1　列表推导式对可读性的影响
请你切身体验一下，示例 2-1 和示例 2-2 哪一个更易于理解？

示例 2-1　基于一个字符串构建一个 Unicode 码点列表

>>> symbols = '$¢£¥€¤'
>>> codes = []
>>> for symbol in symbols:
...     codes.append(ord(symbol))
...
>>> codes
[36, 162, 163, 165, 8364, 164]
[2024/2/19 5:31 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/82%5Bnav_point_35%5D/1%3A0%29)

--------------------

示例2-2 使用列表推导式基于一个字符串构建一个 Unicode 码点列表

>>> symbols = '$¢£¥€¤'
>>> codes = [ord(symbol) for symbol in symbols]
>>> codes
[36, 162, 163, 165, 8364, 164]
稍微懂一点 Python 或许就能看懂示例 2-1，但是学会列表推导式后，我觉得示例 2-2 更易于理解，因其意图是明确的。
[2024/2/19 5:31 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/90/2/2/1%3A0%29)

--------------------

### 2.3.2　列表推导式与 map 和 filter 比较

>>> symbols = '$¢£¥€¤'
>>> beyond_ascii = [ord(s) for s in symbols if ord(s) > 127]
>>> beyond_ascii
[162, 163, 165, 8364, 164]
[2024/2/19 5:35 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/116/2/1%3A0%29)

--------------------

### 2.3.3　笛卡儿积

>>> colors = ['black', 'white']
>>> sizes = ['S', 'M', 'L']
>>> tshirts = [(color, size) for color in colors for size in sizes]  ❶
>>> tshirts
[('black', 'S'), ('black', 'M'), ('black', 'L'), ('white', 'S'),
    ('white', 'M'), ('white', 'L')]
>>> for color in colors:  ❷
...     for size in sizes:
...         print((color, size))
...
('black', 'S')
('black', 'M')
('black', 'L')
('white', 'S')
('white', 'M')
('white', 'L')
>>> tshirts = [(color, size) for size in sizes      ❸
...                          for color in colors]
>>> tshirts
[('black', 'S'), ('white', 'S'), ('black', 'M'), ('white', 'M'),
    ('black', 'L'), ('white', 'L')]
❶ 先按颜色再按尺寸排列，生成一个元组列表。

❷ 注意，这里两个循环的嵌套方式与列表推导式中 for 子句的先后顺序一样。

❸ 如果想先按尺寸再按颜色排列，则只需要调整 for 子句的顺序。我在这里插入了一个换行符，这样排列顺序就更明显了。


[2024/2/19 5:36 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/134/2/1%3A0%29)

--------------------

### 2.3.4　生成器表达式

2.3.4　生成器表达式
虽然列表推导式也可以生成元组、数组或其他类型的序列，但是生成器表达式占用的内存更少，因为生成器表达式使用迭代器协议逐个产出项，而不是构建整个列表提供给其他构造函数。


[2024/2/19 5:38 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/148%5Bnav_point_38%5D/1%3A0%29)

--------------------

生成器表达式的句法跟列表推导式几乎一样，只不过把方括号换成圆括号而已。

示例 2-5 使用生成器表达式构建一个元组和一个数组。

示例 2-5　使用生成器表达式构建一个元组和一个数组

>>> symbols = '$¢£¥€¤'
>>> tuple(ord(symbol) for symbol in symbols)  ❶
(36, 162, 163, 165, 8364, 164)
>>> import array
>>> array.array('I', (ord(symbol) for symbol in symbols))  ❷
array('I', [36, 162, 163, 165, 8364, 164])
[2024/2/19 5:38 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/152/1%3A0%29)

--------------------

## 2.4　元组不仅仅是不可变列表

2.4　元组不仅仅是不可变列表
有些 Python 入门教程把元组称为“不可变列表”，然而这没有完全概括元组的特点。元组有两个作用，除了可以作为不可变列表使用之外，还可用作没有字段名称的记录。
[2024/2/21 4:21 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/176%5Bnav_point_39%5D/1%3A0%29)

元组是列表的延伸，列表内部是单个元素，元祖内部元素可以是多个

--------------------

### 2.4.1　用作记录

示例 2-7　把元组当作记录使用

>>> lax_coordinates = (33.9425, -118.408056)  ❶
>>> city, year, pop, chg, area = ('Tokyo', 2003, 32_450, 0.66, 8014)  ❷
>>> traveler_ids = [('USA', '31195855'), ('BRA', 'CE342567'),  ❸
...     ('ESP', 'XDA205856')]
[2024/2/20 5:08 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/188/2/2/1%3A0%29)

--------------------

一个元组列表，元组的形式为 (country_code, passport_number)。
[2024/2/20 5:08 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/196/1%3A2%29)

--------------------

仅使用一个语句就把 ('Tokyo', 2003, 32_450, 0.66, 8014) 赋值给了 city, year, pop, chg, area。
[2024/2/20 5:09 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/208/1%3A66%29)

--------------------

### 2.4.2　用作不可变列表

2.4.2　用作不可变列表
Python 解释器和标准库经常把元组当作不可变列表使用，你也可以。这么做主要有两个好处。

意图清晰

　　只要在源码中见到元组，你就知道它的长度永不可变。


[2024/2/20 5:09 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/214%5Bnav_point_41%5D/1%3A0%29)

--------------------

性能优越

　　长度相同的元组和列表，元组占用的内存更少，而且 Python 可对元组做些优化。


[2024/2/20 5:09 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/222/2/1%3A0%29)

--------------------

元组依然广泛用作不可变列表。Stack Overflow 网站中有一个问题，题为“Are tuples more efficient than lists in Python?”
[2024/2/20 5:12 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/244/1%3A5%29)

--------------------

tuple 实例长度固定，分配的内存空间正好够用。而 list 实例的内存空间要富余一些，时刻准备追加元素。

[2024/2/20 5:12 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/246/6/2/1%3A0%29)

--------------------

### 2.4.3　列表和元组方法的比较

把元组当作列表的不可变变体使用时，有必要了解二者 API 之间的异同。从表 2-1 可以看出，元组支持所有不涉及增删项的列表方法，而且元组没有 __reversed__ 方法。
[2024/2/21 3:15 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/250/1%3A0%29)

--------------------

表 2-1：列表和元组的方法及属性
[2024/2/21 3:15 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/252/2/1%3A0%29)

--------------------

## 2.5　序列和可迭代对象拆包

最明显的拆包形式是并行赋值（parallel assignment），即把可迭代对象中的项赋值给变量元组，如以下示例所示。

>>> lax_coordinates = (33.9425, -118.408056)
>>> latitude, longitude = lax_coordinates  # 拆包
>>> latitude
33.9425
>>> longitude
-118.408056
利用拆包还可以轻松对调两个变量的值，省掉中间的临时变量。

>>> b, a = a, b
调用函数时在参数前面加上一个 *，利用的也是拆包。

>>> divmod(20, 8)
(2, 4)
>>> t = (20, 8)
>>> divmod(*t)
(2, 4)
>>> quotient, remainder = divmod(*t)
>>> quotient, remainder
(2, 4)
上述代码还展示了拆包的另一个用途：为函数返回多个值提供一种便于调用方使用的方式。再举一个例子：os.path.split() 函数根据传入的文件系统路径构建元组 (path, last_part)。

>>> import os
>>> _, filename = os.path.split('/home/luciano/.ssh/id_rsa.pub')
>>> filename
'id_rsa.pub'
如果只需要拆包得到的部分项，那么还可以使用 2.5.1 节介绍的 * 句法。
[2024/2/21 3:18 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/266/1%3A0%29)

--------------------

### 2.5.1　使用 * 获取余下的项

2.5.1　使用 * 获取余下的项
定义函数时可以使用 *args 捕获余下的任意数量的参数，这是 Python 的一个经典特性。
[2024/2/21 3:19 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/284%5Bnav_point_44%5D/1%3A0%29)

--------------------

Python 3 把这一思想延伸到了并行赋值上。

>>> a, b, *rest = range(5)
>>> a, b, rest
(0, 1, [2, 3, 4])
>>> a, b, *rest = range(3)
>>> a, b, rest
(0, 1, [2])
>>> a, b, *rest = range(2)
>>> a, b, rest
(0, 1, [])
[2024/2/21 3:19 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/288/1%3A0%29)

--------------------

### 2.5.3　嵌套拆包

2.5.3　嵌套拆包
拆包的对象可以嵌套，例如 (a, b, (c, d))。如果值的嵌套结构是相同的，则 Python 能正确处理。示例 2-8 演示了嵌套拆包的具体用法。

示例 2-8　拆包嵌套元组，获取经度

metro_areas = [
    ('Tokyo', 'JP', 36.933, (35.689722, 139.691667)),  ❶
    ('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
    ('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
    ('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
    ('São Paulo', 'BR', 19.649, (-23.547778, -46.635833)),
]

[2024/2/21 3:21 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/312%5Bnav_point_46%5D/1%3A0%29)

--------------------

 for name, _, _, (lat, lon) in metro_areas:  ❷
[2024/2/21 3:21 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/318/2/1%3A390%29)

--------------------

## 2.7　切片

2.7　切片
在 Python 中，列表、元组、字符串等所有序列类型都支持切片操作。切片比多数人认为的要强大很多。
[2024/2/21 3:30 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/476%5Bnav_point_49%5D/1%3A0%29)

--------------------

### 2.7.1　为什么切片和区间排除最后一项

方便在索引 x 处把一个序列拆分成两部分而不产生重叠，直接使用 my_list[:x] 和 my_list[x:] 即可。例如：

>>> l = [10, 20, 30, 40, 50, 60]
>>> l[:2]  # 在索引位2处拆分
[10, 20]
>>> l[2:]
[30, 40, 50, 60]
>>> l[:3]  # 在索引位3处拆分
[10, 20, 30]
>>> l[3:]
[40, 50, 60]
[2024/2/21 3:31 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/486/6/2/1%3A0%29)

--------------------

### 2.7.4　为切片赋值

2.7.4　为切片赋值
在赋值语句的左侧使用切片表示法，或者作为 del 语句的目标，可以就地移植、切除或以其他方式修改可变序列。下面举几个例子演示这种表示法的强大功能。

>>> l = list(range(10))
>>> l
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> l[2:5] = [20, 30]
>>> l
[0, 1, 20, 30, 5, 6, 7, 8, 9]
>>> del l[5:7]
>>> l
[0, 1, 20, 30, 5, 8, 9]
[2024/2/21 3:33 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/530%5Bnav_point_53%5D/1%3A0%29)

--------------------

## 2.8　使用 + 和 * 处理序列

2.8　使用 + 和 * 处理序列
Python 程序员预期序列支持 + 和 *。通常，+ 的两个运算对象必须是同一种序列，而且都不可修改，拼接的结果是一个同类型的新序列。

如果想多次拼接同一个序列，可以乘以一个整数。同样，结果是一个新创建的序列。

>>> l = [1, 2, 3]
>>> l * 5
[1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3]
>>> 5 * 'abcd'
'abcdabcdabcdabcdabcd'
+ 和 * 始终创建一个新对象，绝不更改操作数。
[2024/2/21 3:34 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/540%5Bnav_point_54%5D/1%3A0%29)

--------------------

## 2.9　list.sort 与内置函数 sorted

2.9　list.sort 与内置函数 sorted
list.sort 方法就地排序列表，即不创建副本。返回值为 None，目的就是提醒我们，它更改了接收者，11 没有创建新列表。这是 Python API 的一个重要约定：就地更改对象的函数或方法应该返回 None，让调用方清楚地知道接收者已被更改，没有创建新对象。
[2024/2/21 3:37 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/680%5Bnav_point_58%5D/1%3A0%29)

--------------------

与之相反，内置函数 sorted 返回创建的新列表。
[2024/2/21 3:38 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/688/1%3A0%29)

--------------------

## 2.10　当列表不适用时

2.10　当列表不适用时
list 类型简单灵活，不过，针对具体的需求，或许还有更好的选择。例如，使用数组处理上百万个浮点值可以节省大量内存。另外，如果经常需要在列表的两端添加和删除项，使用 deque（double-ended queue，双端队列）更合适，这是一种更高效的 FIFO14 数据结构。
[2024/2/21 3:39 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/732%5Bnav_point_59%5D/1%3A0%29)

--------------------

如果你在代码中经常检查容器中是否存在某一项（例如 item in my_collection），应考虑使用 set 类型存储 my_collection，尤其是项数较多的情况。Python 对 set 成员检查做了优化，速度更快。set 也是可迭代对象，但不是序列，因为 set 中的项是无序的
[2024/2/21 3:40 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/738/2/2%3A1%29)

--------------------

### 2.10.1　数组

2.10.1　数组
[2024/2/21 3:54 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/742%5Bnav_point_60%5D/1%3A0%29)

--------------------

如果一个列表只包含数值，那么使用 array.array 会更高效。数组支持所有可变序列操作（包括 .pop、.insert 和 .extend），此外还有快速加载项和保存项的方法，例如 .frombytes 和 .tofile。
[2024/2/21 3:54 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/744/1%3A0%29)

--------------------

表 2-3：list 和 array 的方法及属性
[2024/2/21 3:56 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/776/2/1%3A0%29)

--------------------

### 2.10.3　NumPy

示例 2-22 简单演示 NumPy 的用法，对二维数组做了些基本操作。

示例 2-22　numpy.ndarray 中行和列的基本操作

>>> import numpy as np ❶
>>> a = np.arange(12)  ❷
>>> a
array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11])
>>> type(a)
<class 'numpy.ndarray'>
>>> a.shape  ❸
(12,)
>>> a.shape = 3, 4  ❹
>>> a
array([[ 0,  1,  2,  3],
       [ 4,  5,  6,  7],
       [ 8,  9, 10, 11]])
>>> a[2]  ❺
array([ 8,  9, 10, 11])
>>> a[2, 1]  ❻
9
>>> a[:, 1]  ❼
array([1, 5, 9])
>>> a.transpose()  ❽
array([[ 0,  4,  8],
       [ 1,  5,  9],
       [ 2,  6, 10],
       [ 3,  7, 11]])
[2024/2/21 3:59 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/842/1%3A0%29)

--------------------

NumPy 和 SciPy 这两个库的功能异常强大，为很多优秀的工具提供了坚实的基础，例如 Pandas 和 scikit-learn。Pandas 实现的高效数组类型可以保存非数值数据，此外还支持导入和导出多种格式，包括 .csv、.xls、SQL 转储、HDF5 等。scikit-learn 是目前最广泛使用的机器学习工具集。
[2024/2/21 4:00 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/886/1%3A0%29)

--------------------

## 2.11　本章小结

元组在 Python 中扮演两个角色，一是不具名字段记录，二是不可变列表。把元组当作不可变列表使用时请记住，仅当元组中的所有项也都是不可变对象时，才能保证元组值是固定的。在元组上调用 hash(t) 函数可以快速判断元组的值是否固定。如果 t 包含可变的项，则 hash(t) 抛出 TypeError。

把元组当作记录使用时，元组拆包是提取元组字段最安全、可读性最高的方法。除了元组之外，* 在许多上下文中还适用于列表和可迭代对象。
[2024/2/21 4:02 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/954/1%3A0%29)

--------------------

序列切片是最受欢迎的 Python 句法特性之一，其功能比许多人所想的还要强大。用户定义的序列甚至可以支持 NumPy 那种多维切片和省略号（...）表示法。通过切片赋值修改可变序列是极具表现力的操作。


[2024/2/21 4:02 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/958/1%3A0%29)

--------------------

## 2.12　延伸阅读

与列表不同，元组通常包含不同类型的项。这也符合常理：如果元组中的每一项都是一个字段，那么每个字段就可以具有不同的类型。
[2024/2/21 4:07 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/18/2/4/992/32/1%3A0%29)

--------------------

# 第 3 章　字典和集合

## 3.1　本章新增内容

dict 和 set 的底层实现仍然依赖于哈希表，不过 dict 的代码有两项重要的优化，节省了内存，还能保留键的插入顺序。
[2024/2/21 4:13 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/20/2/4/22/2/1%3A0%29)

--------------------

## 3.2　字典的现代句法

### 3.2.1　字典推导式

3.2.1　字典推导式
自 Python 2.7 开始，列表推导式和生成器表达式经过改造，以适用于字典推导式（以及后文要讲的集合推导式）。字典推导式从任何可迭代对象中获取键值对，构建 dict 实例。示例 3-1 使用字典推导式根据同一个元组列表构建两个字典。

示例 3-1　字典推导式示例

>>> dial_codes = [                                                  ❶
...     (880, 'Bangladesh'),
...     (55,  'Brazil'),
...     (86,  'China'),
...     (91,  'India'),
...     (62,  'Indonesia'),
...     (81,  'Japan'),
...     (234, 'Nigeria'),
...     (92,  'Pakistan'),
...     (7,   'Russia'),
...     (1,   'United States'),
... ]
>>> country_dial = {country: code for code, country in dial_codes}  ❷
>>> country_dial
{'Bangladesh': 880, 'Brazil': 55, 'China': 86, 'India': 91, 'Indonesia': 62,
'Japan': 81, 'Nigeria': 234, 'Pakistan': 92, 'Russia': 7, 'United States': 1}
>>> {code: country.upper()                                          ❸
...     for country, code in sorted(country_dial.items())
...     if code < 70}
{55: 'BRAZIL', 62: 'INDONESIA', 7: 'RUSSIA', 1: 'UNITED STATES'}
[2024/2/21 4:24 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/20/2/4/28%5Bnav_point_69%5D/1%3A0%29)

--------------------

## 3.8　字典视图

dict 的实例方法 .keys()、.values() 和 .items() 分别返回 dict_keys、dict_values 和 dict_items 类的实例。这些字典视图是 dict 内部实现使用的数据结构的只读投影。
[2024/2/21 4:35 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/20/2/4/434/2/1%3A0%29)

--------------------

## 3.10　集合论

本书使用“集合”一词指代 set 和 frozenset。
[2024/2/21 4:37 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/20/2/4/482/2/2%3A1%29)

--------------------

集合是一组唯一的对象。集合的基本作用是去除重复项。

>>> l = ['spam', 'spam', 'eggs', 'spam', 'bacon', 'eggs']
>>> set(l)
{'eggs', 'spam', 'bacon'}
>>> list(set(l))
['eggs', 'spam', 'bacon']
[2024/2/21 4:37 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/20/2/4/484/1%3A0%29)

--------------------

如果想去除重复项，同时保留每一项首次出现位置的顺序，那么现在使用普通的 dict 即可，如下所示。

>>> dict.fromkeys(l).keys()
dict_keys(['spam', 'eggs', 'bacon'])
>>> list(dict.fromkeys(l).keys())
['spam', 'eggs', 'bacon']
[2024/2/21 4:38 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/20/2/4/488/2/2%3A1%29)

--------------------

# 第 5 章　数据类构建器

Python 提供了几种构建简单类的方式，这些类只是字段的容器，几乎没有额外功能。这种模式称为“数据类”（data class），dataclasses 包就支持该模式。
[2024/2/22 4:51 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/24/2/4/8/1%3A0%29)

--------------------

## 5.2　数据类构建器概述

下面使用 namedtuple 构建 Coordinate 类。namedtuple 是一个工厂方法，使用指定的名称和字段构建 tuple 的子类。

>>> from collections import namedtuple
>>> Coordinate = namedtuple('Coordinate', 'lat lon')
>>> issubclass(Coordinate, tuple)
True
>>> moscow = Coordinate(55.756, 37.617)
>>> moscow
Coordinate(lat=55.756, lon=37.617)  ❶
[2024/2/22 4:53 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/24/2/4/54/1%3A0%29)

--------------------

# 第 6 章　对象引用、可变性和垃圾回收

## 6.6　del 和垃圾回收

其次，del 语句删除引用，而不是对象。
[2024/2/23 9:41 AM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/26/2/4/374/1%3A0%29)

--------------------

# 第 7 章　函数是一等对象

## 7.3　高阶函数

### map、filter 和 reduce 的现代替代品

sum 和 reduce 的整体运作方式是一样的，即把某个操作连续应用到序列中的项上，累计前一个结果，把一系列值归约成一个值。
[2024/2/23 10:24 AM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/30/2/4/106/2/4/1%3A0%29)

--------------------

## 7.5　9 种可调用对象

生成器函数

　　主体中有 yield 关键字的函数或方法。调用生成器函数返回一个生成器对象。

原生协程函数

　　使用 async def 定义的函数或方法。调用原生协程函数返回一个协程对象。Python 3.5 新增。

异步生成器函数

　　使用 async def 定义，而且主体中有 yield 关键字的函数或方法。调用异步生成器函数返回一个异步生成器，供 async for 使用。Python 3.6 新增。

与其他可调用对象不同，生成器、原生协程和异步生成器函数的返回值不是应用程序数据，而是需要进一步处理的对象，要么产出应用程序数据，要么执行某种操作。生成器函数会返回迭代器（详见第 17 章）。原生协程函数和异步生成器函数返回的对象只能由异步编程框架（例如 asyncio）处理（详见第 21 章）。


[2024/2/23 10:28 AM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/30/2/4/170/2/1%3A0%29)

--------------------

## 7.10　延伸阅读

函数有名称，栈跟踪更易于阅读。匿名函数是一种便利的简洁方式，人们乐于使用它们，但是有时会忘乎所以，尤其是在鼓励深层嵌套匿名函数的语言和环境中，例如 Node.js 之上的 JavaScript。匿名函数嵌套的层级太深，不利于调试和处理错误。
[2024/3/1 9:27 AM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/30/2/4/414/32/1%3A8%29)

--------------------

# 第 10 章　使用一等函数实现设计模式

## 10.2　案例分析：重构策略模式

### 10.2.1　经典的策略模式


class Promotion(ABC):  # 策略：抽象基类
    @abstractmethod
    def discount(self, order: Order) -> Decimal:
        """返回折扣金额（正值）"""


class FidelityPromo(Promotion):  # 第一个具体策略
    """为积分为1000或以上的顾客提供5%折扣"""

    def discount(self, order: Order) -> Decimal:
        rate = Decimal('0.05')
        if order.customer.fidelity >= 1000:
            return order.total() * rate
        return Decimal(0)


[2024/3/1 9:45 AM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/36/2/4/68/2/1%3A930%29)

--------------------

# 第 11 章　符合 Python 风格的对象

## 11.5　classmethod 与 staticmethod

classmethod 装饰器非常有用，但是我从未见过不得不使用 staticmethod 的情况。有些函数即使不直接处理类，也与类联系紧密，因此你会想把函数与类放在一起定义。对于这种情况，在类的前面或后面定义函数，保持二者在同一个模块中基本上就可以了。5


[2024/3/1 2:55 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/40/2/4/152/2/4/1%3A0%29)

--------------------

# 第 17 章　迭代器、生成器和经典协程

## 17.5　为 Sentence 类实现 __iter__ 方法

### 17.5.4　生成器的工作原理

17.5.4　生成器的工作原理
只要 Python 函数的主体中有 yield 关键字，该函数就是生成器函数。调用生成器函数，返回一个生成器对象。也就是说，生成器函数是生成器工厂。
[2024/3/1 3:27 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/54/2/4/286%5Bnav_point_364%5D/1%3A0%29)

--------------------

## 17.11　yield from：从子生成器中产出

17.11　yield from：从子生成器中产出
Python 3.3 新增的 yield from 表达式句法可把一个生成器的工作委托给一个子生成器。

引入 yield from 之前，如果一个生成器根据另一个生成器生成的值产出值，则需要使用 for 循环。

>>> def sub_gen():
...     yield 1.1
...     yield 1.2
...
>>> def gen():
...     yield 1
...     for i in sub_gen():
...         yield i
...     yield 2
...
>>> for x in gen():
...     print(x)
...
1
1.1
1.2
2
使用 yield from 可以达到相同的效果，如示例 17-25 所示。

示例 17-25　测试驱动 yield from

>>> def sub_gen():
...     yield 1.1
...     yield 1.2
...
>>> def gen():
...     yield 1
...     yield from sub_gen()
...     yield 2
...
>>> for x in gen():
...     print(x)
...
1
1.1
1.2
2
[2024/3/1 4:05 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/54/2/4/718%5Bnav_point_373%5D/1%3A0%29)

--------------------

子生成器中有 return 语句时，返回一个值，在委托生成器中，通过含有 yield from 的表达式可以捕获那个值，如示例 17-26 所示。

示例 17-26　yield from 获取子生成器的返回值

>>> def sub_gen():
...     yield 1.1
...     yield 1.2
...     return 'Done!'
...
>>> def gen():
...     yield 1
...     result = yield from sub_gen()
...     print('<--', result)
...     yield 2
...
>>> for x in gen():
...     print(x)
...
1
1.1
1.2
<-- Done!
2
[2024/3/1 4:07 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/54/2/4/734/1%3A0%29)

子生成器中的yield不是返回值


--------------------

# 第 18 章　with、match 和 else 块

第 18 章　with、match 和 else 块
[2024/3/1 5:04 PM](calibre://view-book/_hex_-43616c696272655f4c696272617279/3/EPUB?open_at=epubcfi%28/56/2/4/2%5Bnav_point_383%5D/1%3A0%29)

本书暂时读到此章节，后续需要进阶再读

--------------------