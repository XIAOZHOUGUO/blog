---
title: 前端基础进阶07 函数与函数式编程
category:
  - 前端基础进阶
tags:
  - JavaScript
date: 2017-08-10 17:06:24
---

纵观JavaScript中所有必须需要掌握的重点知识中，函数是我们在初学的时候最容易忽视的一个知识点。在学习的过程中，可能会有很多人、很多文章告诉你面向对象很重要，原型很重要，可是却很少有人告诉你，面向对象中所有的重点难点，几乎都与函数息息相关。所以大家一定要重视函数。
<!-- more -->

### 1.函数声明、函数表达式、匿名函数、函数自执行
#### 1.1 函数声明

我们知道，JavaScript中，有两种声明方式，一个是使用var的变量声明，另一个是使用function的函数声明。

在 **变量对象详解** 中我有提到过，变量对象的创建过程中，**函数声明比变量声明具有更为优先的执行顺序，即我们常常提到的函数声明提前。**  因此我们在执行上下文中，无论在什么位置声明了函数，我们都可以在同一个执行上下文中直接使用该函数。
```javascript
fn(); //function

function fn(){
    console.log('function');
}
```

#### 1.2 函数表达式

与函数声明不同，函数表达式使用了var进行声明，那么我们在确认他是否可以正确使用的时候就必须依照var的规则进行判断，即变量声明。我们知道使用var进行变量声明，其实是进行了两步操作。
```javascript
// 变量声明
var a = 20;

// 实际执行顺序
var a = undefined;  // 变量声明，初始值undefined，变量提升，提升顺序次于function声明
a = 20;  // 变量赋值，该操作不会提升
```

同样的道理，**当我们使用变量声明的方式来声明函数时，就是我们常常说的函数表达式**。函数表达的提升方式与变量声明一致。
```javascript
fn(); // 报错
var fn = function() {
    console.log('function');
}
```
上例子的执行顺序为：
```javascript
var fn = undefined;   // 变量声明提升
fn();    // 执行报错
fn = function() {   // 赋值操作，此时将后边函数的引用赋值给fn
    console.log('function');
}
```

>因此，由于声明方式的不同，导致了函数声明与函数表达式在使用上的一些差异需要我们注意，除此之外，这两种形式的函数在使用上并无不同。

关于上面例子中，函数表达式中的赋值操作，在其他一些地方也会被经常使用，我们清楚其中的关系即可。
```javascript
在构造函数中添加方法
function Person(name) {
    this.name = name;
    this.age = age;
    // 在构造函数内部中添加方法
    this.getAge = function() {
        return this.age;
    }

}
// 给原型添加方法
Person.prototype.getName = function() {
    return this.name;
}

// 在对象中添加方法
var a = {
    m: 20,
    getM: function() {
        return this.m;
    }
}
```

#### 1.3 匿名函数

在上面我们大概讲述了函数表达式中的赋值操作。而 `匿名函数`，顾名思义，就是指的没有被显式进行赋值操作的函数。它的使用场景，多作为一个参数传入另一个函数中。

```javascript
var a = 10;
var fn = function(bar, num) {
    return bar() + num;
}

fn(function() {
    return a;
}, 20) //30
```
在上面的例子中，fn的第一个参数传入了一个匿名函数。虽然该匿名函数没有显示的进行赋值操作，我们没有办法在外部执行上下文中引用到它，但是在fn函数内部，我们将该匿名函数赋值给了变量bar，保存在了fn变量对象的 `arguments对象` 中。

```javascript
// 变量对象在fn上下文执行过程中的创建阶段
VO(fn) = {
    arguments: {
        bar: undefined,
        num: undefined,
        length: 2
    }
}

// 变量对象在fn上下文执行过程中的执行阶段
// 变量对象变为活动对象，并完成赋值操作与执行可执行代码
VO -> AO

AO(fn) = {
    arguments: {
        bar: function() { return a },
        num: 20,
        length: 2
    }
}
```

