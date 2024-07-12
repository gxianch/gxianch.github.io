---
title: Python工匠：案例、技巧与工程实践
date: 2024-07-09
time: 14:52
categories:
  - 计算机
toc: true
mathjax: true
top: 98
tags:
  - python
  - ✰✰✰✰✰✰
author:
---

五星好评，python开发必读书

![[Pasted image 20240712145118.png]]



<!-- more -->


# 第 3 章 容器类型

而在代码世界里，同样也有“容器”这个概念。代码里的容器泛指那些专门用来装其他对象的特殊数据类型。在 Python 中，最常见的内置容器类型有四种：列表、元组、字典、集合。
## 列表（list）
　　列表（list）是一种非常经典的容器类型，通常用来存放多个同类对象，比如从 1 到 10 的所有整数：


>>> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

## 元组（tuple）
  元组（tuple）和列表非常类似，但跟列表不同，它不能被修改。这意味着元组完成初始化后就没法再改动了：


>>> names = ('foo', 'bar')
>>> names[1] = 'x'
...
TypeError: 'tuple' object does not support item assignment

## 字典（dict）
字典（dict）类型存放的是一个个键值对（key: value）。它功能强大，应用广泛，就连 Python 内部也大量使用，比如每个类实例的所有属性，就都存放在一个名为 dict 的字典里：

```
class Foo:
    def __init__(self, value):
        self.value = value

foo = Foo('bar')
print(foo.__dict__, type(foo.__dict__))
　　执行后输出：


{'value': 'bar'} <class 'dict'>
```

## 集合（set）
集合（set）也是一种常用的容器类型。它最大的特点是成员不能重复，所以经常用来去重（剔除重复元素）：

>>> numbers = [1, 2, 2, 1]
>>> set(numbers)
>>>  {1, 2}
　　
这四种容器类型各有优缺点，适用场景也各不相同。本章将简单介绍每种容器类型的特点，深入分析它们的应用场景，帮你厘清一些常见的概念。更好地掌握容器能帮助你写出更高效的 Python 代码。


## 3.2 案例故事

### 使用 `defaultdict` 类型

defaultdict(default_factory, ...) 是一种特殊的字典类型。它在被初始化时，接收一个可调用对象 default_factory 作为参数。之后每次进行 d[key] 操作时，如果访问的 key 不存在，defaultdict 对象会自动调用 default_factory() 并将结果作为值保存在对应的 key 里。


```python
>>> from collections import defaultdict
>>> int_dict = defaultdict(int)
>>> int_dict['foo'] += 1
```

 当 `int_dict` 发现键 `'foo'` 不存在时，它会调用 `default_factory`——也就是 `int()`——拿到结果 `0`，将其保存到字典后再执行累加操作

### 使用 MutableMapping 创建自定义字典类型

在前面的函数里，有一段核心的字典操作代码：先通过 time_cost 计算出 level，然后以 level 为键将请求数保存到字典中。这段代码的逻辑比较独立，假如把它从函数中抽离出来，代码会变得更好理解。

此时就该自定义字典类型闪亮登场了。自定义字典和普通字典很像，但它可以给字典的默认行为加上一些变化。比如在这个场景下，我们会让字典在操作“响应耗时”键时，直接将其翻译成对应的性能等级。

在 Python 中定义一个字典类型，可通过继承 MutableMapping 抽象类来实现
```Python
from enum import Enum
from collections import defaultdict
from collections.abc import MutableMapping
　
　
class PagePerfLevel(str, Enum):
    LT_100 = 'Less than 100 ms'
    LT_300 = 'Between 100 and 300 ms'
    LT_1000 = 'Between 300 ms and 1 s'
    GT_1000 = 'Greater than 1 s'
　
　
class PerfLevelDict(MutableMapping):
    """存储响应时间性能等级的字典"""

    def __init__(self):
        self.data = defaultdict(int)

    def __getitem__(self, key):
        """当某个性能等级不存在时，默认返回 0"""
        return self.data[self.compute_level(key)]

    def __setitem__(self, key, value):
        """将 key 转换为对应的性能等级，然后设置值"""
        self.data[self.compute_level(key)] = value

    def __delitem__(self, key):
        del self.data[key]

    def __iter__(self):
        return iter(self.data)

    def __len__(self):
        return len(self.data)

    def items(self):
        """按照顺序返回性能等级数据"""
        return sorted(
            self.data.items(),
            key=lambda pair: list(PagePerfLevel).index(pair[0]),
        )

    def total_requests(self):
        """返回总请求数"""
        return sum(self.values())

    @staticmethod
    def compute_level(time_cost_str):
        """根据响应时间计算性能等级"""
        if time_cost_str in list(PagePerfLevel):
            return time_cost_str

        time_cost = int(time_cost_str)
        if time_cost < 100:
            return PagePerfLevel.LT_100
        elif time_cost < 300:
            return PagePerfLevel.LT_300
        elif time_cost < 1000:
            return PagePerfLevel.LT_1000
        return PagePerfLevel.GT_1000

def analyze_v2():
    path_groups = defaultdict(PerfLevelDict)
	path_groups["path"][9] += 1  
	path_groups["path"][9] += 1  
	path_groups["path1"][10] += 1  
	path_groups["path1"][10001] += 1

    for path, result in path_groups.items():
        print(f'== Path: {path}')
        print(f'   Total requests: {result.total_requests()}')
        print(f'   Performance:')
        for level_name, count in result.items():
            print(f'     - {level_name}: {count}')
　
　
if __name__ == '__main__':
    analyze_v2()

```

