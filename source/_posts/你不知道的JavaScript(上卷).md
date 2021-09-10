---
title: 你不知道的JavaScript(上卷)
date: 2020-12-11 08:55:29
categories: [JavaScript]
toc: true
mathjax: true
top: 98
tags:
  - ✰✰✰✰✰
  - OReilly

---
和Javascript忍者秘籍并列的Javascript神书，五星必看，英文名You-Dont-Know-JS

{% asset_img label cover.jpg %}
![](你不知道的JavaScript(上卷)/cover.jpg)



源码：https://github.com/getify/You-Dont-Know-JS

fork地址：https://github.com/gxianch/You-Dont-Know-JS

*注意：有一版，二版及中文版的分支*

<!-- more -->

## 你不知道的JavaScript（上卷）

- > 第一部分作用域和闭包

- > 第 1 章作用域是什么

  - > 1.2.4 引擎和作用域的对话function foo(a) { console.log( a ); // 2 } foo( 2 );让我们把上面这段代码的处理过程想象成一段对话，这段对话可能是下面这样的。引擎：我说作用域，我需要为 foo 进行 RHS 引用。你见过它吗？作用域：别说，我还真见过，编译器那小子刚刚声明了它。它是一个函数，给你。引擎：哥们太够意思了！好吧，我来执行一下 foo。引擎：作用域，还有个事儿。我需要为 a 进行 LHS 引用，这个你见过吗？作用域：这个也见过，编译器最近把它声名为 foo 的一个形式参数了，拿去吧。引擎：大恩不言谢，你总是这么棒。现在我要把 2 赋值给 a。引擎：哥们，不好意思又来打扰你。我要为 console 进行 RHS 引用，你见过它吗？作用域：咱俩谁跟谁啊，再说我就是干这个。这个我也有，console 是个内置对象。给你。引擎：么么哒。我得看看这里面是不是有 log(..)。太好了，找到了，是一个函数。引擎：哥们，能帮我再找一下对 a 的 RHS 引用吗？虽然我记得它，但想再确认一次。作用域：放心吧，这个变量没有变动过，拿走，不谢。引擎：真棒。我来把 a 的值，也就是 2，传递进 log(..)。……

  - > 不成功的 RHS 引用会导致抛出 ReferenceError 异常。不成功的 LHS 引用会导致自动隐式地创建一个全局变量（非严格模式下），该变量使用 LHS 引用的目标作为标识符，或者抛出 ReferenceError 异常（严格模式下）。

- > 第 2 章词法作用域

  - > 考虑以下代码：function foo(a) { var b = a * 2; function bar(c) { console.log( a, b, c ); } bar( b * 3 ); } foo( 2 ); // 2, 4, 12在这个例子中有三个逐级嵌套的作用域。为了帮助理解，可以将它们想象成几个逐级包含的气泡。 包含着整个全局作用域，其中只有一个标识符：foo。 包含着 foo 所创建的作用域，其中有三个标识符：a、bar 和 b。 包含着 bar 所创建的作用域，其中只有一个标识符：c。作用域气泡由其对应的作用域块代码写在哪里决定，它们是逐级包含的。下一章会讨论不同类型的作用域，但现在只要假设每一个函数都会创建一个新的作用域气泡就好了。bar 的气泡被完全包含在 foo 所创建的气泡中，唯一的原因是那里就是我们希望定义函数bar 的位置。

- > 第 3 章函数作用域和块作用域

  - > \2. let循环一个 let 可以发挥优势的典型例子就是之前讨论的 for 循环。for (let i=0; i<10; i++) { console.log( i ); } console.log( i ); // ReferenceErrorfor 循环头部的 let 不仅将 i 绑定到了 for 循环的块中，事实上它将其重新绑定到了循环的每一个迭代中，确保使用上一个循环迭代结束时的值重新进行赋值。

- > 第 4 章提升

  - > 考虑以下代码：a = 2; var a; console.log( a );你认为 console.log(..) 声明会输出什么呢？很多开发者会认为是 undefined，因为 var a 声明在 a = 2 之后，他们自然而然地认为变量被重新赋值了，因此会被赋予默认值 undefined。但是，真正的输出结果是 2。

  - > 可以看到，函数声明会被提升，但是函数表达式却不会被提升。

  - > 我们习惯将 var a = 2; 看作一个声明，而实际上 JavaScript 引擎并不这么认为。它将 var a和 a = 2 当作两个单独的声明，第一个是编译阶段的任务，而第二个则是执行阶段的任务。

- > 第 5 章作用域闭包

  - > 当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

  - > 下面用一些代码来解释这个定义。function foo() { var a = 2; function bar() { console.log( a ); // 2 } bar(); } foo();这段代码看起来和嵌套作用域中的示例代码很相似。基于词法作用域的查找规则，函数bar() 可以访问外部作用域中的变量 a（这个例子中的是一个 RHS 引用查询）。这是闭包吗？技术上来讲，也许是。但根据前面的定义，确切地说并不是

  - > 下面我们来看一段代码，清晰地展示了闭包：function foo() { var a = 2; function bar() { console.log( a ); } return bar; } var baz = foo(); baz(); // 2 —— 朋友，这就是闭包的效果。函数 bar() 的词法作用域能够访问 foo() 的内部作用域。然后我们将 bar() 函数本身当作一个值类型进行传递。在这个例子中，我们将 bar 所引用的函数对象本身当作返回值。在 foo() 执行后，其返回值（也就是内部的 bar() 函数）赋值给变量 baz 并调用 baz()，实际上只是通过不同的标识符引用调用了内部的函数 bar()。bar() 显然可以被正常执行。但是在这个例子中，它在自己定义的词法作用域以外的地方执行。在 foo() 执行后，通常会期待 foo() 的整个内部作用域都被销毁，因为我们知道引擎有垃圾回收器用来释放不再使用的内存空间。由于看上去 foo() 的内容不会再被使用，所以很自然地会考虑对其进行回收。而闭包的“神奇”之处正是可以阻止这件事情的发生。事实上内部作用域依然存在，因此没有被回收。谁在使用这个内部作用域？原来是 bar() 本身在使用。拜 bar() 所声明的位置所赐，它拥有涵盖 foo() 内部作用域的闭包，使得该作用域能够一直存活，以供 bar() 在之后任何时间进行引用。bar() 依然持有对该作用域的引用，而这个引用就叫作闭包。

  - > 本质上无论何时何地，如果将函数（访问它们各自的词法作用域）当作第一级的值类型并到处传递，你就会看到闭包在这些函数中的应用。在定时器、事件监听器、Ajax 请求、跨窗口通信、Web Workers 或者任何其他的异步（或者同步）任务中，只要使用了回调函数，实际上就是在使用闭包！

  - > 模块也是普通的函数，因此可以接受参数：function CoolModule(id) { function identify() { console.log( id ); } return { identify: identify }; } var foo1 = CoolModule( "foo 1" ); var foo2 = CoolModule( "foo 2" );

  - > 模块模式另一个简单但强大的变化用法是，命名将要作为公共 API 返回的对象：var foo = (function CoolModule(id) { function change() { // 修改公共 API publicAPI.identify = identify2; } function identify1() { console.log( id ); } function identify2() { console.log( id.toUpperCase() ); } var publicAPI = { change: change, identify: identify1 }; return publicAPI; })( "foo module" ); foo.identify(); // foo module foo.change(); foo.identify(); // FOO MODULE通过在模块实例的内部保留对公共 API 对象的内部引用，可以从内部对模块实例进行修改，包括添加或删除方法和属性，以及修改它们的值。

  - > 5.5.1 现代的模块机制大多数模块依赖加载器 / 管理器本质上都是将这种模块定义封装进一个友好的 API。这里并不会研究某个具体的库，为了宏观了解我会简单地介绍一些核心概念：var MyModules = (function Manager() { var modules = {}; function define(name, deps, impl) { for (var i=0; i作用域闭包 ｜ 55 return { define: define, get: get }; })();这段代码的核心是 modules[name] = impl.apply(impl, deps)。为了模块的定义引入了包装函数（可以传入任何依赖），并且将返回值，也就是模块的 API，储存在一个根据名字来管理的模块列表中。下面展示了如何使用它来定义模块：MyModules.define( "bar", [], function() { function hello(who) { return "Let me introduce: " + who; } return { hello: hello }; } ); MyModules.define( "foo", ["bar"], function(bar) { var hungry = "hippo"; function awesome() { console.log( bar.hello( hungry ).toUpperCase() ); } return { awesome: awesome }; } ); var bar = MyModules.get( "bar" ); var foo = MyModules.get( "foo" ); console.log( bar.hello( "hippo" ) ); // Let me introduce: hippo foo.awesome(); // LET ME INTRODUCE: HIPPO"foo" 和 "bar" 模块都是通过一个返回公共 API 的函数来定义的。"foo" 甚至接受 "bar" 的示例作为依赖参数，并能相应地使用它。

    > 作用域闭包 ｜ 55 return { define: define, get: get }; })();这段代码的核心是 modules[name] = impl.apply(impl, deps)。为了模块的定义引入了包装函数（可以传入任何依赖），并且将返回值，也就是模块的 API，储存在一个根据名字来管理的模块列表中。下面展示了如何使用它来定义模块：MyModules.define( "bar", [], function() { function hello(who) { return "Let me introduce: " + who; } return { hello: hello }; } ); MyModules.define( "foo", ["bar"], function(bar) { var hungry = "hippo"; function awesome() { console.log( bar.hello( hungry ).toUpperCase() ); } return { awesome: awesome }; } ); var bar = MyModules.get( "bar" ); var foo = MyModules.get( "foo" ); console.log( bar.hello( "hippo" ) ); // Let me introduce: hippo foo.awesome(); // LET ME INTRODUCE: HIPPO"foo" 和 "bar" 模块都是通过一个返回公共 API 的函数来定义的。"foo" 甚至接受 "bar" 的示例作为依赖参数，并能相应地使用它。

- > 第二部分this和对象原型

- > 第 1 章关于this

  - > 另一种方法是强制 this 指向 foo 函数对象：function foo(num) { console.log( "foo: " + num ); // 记录 foo 被调用的次数 // 注意，在当前的调用方式下（参见下方代码），this 确实指向 foo this.count++; } foo.count = 0; var i; for (i=0; i<10; i++) { if (i > 5) { // 使用 call(..) 可以确保 this 指向函数对象 foo 本身 foo.call( foo, i ); } } // foo: 6 // foo: 7 // foo: 8 // foo: 9 // foo 被调用了多少次？ console.log( foo.count ); // 4这次我们接受了 this，没有回避它。如果你仍然感到困惑的话，不用担心，之后我们会详细解释具体的原理。

  - > this 实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。

- > 第 2 章this全面解析

  - > 下面我们来看看到底什么是调用栈和调用位置：function baz() { // 当前调用栈是：baz // 因此，当前调用位置是全局作用域 console.log( "baz" ); bar(); // <-- bar 的调用位置 } function bar() {
    > this全面解析 ｜ 83 // 当前调用栈是 baz -> bar // 因此，当前调用位置在 baz 中 console.log( "bar" ); foo(); // <-- foo 的调用位置 } function foo() { // 当前调用栈是 baz -> bar -> foo // 因此，当前调用位置在 bar 中 console.log( "foo" ); } baz(); // <-- baz 的调用位置注意我们是如何（从调用栈中）分析出真正的调用位置的，因为它决定了 this 的绑定。你可以把调用栈想象成一个函数调用链，就像我们在前面代码段的注释中所写的一样。但是这种方法非常麻烦并且容易出错。另一个查看调用栈的方法是使用浏览器的调试工具。绝大多数现代桌面浏览器都内置了开发者工具，其中包含 JavaScript 调试器。就本例来说，你可以在工具中给 foo() 函数的第一行代码设置一个断点，或者直接在第一行代码之前插入一条 debugger;语句。运行代码时，调试器会在那个位置暂停，同时会展示当前位置的函数调用列表，这就是你的调用栈。因此，如果你想要分析 this 的绑定，使用开发者工具得到调用栈，然后找到栈中第二个元素，这就是真正的调用位置。

    > this全面解析 ｜ 83 // 当前调用栈是 baz -> bar // 因此，当前调用位置在 baz 中 console.log( "bar" ); foo(); // <-- foo 的调用位置 } function foo() { // 当前调用栈是 baz -> bar -> foo // 因此，当前调用位置在 bar 中 console.log( "foo" ); } baz(); // <-- baz 的调用位置注意我们是如何（从调用栈中）分析出真正的调用位置的，因为它决定了 this 的绑定。你可以把调用栈想象成一个函数调用链，就像我们在前面代码段的注释中所写的一样。但是这种方法非常麻烦并且容易出错。另一个查看调用栈的方法是使用浏览器的调试工具。绝大多数现代桌面浏览器都内置了开发者工具，其中包含 JavaScript 调试器。就本例来说，你可以在工具中给 foo() 函数的第一行代码设置一个断点，或者直接在第一行代码之前插入一条 debugger;语句。运行代码时，调试器会在那个位置暂停，同时会展示当前位置的函数调用列表，这就是你的调用栈。因此，如果你想要分析 this 的绑定，使用开发者工具得到调用栈，然后找到栈中第二个元素，这就是真正的调用位置。

  - > 2.2.1 默认绑定

  - > 2.2.2 隐式绑定另一条需要考虑的规则是调用位置是否有上下文对象，或者说是否被某个对象拥有或者包含，不过这种说法可能会造成一些误导。思考下面的代码：function foo() { console.log( this.a ); } var obj = { a: 2, foo: foo }; obj.foo(); // 2

  - > function foo() { console.log( this.a ); } var obj = { a: 2, foo: foo }; var bar = obj.foo; // 函数别名！ var a = "oops, global"; // a 是全局对象的属性 bar(); // "oops, global"虽然 bar 是 obj.foo 的一个引用，但是实际上，它引用的是 foo 函数本身，因此此时的bar() 其实是一个不带任何修饰的函数调用，因此应用了默认绑定。

  - > fn(); // <-- 调用位置！

  - > doFoo( obj.foo ); // "oops, global"

  - > 参数传递其实就是一种隐式赋值，因此我们传入函数时也会被隐式赋值，所以结果和上一个例子一样。

  - > 就像我们看到的那样，回调函数丢失 this 绑定是非常常见的。

  - > 2.2.3 显式绑定

  - > 具体点说，可以使用函数的 call(..) 和apply(..) 方法。严格来说，JavaScript 的宿主环境有时会提供一些非常特殊的函数，它们并没有这两个方法。但是这样的函数非常罕见，JavaScript 提供的绝大多数函数以及你自己创建的所有函数都可以使用 call(..) 和 apply(..) 方法。

  - > 这两个方法是如何工作的呢？它们的第一个参数是一个对象，它们会把这个对象绑定到this，接着在调用函数时指定这个 this。因为你可以直接指定 this 的绑定对象，因此我们称之为显式绑定。

  - > 思考下面的代码：function foo() { console.log( this.a ); } var obj = { a:2 }; foo.call( obj ); // 2通过 foo.call(..)，我们可以在调用 foo 时强制把它的 this 绑定到 obj 上

  - > 如果你传入了一个原始值（字符串类型、布尔类型或者数字类型）来当作 this 的绑定对象，这个原始值会被转换成它的对象形式（也就是 new String(..)、new Boolean(..) 或者new Number(..)）。这通常被称为“装箱”。

  - > var bar = function() { foo.call( obj ); };

  - > 我们来看看这个变种到底是怎样工作的。我们创建了函数 bar()，并在它的内部手动调用了 foo.call(obj)，因此强制把 foo 的 this 绑定到了 obj。无论之后如何调用函数 bar，它总会手动在 obj 上调用 foo。这种绑定是一种显式的强制绑定，因此我们称之为硬绑定。

  - > var bar = function() { return foo.apply( obj, arguments ); };

  - > var bar = foo.bind( obj );

  - > bind(..) 会返回一个硬编码的新函数，它会把参数设置为 this 的上下文并调用原始函数。

  - > 思考下面的代码：function foo(a) { this.a = a; } var bar = new foo(2); console.log( bar.a ); // 2使用 new 来调用 foo(..) 时，我们会构造一个新对象并把它绑定到 foo(..) 调用中的 this上。new 是最后一种可以影响函数调用时 this 绑定行为的方法，我们称之为 new 绑定。

  - > obj2

  - > obj1

  - > 可以看到，显式绑定优先级更高

  - > new obj1.foo( 4 );

  - > new 绑定比隐式绑定优先级高。

  - > 判断this现在我们可以根据优先级来判断函数在某个调用位置应用的是哪条规则。可以按照下面的顺序来进行判断：1. 函数是否在 new 中调用（new 绑定）？如果是的话 this 绑定的是新创建的对象。var bar = new foo()2. 函数是否通过 call、apply（显式绑定）或者硬绑定调用？如果是的话，this 绑定的是指定的对象。var bar = foo.call(obj2)3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话，this 绑定的是那个上下文对象。var bar = obj1.foo()4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 undefined，否则绑定到全局对象。var bar = foo()就是这样。对于正常的函数调用来说，理解了这些知识你就可以明白 this 的绑定原理了。不过……凡事总有例外。

  - > 2.6 小结如果要判断一个运行中函数的 this 绑定，就需要找到这个函数的直接调用位置。找到之后就可以顺序应用下面这四条规则来判断 this 的绑定对象。1. 由 new 调用？绑定到新创建的对象。2. 由 call 或者 apply（或者 bind）调用？绑定到指定的对象。3. 由上下文对象调用？绑定到那个上下文对象。4. 默认：在严格模式下绑定到 undefined，否则绑定到全局对象。

- > 第 3 章对象

- > 第 4 章混合对象“类”

- > 第 5 章原型

  - > 所有普通的 [[Prototype]] 链最终都会指向内置的 Object.prototype。

  - > 这个 Object.prototype 对象，所以它包含 JavaScript 中许多通用的功能。

  - > 尽管 myObject.a++ 看起来应该（通过委托）查找并增加 anotherObject.a 属性，但是别忘了 ++ 操作相当于 myObject.a = myObject.a + 1。因此 ++ 操作首先会通过 [[Prototype]]查找属性 a 并从 anotherObject.a 获取当前属性值 2，然后给这个值加 1，接着用 [[Put]]将值 3 赋给 myObject 中新建的屏蔽属性 a，天呐！修 改 委 托 属 性 时 一 定 要 小 心。 如 果 想 让 anotherObject.a 的 值 增 加， 唯 一 的 办 法 是anotherObject.a++。

  - > 调用 new Foo() 时会创建 a（具体的 4 个步骤参见第 2 章），其中的一步就是给 a 一个内部的 [[Prototype]] 链接，关联到 Foo.prototype 指向的那个对象。

  - > new Foo() 会生成一个新对象（我们称之为 a），这个新对象的内部链接 [[Prototype]] 关联的是 Foo.prototype 对象。

  - > 最后我们得到了两个对象，它们之间互相关联，就是这样。我们并没有初始化一个类，实际上我们并没有从“类”中复制任何行为到一个对象中，只是让两个对象互相关联。

  - > 在 JavaScript 中，我们并不会将一个对象（“类”）复制到另一个对象（“实例”），只是将它们关联起来。

  - > 在我看来，在“继承”前面加上“原型”对于事实的曲解就好像一只手拿橘子一只手拿苹果然后把苹果叫作“红橘子”一样。无论添加什么标签都无法改变事实：一种水果是苹果，另一种是橘子。

  - > 因此我认为这个容易混淆的组合术语“原型继承”（以及使用其他面向类的术语比如“类”、“构造函数”、“实例”、“多态”，等等）严重影响了大家对于 JavaScript 机制真实原理的理解。

  - > 委托（参见第 6 章）这个术语可以更加准确地描述 JavaScript 中对象的关联机制。

  - > 除了令人迷惑的“构造函数”语义外，Foo.prototype 还有另一个绝招。思考下面的代码：function Foo() { // ... } Foo.prototype.constructor === Foo; // true var a = new Foo(); a.constructor === Foo; // true

  - > 实际上，.constructor 引用同样被委托给了 Foo.prototype，

  - > 这段代码的核心部分就是语句 Bar.prototype = Object.create( Foo.prototype )。调用Object.create(..) 会凭空创建一个“新”对象并把新对象内部的 [[Prototype]] 关联到你指定的对象（本例中是 Foo.prototype）。换句话说，这条语句的意思是：“创建一个新的 Bar.prototype 对象并把它关联到 Foo.prototype”。

- > 第 6 章行为委托

  - > 下面是推荐的代码形式，非常简单：Task = { setID: function(ID) { this.id = ID; }, outputID: function() { console.log( this.id ); } }; // 让 XYZ 委托 Task XYZ = Object.create( Task ); XYZ.prepareTask = function(ID,Label) { this.setID( ID ); this.label = Label; }; XYZ.outputTaskDetails = function() { this.outputID(); console.log( this.label ); }; // ABC = Object.create( Task ); // ABC ... = ...在 这 段 代 码 中，Task 和 XYZ 并 不 是 类（ 或 者 函 数 ）， 它 们 是 对 象。XYZ 通 过 Object.create(..) 创建，它的 [[Prototype]] 委托了 Task 对象（

  - > \1. 在上面的代码中，id 和 label 数据成员都是直接存储在 XYZ 上（而不是 Task）。通常来说，在 [[Prototype]] 委托中最好把状态保存在委托者（XYZ、ABC）而不是委托目标（Task）上。

  - > \2. 在类设计模式中，我们故意让父类（Task）和子类（XYZ）中都有 outputTask 方法，这样就可以利用重写（多态）的优势。在委托行为中则恰好相反：我们会尽量避免在[[Prototype]] 链的不同级别中使用相同的命名，否则就需要使用笨拙并且脆弱的语法来消除引用歧义

  - > \3. this.setID(ID)；XYZ 中的方法首先会寻找 XYZ 自身是否有 setID(..)，但是 XYZ 中并没有这个方法名，因此会通过 [[Prototype]] 委托关联到 Task 继续寻找，这时就可以找到setID(..) 方法。此外，由于调用位置触发了 this 的隐式绑定规则（参见第 2 章），因此虽然 setID(..) 方法在 Task 中，运行时 this 仍然会绑定到 XYZ，这正是我们想要的。在之后的代码中我们还会看到 this.outputID()，

  - > 在 API 接口的设计中，委托最好在内部实现，不要直接暴露出去。在之前的例子中我们并没有让开发者通过 API 直接调用 XYZ.setID()。（当然，可以这么做！）相反，我们把委托隐藏在了 API 的内部，XYZ.prepareTask(..) 会委托 Task.setID(..)。

  - > 下面我们看看两段代码对应的思维模型。首先，类风格代码的思维模型强调实体以及实体间的关系：
    > 172 ｜ 第 6 章实际上这张图有点不清晰 / 误导人，因为它还展示了许多技术角度不需要关注的细节（但是你必须理解它们）！从图中可以看出这是一张十分复杂的关系网。此外，如果你跟着图中的箭头走就会发现，JavaScript 机制有很强的内部连贯性。

    > 172 ｜ 第 6 章实际上这张图有点不清晰 / 误导人，因为它还展示了许多技术角度不需要关注的细节（但是你必须理解它们）！从图中可以看出这是一张十分复杂的关系网。此外，如果你跟着图中的箭头走就会发现，JavaScript 机制有很强的内部连贯性。

  - > 不过我们并不需要再纠结于这个问题，这里提到只是让你简单回忆一下；现在我们来看看ES6 的 class 机制。我们会介绍它的工作原理并分析 class 是否改进了之前提到的那些缺点。

  - > 平心而论，class 语法确实解决了典型原型风格代码中许多显而易见的（语法）问题和缺点。