由于匿名函数传入另一个函数之后，最终会在另一个函数中执行，因此我们也常常称这个匿名函数为`回调函数`。关于匿名函数更多的内容，我会在下一篇深入探讨柯里化的文章中进行更加详细讲解。

**匿名函数的这个应用场景几乎承担了函数的所有难以理解的知识点，**因此我们一定要对它的这些细节了解的足够清楚，如果对于变量对象的演变过程你还看不太明白，一定要回过头去看这篇文章：[前端基础进阶03：变量对象详解](https://xiaozhouguo.github.io/2017/08/08/WebJs03/)

#### 1.4 函数自执行和块级作用域

在ES5中，没有块级作用域，因此我们常常使用函数自执行的方式来模仿块级作用域，这样就提供了一个独立的执行上下文，结合闭包，就为模块化提供了基础。
```javascript
(function (){
    // ......
})();
```

**一个模块往往可以包括：私有变量、私有方法、公有变量、公有方法。**

根据作用域链的单向访问，外面可能很容易知道在这个独立的模块中，外部执行环境是无法访问内部的任何变量与方法的，因此我们可以很容易的创建属于这个模块的私有变量与私有方法。
```javascript
(function() {
    // 私有变量
    var age = 20;
    var name = 'Tom';

    // 私有方法
    function getName() {
        return `your name is ` + name;
    }
})();
```

但是共有方法和变量应该怎么办？大家还记得我们前面讲到过的闭包的特性吗？没错，利用闭包，我们可以访问到执行上下文内部的变量和方法，因此，我们只需要根据闭包的定义，创建一个闭包，将你认为需要公开的变量和方法开放出来即可。
>如果你对闭包了解不够，[前端基础进阶04 详细图解作用域链与闭包](https://xiaozhouguo.github.io/2017/08/08/WebJs04/) 应该可以帮到你。

```javascript
(function() {
    // 私有变量
    var age = 20;
    var name = 'Tom';

    // 私有方法
    function getName() {
        return `your name is ` + name;
    }

    // 共有方法
    function getAge() {
        return age;
    }

    // 将引用保存在外部执行环境的变量中，形成闭包，防止该执行环境被垃圾回收
    window.getAge = getAge;
})();
```

当然，闭包在模块中的重要作用，我们也在讲解闭包的时候已经强调过，但是这个知识点真的太重要，需要我们反复理解并且彻底掌握，因此为了帮助大家进一步理解闭包，我们来看看jQuery中，是如何利用我们模块与闭包的。
```javascript
// 使用函数自执行的方式创建模块
(function(window, undefined) {

    // 声明jQuery构造函数
     var jQuery = function(name) {

        // 主动在构造函数中，返回一个jQuery实例
         return new jQuery.fn.init(name);
     }

    // 添加原型方法
     jQuery.prototype = jQuery.fn = {
         constructor: jQuery,
         init:function() { ... },
         css: function() { ... }
     }
     jQuery.fn.init.prototype = jQuery.fn;

    // 将jQuery改名为$，并将引用保存在window上，形成闭包，对外开发jQuery构造函数，这样我们就可以访问所有挂载在jQuery原型上的方法了
     window.jQuery = window.$ = jQuery;
 })(window);

// 在使用时，我们直接执行了构造函数，因为在jQuery的构造函数中通过一些手段，返回的是jQuery的实例，所以我们就不用再每次用的时候在自己new了
$('#div1');
```
在这里，我们只需要看懂闭包与模块的部分就行了，至于内部的原型链是如何绕的，为什么会这样写，我在讲面向对象的时候会为大家慢慢分析。举这个例子的目的所在，就是希望大家能够重视函数，因为在实际开发中，它无处不在。

接下来我要分享一个高级的，非常有用的模块的应用。当我们的项目越来越大，那么需要保存的数据与状态就越来越多，因此，我们需要一个专门的模块来维护这些数据，这个时候，有一个叫做 `状态管理器` 的东西就应运而生。对于状态管理器，最出名的话，我想非 `redux` 莫属了。虽然对于还在学习中的大家来说，redux是一个有点高深莫测的东西，但是在我们学习之前，可以先通过简单的方式，让大家大致了解状态管理器的实现原理，为我们未来的学习奠定坚实的基础。
```javascript
// 自执行创建模块
(function() {
    // states 结构预览
    // states = {
    //     a: 1,
    //     b: 2,
    //     m: 30,
    //     o: {}
    // }
    var states = {};  // 私有变量，用来存储状态与数据
    // 判断数据类型
    function type(elem) {
        if(elem == null) {
            return elem + '';
        }
        return toString.call(elem).replace(/[\[\]]/g, '').split(' ')[1].toLowerCase();
    }
    /**
     * @Param name 属性名
     * @Description 通过属性名获取保存在states中的值
    */
    function get(name) {
        return states[name] ? states[name] : '';
    }
    function getStates() {
        return states;
    }
    /*
    * @param options {object} 键值对
    * @param target {object} 属性值为对象的属性，只在函数实现时递归中传入
    * @desc 通过传入键值对的方式修改state树，使用方式与小程序的data或者react中的setStates类似
    */
    function set(options, target) {
        var keys = Object.keys(options);
        var o = target ? target : states;
        keys.map(function(item) {
            if(typeof o[item] == 'undefined') {
                o[item] = options[item];
            }
            else {
                type(o[item]) == 'object' ? set(options[item], o[item]) : o[item] = options[item];
            }
            return item;
        })
    }
    // 对外提供接口
    window.get = get;
    window.set = set;
    window.getStates = getStates;
})()

// 具体使用如下
set({ a: 20 });     // 保存 属性a
set({ b: 100 });    // 保存属性b
set({ c: 10 });     // 保存属性c

// 保存属性o, 它的值为一个对象
set({
    o: {
        m: 10,
        n: 20
    }
})
// 修改对象o 的m值
set({
    o: {
        m: 1000
    }
})
// 给对象o中增加一个c属性
set({
    o: {
        c: 100
    }
})
console.log(getStates())
```
>[项目预览](https://codepen.io/yangbo5207/pen/EZzEbY?editors=1111)

我之所以说这是一个高级应用，是因为在单页应用中，我们很可能会用到这样的思路。根据我们提到过的知识，理解这个例子其实很简单，其中的难点估计就在于set方法的处理上，因为为了具有更多的适用性，因此做了很多适配，用到了递归等知识。如果你暂时看不懂，没有关系，知道如何使用就行了，上面的代码可以直接运用于实际开发。记住，当你需要保存的状态太多的时候，你就想到这一段代码就行了。

>函数自执行的方式另外还有其他几种写法，诸如!function(){}()，+function(){}()

### 2. 函数参数传递方式：按值传递

还记得基本数据类型与引用数据类型在复制上的差异吗？基本数据类型复制，是直接值发生了复制，因此改变后，各自相互不影响。但是引用数据类型的复制，是保存在变量对象中的引用发生了复制，因此复制之后的这两个引用实际访问的实际是同一个堆内存中的值。当改变其中一个时，另外一个自然也被改变。如下例。
```javascript
var a = 20;
var b = a;
b = 10;
console.log(a);  // 20

var m = { a: 1, b: 2 }
var n = m;
n.a = 5;
console.log(m.a) // 5
```

当值作为函数的参数传递进入函数内部时，也有同样的差异。我们知道，函数的参数在进入函数后，实际是被保存在了函数的变量对象中，因此，这个时候相当于发生了一次复制。如下例。
```javascript
var a = 20;

function fn(a) {
    a = a + 10;
    return a;
}

console.log(a); // 20
```

```javascript
var a = { m: 10, n: 20 }
function fn(a) {
    a.m = 20;
    return a;
}

fn(a);
console.log(a);   // { m: 20, n: 20 }
```

正是由于这样的不同，导致了许多人在理解函数参数的传递方式时，就有许多困惑。到底是按值传递还是按引用传递？实际上结论仍然是按值传递，只不过当我们期望传递一个引用类型时，真正传递的，只是这个引用类型保存在变量对象中的引用而已。为了说明这个问题，我们看看下面这个例子。
```javascript
var person = {
    name: 'Nicholas',
    age: 20
}

function setName(obj) {  // 传入一个引用
    obj = {};   // 将传入的引用指向另外的值
    obj.name = 'Greg';  // 修改引用的name属性
}

setName(person);
console.log(person.name);  // Nicholas 未被改变
```
在上面的例子中，如果person是按引用传递，那么person就会自动被修改为指向其name属性值为Gerg的新对象。但是我们从结果中看到，person对象并未发生任何改变，因此只是在函数内部引用被修改而已。

### 3. 函数式编程

虽然JavaScript并不是一门纯函数式编程的语言，但是它使用了许多函数式编程的特性。因此了解这些特性可以让我们更加了解自己写的代码。

#### 3.1 函数是第一等公民

所谓"第一等公民"（first class），指的是函数与其他数据类型一样，处于平等地位，可以赋值给其他变量，也可以作为参数，传入另一个函数，或者作为别的函数的返回值。这些场景，我们应该见过很多。
```javascript
var a = function foo() {}  // 赋值
function fn(function() {}, num) {}   // 函数作为参数

// 函数作为返回值
function var() {
    return function() {
        ... ...
    }
}
```

#### 3.2 只用"表达式"，不用"语句"

"表达式"（expression）是一个单纯的运算过程，总是有返回值；"语句"（statement）是执行某种操作，没有返回值。函数式编程要求，只使用表达式，不使用语句。也就是说，每一步都是单纯的运算，而且都有返回值。

了解这一点，可以让我们自己在封装函数的时候养成良好的习惯。借助这个特性，我们在学习其他API的时候，了解函数的返回值也是一个十分重要的习惯。

#### 3.3 没有副作用

所谓"副作用"（side effect），指的是函数内部与外部互动（最典型的情况，就是修改全局变量的值），产生运算以外的其他结果。

函数式编程强调没有"副作用"，意味着函数要保持独立，所有功能就是返回一个新的值，没有其他行为，尤其是不得修改外部变量的值。

即所谓的只要是同样的参数传入，返回的结果一定是相等的。

#### 3.4 闭包

闭包是函数式编程语言的重要特性，我也在前面几篇文章中说了很多关于闭包的内容。这里不再赘述。

#### 3.5 柯里化

理解柯里化稍微有点难，我在下一篇文章里专门单独来深入分析。

### 4. 函数封装

在我们自己封装函数时，最好尽量根据函数式编程的特点来编写。当然在许多情况下并不能完全做到，比如函数中我们常常会利用模块中的私有变量等。

#### 4.1 普通封装
```javascript
function add(num1, num2) {
    return num1 + num2;
}

add(20, 10); // 30
```

#### 4.2 挂载在对象上
```javascript
if(typeof Array.prototype.add !== 'function') {
  Array.prototype.add = function() {
    var i = 0,
        len = this.length,
        result = 0;

    for( ; i < len; i++) {
        result += this[i]
    }
    return result;
  }
}

[1, 2, 3, 4].add() // 10
```

修改数组对象的例子，常在面试中被问到类似的，但是并不建议在实际开发中扩展原生对象。与普通封装不一样的是，因为挂载在对象的原型上我们可以通过this来访问对象的属性和方法，所以这种封装在实际使用时会有许多的难点，因此我们一定要掌握好this。

参考來源：简书 过去式丶 http://www.jianshu.com/p/69dede6f7e5f
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