```shell
== Path: path
   Total requests: 2
   Performance:
     - Less than 100 ms: 2
== Path: path1
   Total requests: 2
   Performance:
     - Less than 100 ms: 1
     - Greater than 1 s: 1
```
编写了一个继承了 MutableMapping 的字典类 PerfLevelDict。但光继承还不够，要让这个类变得像字典一样，还需要重写包括 __getitem__、__setitem__ 在内的 6 个魔法方法

为何不直接继承 dict？

在实现自定义字典时，我让 PerfLevelDict 继承了 collections.abc 下的 MutableMapping 抽象类，而不是内置字典 dict。这看起来有点儿奇怪，因为从直觉上说，假如你想实现某个自定义类型，最方便的选择就是继承原类型。

但是，如果真的继承 dict 来创建自定义字典类型，你会碰到很多问题。

拿一个最常见的场景来说，假如你继承了 dict，通过 __setitem__ 方法重写了它的键赋值操作。此时，虽然常规的 d[key] = value 行为会被重写；但假如调用方使用 d.update(...) 来更新字典内容，就根本不会触发重写后的键赋值逻辑。这最终会导致自定义类型的行为不一致。

正因如此，如果你想创建一个自定义字典，继承 collections.abc 下的 MutableMapping 抽象类是个更好的选择，因为它没有上面的问题。而对于列表等其他容器类型来说，这条规则也同样适用。



## 3.3 编程建议

### 3.3.1 用按需返回替代容器

#### 用生成器替代列表

在日常工作中，我们经常需要编写下面这样的代码：

```python
def batch_process(items):
    """
    批量处理多个 items 对象
    """
    # 初始化空结果列表
    results = []
    for item in items:
        # 处理 item，可能需要耗费大量时间……
        # processed_item = ...
        results.append(processed_item)
    # 将拼装后的结果列表返回
    return results

```


这样的函数遵循同一种模式：“初始化结果容器→处理→将结果存入容器→返回容器”。这个模式虽然简单，但它有两个问题。

一个问题是，如果需要处理的对象 items 过大，batch_process() 函数就会像 Python 2 里的 range() 函数一样，每次执行都特别慢，存放结果的对象 results 也会占用大量内存。

另一个问题是，如果函数调用方想在某个 processed_item 对象满足特定条件时中断，不再继续处理后面的对象，现在的 batch_process() 函数也做不到——它每次都得一次性处理完所有 items 才会返回。

为了解决这两个问题，我们可以用生成器函数来改写它。简单来说，就是用 yield item 替代 append 语句：

```python
def batch_process(items):
    for item in items:
        # 处理 item，可能需要耗费大量时间……
        # processed_item = ...
        yield processed_item
```
生成器函数不仅看上去更短，而且很好地解决了前面的两个问题。当输入参数 items 很大时，batch_process() 不再需要一次性拼装返回一个巨大的结果列表，内存占用更小，执行起来也更快。



### 3.3.2 了解容器的底层实现

#### deque 底层使用了双端队列，无论在头部还是尾部追加成员，时间复杂度都是 O(1)

如果你经常需要往列表头部插入数据，请考虑使用 collections.deque 类型来替代列表（代码如下）。因为 deque 底层使用了双端队列，无论在头部还是尾部追加成员，时间复杂度都是 O(1)。



### 3.3.3 掌握如何快速合并字典
#### 字典类型 | 运算符快速合并

字典类型新增了对 | 运算符的支持。只要执行 d1 | d2，就能快速拿到两个字典合并后的结果：

```python
>>> d1 = {'name': 'apple'}
>>> d2 = {'name': 'orange', 'price': 10}
>>> d1 | d2
{'name': 'orange', 'price': 10}
>>> d2 | d1  ➊
{'name': 'apple', 'price': 10}
```
➊ 运算顺序不同，会影响最终的合并结果





### 3.3.7 让函数返回 NamedTuple

对于这种未来可能会变动的多返回值函数来说，如果一开始就使用 NamedTuple 类型对返回结果进行建模，上面的改动会变得简单许多：

