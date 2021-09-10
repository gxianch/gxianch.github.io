---
title: Javascript忍者秘籍(第二版)
date: 2020-12-08 14:28:12
categories: [JavaScript]
toc: true
mathjax: true
top: 98
tags:
  - ✰✰✰✰✰
  - Manning

---

JavaScript知识讲的很深入的一本书，Javascript有此一本足以

{% asset_img label cover.jpg %}

![](Javascript忍者秘籍(第二版)/cover.jpg)

官方上传到github源码:https://github.com/gxianch/secrets-of-js-ninja-2

*原官方地址：https://www.manning.com/books/secrets-of-the-javascript-ninja-second-edition*

<!-- more -->


- > 前言

  - > 控制对象的作用域范围是“忍者”编写代码的关键因素

  - > const 也是块级作用域常量？

  - > 默认参数值

  - > action="skulk"

  - > [...items,3,4,5]

  - > promise对象

  - > 占位符

- > 第1部分 热身

- > 第1章 无所不在的JavaScript

  - > 1995年的一项10天内仓促完成的项目，现在却成为了世界上使用最广泛的编程语言之一

- > 第2章 运行时的页面构建过程

  - > 图2.1 客户端Web应用的周期从用户指定某个网站地址（或单击某个链接）开始，其由两个步骤组成：页面构建和事件处理

  - > 两种不同类型的JavaScript代码：全局代码和函数代码

  - > 函数代码指的是包含在函数中的代码

  - > 全局代码指的是位于函数之外的代码

  - > 全局代码由JavaScript引擎（

  - > 每当遇到这样的代码就一行接一行地执行

  - > 图2.7 当执行了脚本元素中的JavaScript代码后，页面中的DOM结构

  - > 一般来说，JavaScript 代码能够在任何程度上修改DOM结构：它能创建新的接单或移除现有DOM节点。但它依然不能做某些事情，例如
    > 选择和修改还没被创建的节点。这就是为什么要把script元素放在页面底部的原因

    > 选择和修改还没被创建的节点。这就是为什么要把script元素放在页面底部的原因

  - > 。如此一来，我们就不必担心是否某个HTML元素已经加载为DOM。

  - > ● 浏览器事件，例如当页面加载完成后或无法加载时；● 网络事件，例如来自服务器的响应（Ajax事件和服务器端事件）；● 用户事件，例如鼠标单击、鼠标移动和键盘事件；● 计时器事件，当timeout时间到期或又触发了一次时间间隔。

  - > Web应用的JavaScript代码中，大部分内容都是对上述事件的处理！

  - > 在客户端Web应用中，有两种方式注册事件。● 通过把函数赋给某个特殊属性；● 通过使用内置addEventListener方法。

- > 第2部分 理解函数