```python

from typing import NamedTuple

class Address(NamedTuple):
    """地址信息结果"""
    country: str
    province: str
    city: str

def latlon_to_address(lat, lon):
    return Address(
        country=country,
        province=province,
        city=city,
    )

addr = latlon_to_address(lat, lon)
# 通过属性名来使用 addr
# addr.country / addr.province / addr.city
```

假如我们在 Address 里增加了新的返回值 district，已有的函数调用代码也不用进行任何适配性修改，因为函数结果只是多了一个新属性，没有任何破坏性影响。




# 第 4 章 条件分支控制流

## 4.1 基础知识

### 4.1.1 分支惯用写法

#### 不要显式地和布尔值做比较：

```python
# 不推荐的写法
# if user.is_active_member() == True:

# 推荐写法
if user.is_active_member():
　　绝大多数情况下，在分支判断语句里写 == True 都没有必要，删掉它代码会更短也更易读。

```


#### 省略零值判断

当你编写 if 分支时，如果需要判断某个类型的对象是否是零值，可能会把代码写成下面这样：

```python
if containers_count == 0:
    ...

if fruits_list != []:
    ...
```
这种判断语句其实可以变得更简单，因为当某个对象作为主角出现在 if 分支里时，解释器会主动对它进行“真值测试”，也就是调用 bool() 函数获取它的布尔值。


####  内置类型的布尔值规则如下。

布尔值为假：None、0、False、[]、()、{}、set()、frozenset()，等等。
布尔值为真：非 0 的数值、True，非空的序列、元组、字典，用户定义的类和实例，等等。


### 4.1.2 修改对象的布尔值
```python
# 不再需要手动判断对象内部 items 的长度
if users:
    print("There's some users in collection!")
　　为类定义 __len__ 魔法方法，实际上就是为它实现了 Python 世界的长度协议：
```

Python 在计算这类对象的布尔值时，会受 len(users) 的结果影响——假如长度为 0，布尔值为 False，反之为 True。因此当例子中的 UserCollection 类实现了 __len__ 后，整个条件判断语句就得到了简化。


### 4.1.3 与 None 比较时使用 is 运算符
```python
因此，仅当你需要判断某个对象是否是 None、True、False 时，使用 is，其他情况下，请使用 ==。
```



# 第 5 章 异常与错误处理

## 5.1 基础知识

### 5.1.1 优先使用异常捕获

#### 获取原谅比许可简单

和 LBYL 相比，EAFP 编程风格更为简单直接，它总是直奔主流程而去，把意外情况都放在异常处理 try/except 块内消化掉。

如果你问我：这两种编程风格哪个更好？我只能说，整个 Python 社区明显偏爱基于异常捕获的 EAFP 风格。这里面的原因很多。


(1) LBYL：每次调用都要先进行额外的 isinstance 和 isdigit 判断。

(2) EAFP：每次调用直接执行转换，返回结果。

另外，和许多其他编程语言不同，在 Python 里抛出和捕获异常是很轻量的操作，即使大量抛出、捕获异常，也不会给程序带来过多额外负担。

所以，每当直觉驱使你写下 if/else 来进行错误分支判断时，请先把这份冲动放一边，考虑用 try 来捕获异常是不是更合适。毕竟，Pythonista1 们喜欢“吃感冒药”胜过“看天气预报”。


### 5.1.2 try 语句常用知识

#### 使用 try 语句块里的 else 分支

异常捕获语句里的 else 表示：仅当 try 语句块里没抛出任何异常时，才执行 else 分支下的内容，效果就像在 try 最后增加一个标记变量一样。


#### 使用空 raise 语句

在处理异常时，有时我们可能仅仅想记录下某个异常，然后把它重新抛出，交由上层处理。这时，不带任何参数的 raise 语句可以派上用场：

### 5.1.3 抛出异常，而不是返回错误

#### 抛出异常，而不是返回错误
　　我们知道，Python 里的函数可以一次返回多个值（通过返回一个元组实现）。所以，当我们要表明函数执行出错时，可以让它同时返回结果与错误信息。


Python 有完善的异常机制，并且在某种程度上鼓励我们使用异常（见 5.1.1 节）。所以，用异常来进行错误处理才是更地道的做法。


### 5.1.4 使用上下文管理器

上下文管理器是一种定义了“进入”和“退出”动作的特殊对象。要创建一个上下文管理器，只要实现 __enter__ 和 __exit__ 两个魔法方法即可。

上下文管理器功能强大、用处很多，其中最常见的用处之一，就是简化异常处理工作。

当程序使用 with 进入一段上下文后，不论里面发生了什么，它在退出这段上下文代码块时，必定会调用上下文管理器的 __exit__ 方法，就和 finally 语句的行为一样。

虽然上下文管理器很好用，但定义一个符合协议的管理器对象其实挺麻烦的——得首先创建一个类，然后实现好几个魔法方法。为了简化这部分工作，Python 提供了一个非常好用的工具：@contextmanager 装饰器。



## 5.3 编程建议

### 5.3.2 不要手动做数据校验

在数据校验这块，pydantic 模块是一个不错的选择。

```python
from pydantic import BaseModel, conint, ValidationError 
class NumberInput(BaseModel): 
# 使用类型注解 conint 定义 number 属性的取值范围 
	number: conint(ge=0, le=100)
```

### 5.3.3 抛出可区分的异常

当开发者编写自定义异常类时，似乎不需要遵循太多原则。常见的几条是：要继承 Exception 而不是 BaseException；异常类名最好以 Error 或 Exception 结尾等


### 5.3.4 不要使用 assert 来检查参数合法性

不要拿 assert 来做参数校验，用 raise 语句来替代


### 5.3.5 无须处理是最好的错误处理

#### 空对象模式

“空对象模式”也是一种转换设计观念以避免错误处理的技巧。当函数进入边界情况时，“空对象模式”不再抛出错误，而是让其返回一个类似于正常结果的特殊对象，因此使用方自然就不必处理任何错误，人们写起代码来也会更轻松。



# 第 6 章 循环与可迭代对象

## 6.1 基础知识

### 6.1.1 迭代器与可迭代对象

生成器（generator）利用其简单的语法，大大降低了迭代器的使用门槛，是优化循环代码时最得力的帮手

生成器关键字yield

### 6.1.2 修饰可迭代对象优化循环

enumerate() 是 Python 的一个内置函数，它接收一个可迭代对象作为参数，返回一个不断生成 ( 当前下标 , 当前元素 ) 的新可迭代对象。



## 6.3 编程建议

### 6.3.1 中断嵌套循环的正确方式--直接使用 return

许许多多的 break 会让代码逻辑变得更难理解，也更容易出现 bug。

如果想快速从嵌套循环里跳出，其实有个更好的做法，那就是把循环代码拆分为一个新函数，然后直接使用 return



# 第 7 章 函数

## 7.1 基础知识

### 7.1.1 函数参数的常用技巧

**别将可变类型作为参数默认值**

在编写函数时，我们经常需要为参数设置默认值。这些默认值可以是任何类型，比如字符串、数值、列表，等等。而当它是可变类型时，怪事儿就会发生。

以下面这个函数为例：

```python
def append_value(value, items=[]):
    """向 items 列表中追加内容，并返回列表"""
    items.append(value)
    return items
```

这样的函数定义看上去没什么问题，但当你多次调用它以后，就会发现函数的行为和预想的不太一样：

```python
>>> append_value('foo')
['foo']
>>> append_value('bar')
['foo', 'bar']
```

可以看到，在第二次调用时，函数并没有返回正确结果 ['bar']，而是返回了 ['foo', 'bar']，这意味着参数 items 的值不再是函数定义的空列表 []，而是变成了第一次执行后的结果 ['foo']。

之所以出现这个问题，是因为 Python 函数的参数默认值只会在函数定义阶段被创建一次，之后不论再调用多少次，函数内拿到的默认值都是同一个对象。

熟悉 Python 的程序员通常不会将可变类型作为参数默认值。这是因为一旦函数在执行时修改了这个默认值，就会对之后的所有函数调用产生影响。

为了规避这个问题，使用 None 来替代可变类型默认值是比较常见的做法：

但建议归建议，在真实的 Python 项目中，接收超过 3 个参数的函数比比皆是。

为什么会这样呢？大概是因为 Python 里的函数不光支持通过有序位置参数（positional argument）调用，还能指定参数名，通过关键字参数（keyword argument）的方式调用。

虽然关键字参数调用模式很有用，但有一个美中不足之处：它只是调用函数时的一种可选方式，无法成为强制要求。不过，我们可以用一种特殊的参数定义语法来弥补这个不足：

#### 参数列表中插入 * 符号
通过在参数列表中插入 * 符号，该符号后的所有参数都变成了“仅限关键字参数”（keyword-only argument）。
如果调用方仍然想用位置参数来提供这些参数值，程序就会抛出错误：


### 7.1.2 函数返回的常见模式

对绝大部分函数而言，返回 None 并不是一个好的做法。

对这些函数来说，用抛出异常来代替返回 None 会更为合理。这也很好理解：当函数被调用时，如果无法返回正常结果，就代表出现了意料以外的状况，而“意料之外”正是异常所掌管的领域。

在这段代码里，函数的 return 数量从 1 个变成了 3 个。试着读读上面的代码，是不是会发现函数的逻辑变得更容易理解了？

产生这种变化的主要原因是，对于读代码的人来说，return 是一种有效的思维减负工具。当我们自上而下阅读代码时，假如遇到了 return，就会清楚知道：“这条执行路线已经结束了”。这部分逻辑在大脑里占用的空间会立刻得到释放，让我们可以专注于下一段逻辑。

### 7.1.3 常用函数模块：functools