- > 第3章 新手的第一堂函数课：定义与参数

  - > 回调函数在哪种情况下会同步调用，或者异步调用呢？

  - > 你为什么需要在函数中使用默认参数？

  - > 当我们说函数是第一类对象的时候，就是说函数也能够实现以下功能。● 通过字面量创建。function ninjaFunction（） {}● 赋值给变量，数组项或其他对象的属性。var ninjaFunction = function（） {}; ←--- 为变量赋值一个新函数ninjaArray.push（function（）{}）; ←--- 向数组中增加一个新函数ninja.data = function（）{}; ←--- 给某个对象的属性赋值为一个新函数● 作为函数的参数来传递。function call（ninjaFunction）{ ninjaFunction（）;}call（function（）{}）; ←--- 一个新函数作为参数传递给函数● 作为函数的返回值。function returnNewNinjaFunction（） { return function（）{}; ←--- 返回一个新函数
    > }● 具有动态创建和分配的属性。var ninjaFunction = function（）{};ninjaFunction.ninja = "Hanzo"; ←--- 为函数增加一个新属性对象能做的任何一件事，函数也都能做。函数也是对象，唯一的特殊之处在于它是可调用的（invokable），即函数会被调用以便执行某项动作。

    > }● 具有动态创建和分配的属性。var ninjaFunction = function（）{};ninjaFunction.ninja = "Hanzo"; ←--- 为函数增加一个新属性对象能做的任何一件事，函数也都能做。函数也是对象，唯一的特殊之处在于它是可调用的（invokable），即函数会被调用以便执行某项动作。

  - > 《JavaScript函数式编程》，购买方式见www.manning.com/ books/functional-programming- in-JavaScript。

  - > 本节我们会考察函数和其他对象类型的相似之处。也许让你感到惊讶的相似之处在于我们可以给函数添加属性：var ninja = {};ninja.name = "hitsuke"; ←--- 创建新对象并为其分配一个新属性var wieldSword = function(){};wieldSword.swordType = "katana"; ←--- 创建新函数并为其分配一个新属性

  - > 清单3.2 存储唯一函数集合var store = { nextId: 1, ←--- 跟踪下一个要被复制的函数 cache: {}, ←--- 使用一个对象作为缓存，我们可以在其中存储函数 add: function(fn) { if (!fn.id) { fn.id = this.nextId++; this.cache[fn.id] = fn; return true; } } ←--- 仅当函数唯一时，将该函数加入缓存};function ninja(){}assert(store.add(ninja), "Function was safely added.");assert(!store.add(ninja), "But it was only added once."); ←--- 测试上面代码按预期工作在这个清单中，我们创建了一个对象赋值给变量store，这个变量中存储的是唯一的函数集合。这个对象有两个数据属性：其一是下一个可用的id，另外一个缓存着已经保存的函数。函数通过add()方法添加到缓存中。

  - > JavaScript提供了几种定义函数的方式，可以分为4类。● 函数定义（function declarations）和函数表达式（functionexpressions）——最常用，在定义函数上却有微妙不同的的两种方式。人们通常不会独立地看待它们，但正如你将看到的，意识到两者的不同能帮我们理解函数何时能够被调用。function myFun(){ return 1;}● 箭头函数（通常被叫做lambda函数）——ES6新增的JavaScript标准，能让我们以尽量简洁的语法定义函数。myArg => myArg*2● 函数构造函数—— 一种不常使用的函数定义方式，能让我们以字符串形式动态构造一个函数，这样得到的函数是动态生成的。这个例子动态地创建了一个函数，其参数为a和b，返回值为两个数的和。new Function('a', 'b', 'return a + b')
    > ● 生成器函数——ES6新增功能，能让我们创建不同于普通函数的函数，在应用程序执行过程中，这种函数能够退出再重新进入，在这些再进入之间保留函数内变量的值。我们可以定义生成器版本的函数声明、函数表达式、函数构造函数。function* myGen(){ yield 1; }理解这几种方式的不同很重要，因为函数创建的方式很大程度地影响了函数可被调用的时间、函数的行为以及函数可以在哪个对象上被调用。

    > ● 生成器函数——ES6新增功能，能让我们创建不同于普通函数的函数，在应用程序执行过程中，这种函数能够退出再重新进入，在这些再进入之间保留函数内变量的值。我们可以定义生成器版本的函数声明、函数表达式、函数构造函数。function* myGen(){ yield 1; }理解这几种方式的不同很重要，因为函数创建的方式很大程度地影响了函数可被调用的时间、函数的行为以及函数可以在哪个对象上被调用。

  - > 一个函数被定义在另一个函数之中！function ninja() { function hiddenNinja() { return "ninja here"; } return hiddenNinja();}在JavaScript中，这是一种非常通用的使用方式

  - > 表3.5中最后4个表达式都是立即调用函数表达式主题的4个不同版本，在JavaScript库中会经常见到这几种形式：+function(){}();-function(){}();!function(){}();~function(){}();不同于用加括号的方式区分函数表达式和函数声明，这里我们使用一元操作符+、-、!和~。这种做法也是用于向JavaScript引擎指明它处理的是表达式，而不是语句。从计算机的角度来讲，注意应用一元操作符得到的结果没有存储到任何地方并不重要，只有调用IIFE才重要

  - > 反之，如果形参的数量大于实参，那么那些没有对应实参的形参则会被设为undefined。

  - > 清单3.7 使用剩余参数function multiMax(first, ...remainingNumbers) { ←--- 剩余参数以……做前缀 var sorted = remainingNumbers.sort(function(a, b) { return b – a; ←--- 以降序排序余下参数 }); return first * sorted[0];}assert(multiMax(3, 1, 2, 3) == 9, ←--- 函数调用方式和其他函数类似 "3*3=9 (First arg, by largest.)");为函数的最后一个命名参数前加上省略号（...）前缀，这个参数就变成了一个叫作剩余参数的数组，数组内包含着传入的剩余的参数。