#### functools.partial 偏函数

假如在你的项目中，有一个负责进行乘法运算的函数 `multiply()`：

```python
def multiply(x, y):
    return x * y
```

同时，还有许多调用 `multiplay()` 函数进行运算的代码：

```python
result = multiply(2, value)
val = multiply(2, number)
# ...
```
这些代码有一个共同的特点，那就是它们调用函数时的第一个参数都是 `2`——全都是对某个值进行 `*2` 操作。
```python
# 使用 functools.partial，上面的 double() 函数定义可以变得更简洁：
import functools
double = functools.partial(multiply, 2)
```

#### 装饰器缓存 lru_cache()
在缓存方面，functools 模块为我们提供一个开箱即用的工具：lru_cache()。使用它，你可以方便地给函数加上缓存功能，同时不用修改任何函数内部代码。

在使用 lru_cache() 装饰器时，可以传入一个可选的 maxsize 参数，该参数代表当前函数最多可以保存多少个缓存结果。当缓存的结果数量超过 maxsize 以后，程序就会基于“最近最少使用”（least recently used，LRU）算法丢掉旧缓存，释放内存。默认情况下，maxsize 的值为 128。

## 7.2 案例故事

### 函数与状态

相比全局变量，使用闭包最大的特点就是封装性要好得多。在闭包代码里，索引变量 called_cnt 完全处于闭包内部，不会污染全局命名空间，而且不同闭包对象之间也不会相互影响。

总而言之，闭包是一种非常有用的工具，非常适合用来实现简单的有状态函数。

不过，除了闭包之外，还有一个天生就适合用来实现“状态”的工具：类。



在小 R 解答练习题的过程中，一共出现了三种实现有状态函数的方式，这三种方式各有优缺点，总结如下。

#### 基于全局变量：

学习成本最低，最容易理解；
会增加模块级的全局状态，封装性和可维护性最差。

#### 基于函数闭包：

学习成本适中，可读性较好；
适合用来实现变量较少，较简单的有状态函数。

#### 创建类来封装状态：

学习成本较高；
当变量较多、行为较复杂时，类代码比闭包代码更易读，也更容易维护。
在日常编码中，如果你需要实现有状态的函数，应该尽量避免使用全局变量，闭包或类才是更好的选择。



## 7.3 编程建议

### 7.3.4 你没有那么需要 lambda

总之，Python 中的 lambda 函数只是一颗简单的语法糖。它的许多使用场景，要么本身就不存在，要么更适合用 operator 模块来满足。lambda 并非无可替代。

当你确实想要编写 lambda 函数时，请尝试问自己一个问题：“这个功能用 def 写一个普通函数是不是更合适？”尤其当需求比较复杂时，千万别试着把大段逻辑糅进一个巨大的匿名函数里。请记住，没什么特殊功能是 lambda 能做而普通函数做不到的。



### 7.3.5 了解递归的局限性

在编程语言领域，为了避免递归导致调用栈过深，占用过多资源，不少编程语言使用一种被称为尾调用优化（tail call optimization）的技术。这种技术能将 fib() 函数里的递归优化成循环，以此避免嵌套层级过深，提升性能。

但 Python 没有这种技术。因此在使用递归时，你必须对函数的输入数据规模时刻保持警惕，确保它所触发的递归深度，一定远远低于 sys.getrecursionlimit() 的最大限制。



# 第 8 章 装饰器

```python
@cache
def function():
    ...
　　完全等同于下面这样：


def function():
    ...

function = cache(function)
```

装饰器并不提供任何独特的功能，它所做的，只是让我们可以在函数定义语句上方，直接添加用来修改函数行为的装饰器函数。假如没有装饰器，我们也可以在完成函数定义后，手动做一次包装和重新赋值。

## 8.1 基础知识

### 8.1.1 装饰器基础

装饰器是一种通过包装目标函数来修改其行为的特殊高阶函数，绝大多数装饰器是利用函数的闭包原理实现的。
代码清单 8-2 所示的 `timer` 是个简单的装饰器，它会记录并打印函数的每次调用耗时。

```python
def timer(func):
    """装饰器：打印函数耗时"""

    def decorated(*args, **kwargs):
        st = time.perf_counter()
        ret = func(*args, **kwargs)
        print('time cost: {} seconds'.format(time.perf_counter() - st))
        return ret

    return decorated
```

在上面的代码中，`timer` 装饰器接收待装饰函数 `func` 作为唯一的位置参数，并在函数内定义了一个新函数：`decorated`。

在写装饰器时，我一般把 `decorated` 叫作“包装函数”。这些包装函数通常接收任意数目的可变参数 `(*args, **kwargs)`，主要通过调用原始函数 `func` 来完成工作。
timer 是一个无参数装饰器，实现起来较为简单。假如你想实现一个接收参数的装饰器，代码会更复杂一些。
代码清单 8-3 给 `timer` 增加了额外的 `print_args` 参数。

```python
def timer(print_args=False):
    """装饰器：打印函数耗时

    :param print_args: 是否打印方法名和参数，默认为 False
    """

    def decorator(func):
        def wrapper(*args, **kwargs):
            st = time.perf_counter()
            ret = func(*args, **kwargs)
            if print_args:
                print(f'"{func.__name__}", args: {args}, kwargs: {kwargs}')
            print('time cost: {} seconds'.format(time.perf_counter() - st))
            return ret

        return wrapper

    return decorator
```
可以看到，为了增加对参数的支持，装饰器在原本的两层嵌套函数上又加了一层。这是由于整个装饰过程发生了变化所导致的。

❶ 先进行一次调用，传入装饰器参数，获得第一层内嵌函数 decorator

❷ 进行第二次调用，获取第二层内嵌函数 wrapper

在应用有参数装饰器时，一共要做两次函数调用，所以装饰器总共得包含三层嵌套函数。正因为如此，有参数装饰器的代码一直都难写、难读。


### 8.1.2 使用 functools.wraps() 修饰包装函数
```python
def calls_counter(func):
    """装饰器：记录函数被调用了多少次

    使用 func.print_counter() 可以打印统计到的信息
    """
    counter = 0

    def decorated(*args, **kwargs):
        nonlocal counter
        counter += 1
        return func(*args, **kwargs)

    def print_counter():
        print(f'Counter: {counter}')

    decorated.print_counter = print_counter ➊
    return decorated
❶ 为被装饰函数增加额外函数，打印统计到的调用次数
```


### 8.1.3 实现可选参数装饰器

假如你用嵌套函数来实现装饰器，接收参数与不接收参数的装饰器代码有很大的区别——前者总是比后者多一层嵌套。


```python
# 1. 接收参数的装饰器：2 层嵌套
def delayed_start(duration=1):
    def decorator(func):
        def wrapper(*args, **kwargs):
            ...
        return wrapper
    return decorator

# 2. 不接收参数的装饰器：1 层嵌套
def delayed_start(func):
    def wrapper(*args, **kwargs):
        ...
    return wrapper
　　当你实现了一个接收参数的装饰器后，即便所有参数都是有默认值的可选参数，你也必须在使用装饰器时加上括号：


@delayed_start(duration=2) ➊

@delayed_start() ➋
❶ 使用装饰器时提供参数

❷ 不提供参数，也需要使用括号调用装饰器

```

## 8.2 编程建议

### 8.2.1 了解装饰器的本质优势

装饰器的优势并不在于它提供了动态修改函数的能力，而在于它把影响函数的装饰行为移到了函数头部，降低了代码的阅读与理解成本。

为了充分发挥这个优势，装饰器特别适合用来实现以下功能。

(1) 运行时校验：在执行阶段进行特定校验，当校验通不过时终止执行。

适合原因：装饰器可以方便地在函数执行前介入，并且可以读取所有参数辅助校验。
代表样例：Django 框架中的用户登录态校验装饰器 @login_required。

(2) 注入额外参数：在函数被调用时自动注入额外的调用参数。

适合原因：装饰器的位置在函数头部，非常靠近参数被定义的位置，关联性强。
代表样例：unittest.mock 模块的装饰器 @patch。

(3) 缓存执行结果：通过调用参数等输入信息，直接缓存函数执行结果。

适合原因：添加缓存不需要侵入函数内部逻辑，并且功能非常独立和通用。
代表样例：functools 模块的缓存装饰器 @lru_cache。

(4) 注册函数：将被装饰函数注册为某个外部流程的一部分。

适合原因：在定义函数时可以直接完成注册，关联性强。
代表样例：Flask 框架的路由注册装饰器 @app.route

(5) 替换为复杂对象：将原函数（方法）替换为更复杂的对象，比如类实例或特殊的描述符对象（见 12.1.3 节）。

适合原因：在执行替换操作时，装饰器语法天然比 foo = staticmethod(foo) 的写法要直观得多。
代表样例：静态类方法装饰器 @staticmethod。


# 第 9 章 面向对象编程

## 9.1 基础知识

### 9.1.1 类常用知识

**在日常编程中，我们极少使用双下划线来标示一个私有属性。如果你认为某个属性是私有的，直接给它加上单下划线 _ 前缀就够了**。而“标准”的双下划线前缀，反而可能会在子类想要重写父类私有属性时带来不必要的麻烦。

❶ 实例的 __dict__ 里，保存着当前实例的所有数据

❷ 类的 __dict__ 里，保存着类的文档、方法等所有数据


### 9.1.2 内置类方法装饰器

❶ 普通方法接收类实例（self）作为参数，但类方法的第一个参数是类本身，通常使用名字 cls


❶ 虽然类方法通常是用类来调用，但你也可以通过实例来调用类方法，效果一样