- > 第4章 函数进阶：理解函数调用

  - > ● 函数中两个隐含的参数：arguments和this

  - > 参数this表示被调用函数的上下文对象，而arguments参数表示函数调用过程中传递的所有参数。这两个参数在JavaScript代码中至关重要。

  - > 我们可以通过4种方式调用一个函数，每种方式之间有一些细微差别。● 作为一个函数(function)——skulk()，直接被调用。● 作为一个方法(method)——ninja.skulk()，关联在一个对象上，实现面向对象编程。● 作为一个构造函数(constructor)——new Ninja()，实例化一个新的对象。● 通过函数的apply或者call方法——skulk.apply(ninja)或者skulk.call(ninja)。

  - > function ninja() {};ninja(); ←--- 函数定义作为函数被调用var samurai = function(){};samurai(); ←--- 函数表达式作为函数被调用(function(){})() ←--- 会被立即调用的函数表达式，作为函数被调用当以这种方式调用时，函数上下文（this关键字的值）有两种可能性：在非严格模式下，它将是全局上下文（window对象），而在严格模式下，它将是undefined。

  - > 即使在整个示例中使用的是相同的函数——whatsMyContext，但通过this返回的函数上下文依然取决于whatsMyContext的调用方式。例如，ninja1和ninja2共享了完全相同的函数，但当执行函数时，该函数可以访问并操作所属对象的其他方法。因此我们不需要创建一个单独的函数副本来操作不同的对象进行相同的处理。这也是面向对象编程的魅力所在。

  - > assert(ninja1.skulk() === ninja1, "The 1st ninja is skulking");assert(ninja2.skulk() === ninja2, "The 2nd ninja is skulking"); ←--- 检测已创建对象中的skulk方法。每个方法都应该返回自身已创建的对象

  - > 图4.1 当使用关键字new调用函数时，会创建一个空的对象实例并将其设置为构造函数的上下文（this参数）

  - > 清单4.8 返回原始值的构造函数function Ninja() { ←--- 定义一个叫做Ninja的构造函数 this.skulk = function () { return true; }; return 1; ←--- 构造函数返回一个确定的原始类型值，即数字1}assert(Ninja() === 1, "Return value honored when not called as a constructor"); ←--- 该函数以函数的形式被调
    > 用，正如预期，其返回值为数字1.var ninja = new Ninja(); ←--- 该函数通过new关键字以构造函数的形式被调用assert(typeof ninja === "object", "Object returned when called as a constructor");assert(typeof ninja.skulk === "function", "ninja object has a skulk method"); ←--- 测试表明，返回值1被忽略了，一个新的被初始化的对象被通过关键字new所返回如果执行这段代码，会发现一切正常。事实上，这个Ninja函数虽然返回简单的数字1，但对代码的行为没有显著的影响。如果将Ninja作为一个函数调用，的确会返回1（正如我们预期的），但如果通过new关键字将其作为构造函数调用，会构造并返回一个新的ninja对象

    > 用，正如预期，其返回值为数字1.var ninja = new Ninja(); ←--- 该函数通过new关键字以构造函数的形式被调用assert(typeof ninja === "object", "Object returned when called as a constructor");assert(typeof ninja.skulk === "function", "ninja object has a skulk method"); ←--- 测试表明，返回值1被忽略了，一个新的被初始化的对象被通过关键字new所返回如果执行这段代码，会发现一切正常。事实上，这个Ninja函数虽然返回简单的数字1，但对代码的行为没有显著的影响。如果将Ninja作为一个函数调用，的确会返回1（正如我们预期的），但如果通过new关键字将其作为构造函数调用，会构造并返回一个新的ninja对象

  - > 如果构造函数返回一个对象，则该对象将作为整个表达式的值返回，而传入构造函数的this将被丢弃。● 但是，如果构造函数返回的是非对象类型，则忽略返回值，返回新创建的对象。

  - > 函数和方法的命名通常以描述其行为（skulk、creep、sneak、doSomethingWonderful等）的动词开头，且第一个字母小写。而构造函数则通常以描述所构造对象的名词命名，并以大写字母开头：Ninja、Samurai、Emperor、Ronin等。

  - > 不同类型函数调用之间的主要区别在于：最终作为函数上下文（可以通过this参数隐式引用到）传递给执行函数的对象不同。对于方法而言，即为方法所在的对象；对于顶级函数而言是window或者undefined（取决于是否处于严格模式下）；对于构造函数而言是一个新创建的对象实例。但是，如果想改变函数上下文怎么办？如果想要显式指定它怎么办？

  - > 若想使用apply方法调用函数，需要为其传递两个参数：作为函数上下文的对象和一个数组作为函数调用的参数。call方法的使用方式类似，不同点在于是直接以参数列表的形式，而不再是作为数组传递。

  - > 值得注意的是，apply和call之间唯一的不同之处在于如何传递参数。在使用apply的情况下，我们使用参数数组；在使用call的情况下，我们则在函数上下文之后依次列出调用参数，如图4.3所示。

  - > 迭代函数接收需要遍历的目标对象数组作为第一个参数，回调函数作为第二个参数。迭代函数遍历数组，对每个数组元素执行回调函数：function forEach(list,callback) { for (var n = 0; n < list.length; n++) { callback.call(list[n], n); }}使用call方法调用回调函数，将当前遍历到的元素作为第一个参数，循环索引作为第二个参数，使得当前元素作为函数上下文，循环索引作为回调函数的参数。

  - > 箭头函数作为回调函数还有一个更优秀的特性：箭头函数没有单独的this值。箭头函数的this与声明所在的上下文的相同。

  - > 用箭头函数解决回调函数上下文问题

  - > () => { ←--- 声明用于处下点击事件的箭头函数。因为 click是对象的方法，我们在函数内部使用this获得对象的引用 this.clicked = true;

  - > 图4.6 箭头函数自身不含上下文，从定义时的所在函数继承上下文。箭头回调函数内的this指向按钮对象

  - > 调用箭头函数时，不会隐式传入this参数，而是从定义时的函数继承上下文。

  - > 所有函数均可访问bind方法，可以创建并返回一个新函数，并绑定在传入的对象上（在本例中，绑定在button对象上）。不管如何调用该函数，this均被设置为对象本身。被绑定的函数与原始函数行为一致，函数体一致。

  - > this表示函数上下文，即与函数调用相关联的对象。函数的定义方式和调用方式决定了this的取值。

  - > ● 函数的调用方式有4种。○ 作为函数调用：skulk()。○ 作为方法调用：ninja.skulk()。○ 作为构造函数调用：new Ninja()。○ 通过apply与call方法调用：skulk.apply(ninja)或skulk.call(ninja)。● 函数的调用方式影响this的取值。○ 如果作为函数调用，在非严格模式下，this指向全局window对象；在严格模式下，this指向undefined。○ 作为方法调用，this通常指向调用的对。○ 作为构造函数调用，this指向新创建的对象。○ 通过call或apply调用，this指向call或apply的第一个参数。● 箭头函数没有单独的this值，this在箭头函数创建时确定。● 所有函数均可使用bind方法，创建新函数，并绑定到bind方法传入的参数上。被绑定的函数与原始函数具有一致的行为。

- > 第5章 精通函数：闭包和作用域

  - > 闭包允许函数访问并操作函数外部的变量。只要变量或函数存在于声明函数时的作用域内，闭包即可使函数能够访问这些变量或函数。

  - > 这怎么可能呢？是什么魔法使得在内部函数的作用域消失之后再执行内部函数时，其内部变量仍然存在呢？

  - > 当在外部函数中声明内部函数时，不仅定义了函数的声明，而且还创建了一个闭包

  - > 该闭包不仅包含了函数的声明，还包含了在函数声明时该作用域中的所有变量。当最终执行内部函数时，尽管声明时的作用域已经消失了，但是通过闭包，仍然能够访问到原始作用域

  - > 图5.3 正如保护气泡一样，只要内部函数一直存在，内部函数的闭包就一直保存着该函数的作用域中的变量

  - > 通过使用闭包，可以通过方法对ninja的状态进行维护，而不允许用户直接访问——这是因为闭包内部的变量可以通过闭包内的方法访问，构造器外部的代码则不能访问闭包内部的变量。

  - > 如果没有闭包，一次性同时做许多事情，例如事件绑定、动画甚至服务端请求等，都将会变得非常困难。如果你想知道关注闭包的理由，那么这就是理
    > 由！

    > 由！

  - > 既然具有两种类型的代码，那么就有两种执行上下文：全局执行上下文和函数执行上下文。二者最重要的差别是：全局执行上下文只有一个，当JavaScript程序开始执行时就已经创建了全局上下文；而函数执行上下文是在每次调用函数时，就会创建一个新的。

  - > 第2章介绍了JavaScript基于单线程的执行模型：在某个特定的时刻只能执行特定的代码。一旦发生函数调用，当前的执行上下文必须停止执行，并创建新的函数执行上下文来执行函数。当函数执行完成后，将函数执行上下文销毁，并重新回到发生调用时的执行上下文中。所以需要跟踪执行上下文——正
    > 在执行的上下文以及正在等待的上下文。最简单的跟踪方法是使用执行上下文栈（或称为调用栈）。

    > 在执行的上下文以及正在等待的上下文。最简单的跟踪方法是使用执行上下文栈（或称为调用栈）。

  - > 图5.6 执行上下文栈的行为

  - > 图5.9 JavaScript引擎如何查找变量的值

- > 第6章 未来的函数：生成器和promise

- > 第3部分 深入钻研对象，强化代码

- > 第7章 面向对象与原型

  - > 清单7.2 通过原型方法创建新的实例

  - > ←--- 使用字面量对象完全重写Ninja的原型对象，仅有一个pierce方法

  - > 我们真正想要实现的是一个完整的原型链，在原型链上，Ninja继承自Person，Person继承自Mammal，Mammal继承自Animal，以此类推，一直到Object。创建这样的原型链最佳技术方案是一个对象的原型直接是另一个对象的实例：SubClass.prototype = new SuperClass(); 例如：Ninja.prototype = new Person();

  - > Ninja.prototype = new Person(); ←--- 通过将Ninja的原型赋值为Person的实例，实现Ninja继承Person

  - > 通过简单赋值语句创建对象属性，例如：ninja.name = "Yoshi";该赋值语句创建的属性可被修改或删除、可遍历、可写，Ninja的name属性值被设置为Yoshi，get和set函数均为undefined。如果想调整属性的配置信息，我们可以使用内置的Object.defineProperty方法，传入3个参数：属性所在的对象、属性名和属性描述对象。查看清单7.9中的示例代码。清单7.9 配置属性var ninja = {};ninja.name = "Yoshi";ninja.weapon = "kusarigama"; ←--- 创建一个空对象；通过赋值语句添加对象属性Object.defineProperty(ninja, "sneaky", { configurable: false, enumerable: false, value: true, writable: true}); ←--- 使用内置的`Object.defineProperty`方法设置对象属性的配置信息

  - > ES6引入新的关键字class，它提供了一种更为优雅的创建对象和实现继承的方式，底层仍然是基于原型的实现。如清单7.13所示，使用关键字class的方法很简单。

  - > 清单7.13 在ES6中创建类class Ninja { ←--- 使用ES6指定的关键字class创建类 constructor(name){
    > this.name = name; } ←--- 定义一个构造函数，当使用关键字new调用类时，会调用这个构造函数 swingSword(){ return true; } ←--- 定义一个所有Ninja实例均可访问的方法}

    > this.name = name; } ←--- 定义一个构造函数，当使用关键字new调用类时，会调用这个构造函数 swingSword(){ return true; } ←--- 定义一个所有Ninja实例均可访问的方法}

  - > class是语法糖前面也提到过，虽然ES6引入关键字class，但是底层仍然是基于原型的实现。class只是语法糖，使得在JavaScript模拟类的代码更为简洁。清单7.13中的代码可转换成如下ES5的代码：function Ninja(name) { this.name = name;}Ninja.prototype.swingSword = function() { return true;};

  - > ● 可以通过Object.setPrototypeOf方法定义对象的原型。

  - > ● 在JavaScript中，原型具有属性（如configurable、enumerable、writable）。这些属性可通过内置的Object.defineProperty方法进行定义。