如果你发现某个方法不需要使用当前实例里的任何内容，那可以使用 @staticmethod 来定义一个静态方法。


和普通方法相比，静态方法不需要访问实例的任何状态，是一种与状态无关的方法，因此静态方法其实可以改写成脱离于类的外部普通函数。

#### 选择静态方法还是普通函数，可以从以下几点来考虑：

如果静态方法特别通用，与类关系不大，那么把它改成普通函数可能会更好；
如果静态方法与类关系密切，那么用静态方法更好；
相比函数，静态方法有一些先天优势，比如能被子类继承和重写等。


#### @property 装饰器
使用 @property 装饰器，你可以把上面的 get_basename() 方法变成一个虚拟属性，然后像使用普通属性一样使用它：

```python
class FilePath:
    ...
    @property
    def basename(self):
        """获取文件名"""
        return self.path.rsplit(os.sep, 1)[-1]
调用效果如下：


>>> p = FilePath('/tmp/foo.py')
>>> p.basename
'foo.py'

```

### 9.1.3 鸭子类型及其局限性

在鸭子类型编程风格下，如果想操作某个对象，你不会去判断它是否属于某种类型，而会直接判断它是不是有你需要的方法（或属性）。或者更激进一些，你甚至会直接尝试调用需要的方法，假如失败了，那就让它报错好了（参考 5.1.1 节）。

当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以称为鸭子。


### 9.1.4 抽象类

要定义一个抽象类，你需要继承 ABC 类或使用 abc.ABCMeta 元类



### 9.1.6 其他知识

元类是 Python 中的一种特殊对象。元类控制着类的创建行为，就像普通类控制着实例的创建行为一样。

type 是 Python 中最基本的元类，利用 type，你根本不需要手动编写 class ... : 代码来创建一个类——直接调用 type() 就行：


在调用 type() 创建类时，需要提供三个参数，它们的含义如下。

(1) name：str，需要创建的类名。

(2) bases：Tuple[Type]，包含其他类的元组，代表类的所有基类。

(3) attrs：Dict[str, Any]，包含所有类成员（属性、方法）的字典。


## 9.2 案例故事

### 继承是把双刃剑

在多数情况下，基于事物的行为来建模，可以孵化出更好、更灵活的模型设计。


## 9.3 编程建议

### 9.3.1 使用 __init_subclass__ 替代元类

__init_subclass__ 是类的一个特殊钩子方法，它的主要功能是在类派生出子类时，触发额外的操作。假如某个类实现了这个钩子方法，那么当其他类继承该类时，钩子方法就会被触发。


通过上面的例子，你会发现 __init_subclass__ 非常适合在这种需要触达所有子类的场景中使用。而且同元类相比，钩子方法只要求使用者了解继承，不用掌握更高深的元类相关知识，门槛低了不少。它和类装饰器一样，都可以有效替代元类。


### 9.3.3 有序组织你的类方法

**公有方法应该放在类的前面，因为它们是其他模块调用类的入口，是类的门面，也是所有人最关心的内容。以 _ 开头的私有方法，大部分是类自身的实现细节，应该放在靠后的位置。**


至于类方法、静态方法和属性对象，你不必将它们区分对待，直接参考公有 / 私有的思路即可。比如，大部分类方法是公有的，所有它们通常会比较靠前。而静态方法常常是内部使用的私有方法，所以常放在靠后的位置。


最后一点，当你从上往下阅读类时，所有方法的抽象级别应该是不断降低的，就好像阅读一篇新闻一样，第一段是新闻的概要，之后才会描述细节。

### 9.3.4 函数搭配，干活不累

```python
class AppConfig:
    """程序配置类，使用单例模式"""

    _instance = None

    def __new__(cls):
        if cls._instance is None:
            inst = super().__new__(cls)
            # 省略：从外部配置文件读取配置
            ...
            cls._instance = inst
        return cls._instance

    def get_database(self):
        """读取数据库配置"""
        ...

    def reload(self):
        """重新读取配置文件，刷新配置"""
        ...
```
在 Python 中，实现单例模式的方式有很多，而上面这种最为常见，它通过重写类的 __new__ 方法来接管实例创建行为。当 __new__ 方法被重写后，类的每次实例化返回的不再是新实例，而是同一个已经初始化的旧实例 cls._instance：

### 预绑定方法模式（prebound method pattern）

预绑定方法模式（prebound method pattern）是一种将对象方法绑定为函数的模式。要实现该模式，第一步就是完全删掉 AppConfig 里的单例设计模式。因为在 Python 里，实现单例压根儿不用这么麻烦，我们有一个随手可得的单例对象——模块（module）。

当你在 Python 中执行 import 语句导入模块时，无论 import 执行了多少次，每个被导入的模块在内存中只会存在一份（保存在 sys.modules 中）。因此，要实现单例模式，只需在模块里创建一个全局对象即可：

```python
class AppConfig:
    """程序配置类，使用单例模式"""

    def __init__(self): ➊
        # 省略：从外部配置文件读取配置
        ...

_config = AppConfig() ➋
```


# 第 10 章 面向对象设计原则（上）

比如，9.3.4 节就有一个与“单例模式”有关的例子。在示例代码里，我先是用 __new__ 方法实现了经典的单例设计模式。但随后，一个模块级全局对象用更少的代码满足了同样的需求。

```python
# 1：单例模式

class AppConfig:

    _instance = None

    def __new__(cls):
        if cls._instance is None:
            inst = super().__new__(cls)
            cls._instance = inst
        return cls._instance

# 2：全局对象

class AppConfig:
    ...

_config = AppConfig()
```



## 10.1 类型注解基础

下面是添加了类型注解后的代码：

```python
from typing import List


class Duck:
    def __init__(self, color: str): ➊
        self.color = color

    def quack(self) -> None: ➋
        print(f"Hi, I'm a {self.color} duck!")


def create_random_ducks(number: int) -> List[Duck]: ➌
    ducks: List[Duck] = [] ➍
    for _ in number:
        color = random.choice(['yellow', 'white', 'gray']) ➎
        ducks.append(Duck(color=color))
    return ducks


```

❶ 给函数参数加上类型注解

❷ 通过 -> 给返回值加上类型注解

❸ 你可以用 typing 模块的特殊对象 List 来标注列表成员的具体类型，注意，这里用的是 [] 符号，而不是 ()

❹ 声明变量时，也可以为其加上类型注解

❺ 类型注解是可选的，非常自由，比如这里的 color 变量就没加类型注解

typing 是类型注解用到的主要模块，除了 List 以外，该模块内还有许多与类型有关的特殊对象，举例如下。

Dict：字典类型，例如 Dict[str, int] 代表键为字符串，值为整型的字典。
Callable：可调用对象，例如 Callable[[str, str], List[str]] 表示接收两个字符串作为参数，返回字符串列表的可调用对象。
TextIO：使用文本协议的类文件类型，相应地，还有二进制类型 BinaryIO。
Any：代表任何类型。

默认情况下，你可以把 Python 里的类型注解当成一种用于提升代码可读性的特殊注释，因为它就像注释一样，只提升代码的说明性，不会对程序的执行过程产生任何实际影响。

但是，如果引入静态类型检查工具，类型注解就不再仅仅是注解了。它在提升可读性之余，还能对程序正确性产生积极的影响。在 13.1.5 节中，我会介绍如何用 mypy 来做到这一点。


# 第 12 章 数据模型与描述符

## 12.1 基础知识

### 12.1.1 字符串魔法方法
```python
__repr__
```

当你需要把一个 Python 对象用字符串表现出来时，实际上可分为两种场景。第一种场景是非正式的，比如用 print() 打印到屏幕、用 str() 转换为字符串。这种场景下的字符串注重可读性，格式应当对用户友好，由类型的 __str__ 方法所驱动。

第二种场景则更为正式，它一般发生在调试程序时。在调试程序时，你常常需要快速获知对象的详细内容，最好一下子就看到所有属性的值。该场景下的字符串注重内容的完整性，由类型的 __repr__ 方法所驱动。

要让对象在调试场景提供更多有用的信息，我们需要实现 __repr__ 方法。

当你在 __repr__ 方法里组装结果时，一般会尽可能地涵盖当前对象的所有信息，假如其他人能通过复制 repr() 的字符串结果直接创建一个同样的对象，就再好不过了。
下面，我试着给 `Person` 加上 `__repr__` 方法：

```python
class Person:
    ...

    def __str__(self):
        return self.name

    def __repr__(self):
        return '{cls_name}(name={name!r}, age={age!r}, favorite_color={color!r})'.format( ➊
            cls_name=self.__class__.__name__, ➋
            name=self.name,
            age=self.age,
            color=self.favorite_color,
        )
```

> ❶ 在字符串模板里，我使用了 `{name!r}` 这样的语法，变量名后的 `!r` 表示在渲染字符串模板时，程序会优先使用 `repr()` 而非 `str()` 的结果。这么做以后，`self.name` 这种字符串类型在渲染时会包含左右引号，省去了手动添加的麻烦
> 
> ❷ 类名不直接写成 `Person` 以便更好地兼容子类

再来试试看效果如何：

```python
>>> p = Person('piglei', 18, 'black')
>>> print(p)
piglei
>>> p
Person(name='piglei', age=18, favorite_color='black')
```
当对象定义了 __repr__ 方法后，它便可以在任何需要的时候，快速提供一种详尽的字符串展现形式，为程度调试提供帮助。

假如一个类型没定义 __str__ 方法，只定义了 __repr__，那么 __repr__ 的结果会用于所有需要字符串的场景。