- > 第8章 控制对象的访问

  - > 图8.2 清单8.2的输出：如果一个属性具有getter和setter方法，访问该属性时将隐式调用getter方法，为该属性赋值时将隐式调用setter方法

- > 第9章 处理集合

- > 第10章 正则表达式

- > 第11章 代码模块化

- > 第4部分 洞悉浏览器

- > 第12章 DOM操作

  - > HTML字符串转DOM结构不是特别。事实上，它主要用到了一个大家都很熟悉的工具：innerHTML属性。

- > 第13章 历久弥新的事件

  - > 这些操作被称为任务，并且分为两类：宏任务（或通常称为任务）和微任务。宏任务的例子很多，包括创建主文档对象、解析HTML、执行主线（或全局）JavaScript代码，更改当前URL以及各种事件，如页面加载、输入、网络事件和定时器事件。从浏览器的角度来看，宏任务代表一个个离散的、独立工作单元。运行完任务后，浏览器可以继续其他调度，如重新渲染页面的UI或执行垃圾回收。而微任务是更小的任务。微任务更新应用程序的状态，但必须在浏览器任务继续执行其他任务之前执行，浏览器任务包括重新渲染页面的UI。微任务的案例包括promise回调函数、DOM发生变化等。微任务需要尽可能快地、通过异步方式执行，同时不能产生全新的微任务。微任务使得我们能够在重新渲染UI之前执行指定的行为，避免不必要的UI重绘，UI重绘会使应用程序的状态不连续。

  - > 事件循环的实现至少应该含有一个用于宏任务的队列和至少一个用于微任
    > 务的队列。大部分的实现通常会更多用于不同类型的宏任务和微任务的队列。这使得事件循环能够根据任务类型进行优先处理

    > 务的队列。大部分的实现通常会更多用于不同类型的宏任务和微任务的队列。这使得事件循环能够根据任务类型进行优先处理

  - > 图13.1 事件循环通常至少需要两个任务队列：宏任务队列和微任务队列。两种队列在同一时刻都只执行一个任务

  - > 注意处理宏任务和微任务队列之间的区别：单次循环迭代中，最多处理一个宏任务（其余的在队列中等待），而队列中的所有微任务都会被处理。

  - > 图13.2 时间表显示了当事件发生时任务是如何添加到队列中的。当一个任务执行完成，事件循环将该任务移除队列，并开始执行下一个任务

  - > 由于JavaScript基于单线程执行模型，单击firstButton并不会立即执行对应的处理器。（记住，一个任务一旦开始执行，就不会被另一个任务中断）firstButton的事件处理器则进入任务队列，等待执行。当单击secondButton时发生类似的情况：对应的事件处理器进入队列，等待执行。注意，事件监测和添加任务是独立于事件循环的，尽管主线程仍在执行，仍然可以向队列添加任务。

  - > JavaScript引擎本应立即调用回调函数，因为我们已知promise成功兑现。但是，为了连续性，JavaScript引擎不会这么做，仍然会在firstHandler代码执行（需要运行8ms）完成之后再异步调用回调函数。通过创建微任务，将回调放入微任务队列。

  - > 图13.6 如果微任务队列中含有微任务，不论队列中等待的其他任务，微任务都将获得优先执行权。在本例中，promise微任务优先于secondButton单击任务开始执行

  - > 图13.7 在第一个按钮的单击处理器执行过程中，创建一个已兑现的promise。在微任务队列中，出现一个在等待中的微任务，该微任务会尽可能快地被执行，但是不会中断当前正在运行中的任务

  - > 如果按先后顺序，那么应该先执行secondButton单击事件才算公平，但是我们已经提到过，微任务是很小的任务，需要尽可能快地执行。微任务具有优先执行权

- > 第14章 跨浏览器开发技巧

