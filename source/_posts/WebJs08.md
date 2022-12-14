---
title: 前端基础进阶08 柯里化
category:
  - 前端基础进阶
tags:
  - JavaScript
date: 2017-08-12 10:01:41
---

柯里化（Currying），也被翻译为“局部套用”，是把多参数函数转换为一系列单参数函数并进行调用的技术。函数也是值，从而我们可以用有趣的方式去操作函数值。柯里化允许我们把函数与传递给它的参数相结合，产生出一个新的函数。
<!-- more -->
柯里化是函数的一个比较高级的应用，想要理解它并不简单。因此我一直在思考应该如何更加表达才能让大家理解起来更加容易。想了很久，决定先抛开柯里化这个概念不管，补充两个重要、但是容易被忽略的知识点。
### 1. 补充知识点

#### 1.1 函数的隐式转换

JavaScript作为一种 `弱类型语言` ，它的  `隐式转换` 是非常灵活有趣的。当我们没有深入了解隐式转换的时候可能会对一些运算的结果会感动困惑，比如4 + true = 5。当然，如果对隐式转换了解足够深刻，肯定是能够很大程度上提高对js的使用能力。只是我没有打算将所有的隐式转换规则分享给大家，这里暂时只分享一下，函数在隐式转换中的一些规则。

```javascript
function fn() {
    return 20;
}

console.log(fn + 10); // 输出结果是多少？
```

稍微修改一下，再想想输出结果会是什么？
```javascript
function fn() {
    return 20;
}

fn.toString = function() {
    return 10;
}

console.log(fn + 10);  // 输出结果是多少
```

还可以继续修改一下。
```javascript
function fn() {
    return 20;
}

fn.toString = function() {
    return 10;
}

fn.valueOf = function() {
    return 5;
}

console.log(fn + 10); // 输出结果是多少？
```

运行结果
```javascript
// 输出结果分别为
function fn() {
    return 20;
}10

20

15
```

**当使用console.log，或者进行运算时，隐式转换就可能会发生。**从上面三个例子中我们可以得出一些关于函数隐式转换的结论。
>当我们没有重新定义toString与valueOf时，函数的隐式转换会调用默认的toString方法，它会将函数的定义内容作为字符串返回。而当我们主动定义了toString/vauleOf方法时，那么隐式转换的返回结果则由我们自己控制了。`其中valueOf会比toString后执行。`

#### 1.2 利用call/apply封数组的map方法

>map(): 对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。

通俗来说，就是遍历数组的每一项元素，并且在map的第一个参数（回调函数）中进行运算处理后返回计算结果。返回一个由所有计算结果组成的新数组。

```javascript
// 回调函数中有三个参数
// 第一个参数表示newArr的每一项，第二个参数表示该项在数组中的索引值
// 第三个表示数组本身
// 除此之外，回调函数中的this，当map不存在第二参数时，this指向丢失，当存在第二个参数时，指向改参数所设定的对象
var newArr = [1, 2, 3, 4].map(function(item, i, arr) {
    //console.log(item, i, arr, this);  // 可运行试试看
    return item + 1;  // 每一项加1
}, { a: 1 })

console.log(newArr); // [2, 3, 4, 5]
```

在上面例子的注释中详细阐述了map方法的细节。现在要面临一个难题，就是如何封装map。

可以先想想for循环。我们可以使用for循环来实现一个map，但是在封装的时候，我们会考虑一些问题。我们在使用for循环的时候，一个循环过程确实很好封装，但是我们在for循环里面要对每一项做的事情却很难用一个固定的东西去把它封装起来。因为每一个场景，for循环里对数据的处理肯定都是不一样的。

于是大家就想了一个很好的办法，将这些不一样的操作单独用一个函数来处理，让这个函数成为map方法的第一个参数，具体这个回调函数中会是什么样的操作，则由我们自己在使用时决定。因此，根据这个思路的封装实现如下。
```javascript
Array.prototype._map = function(fn, context) {
    var temp = [];
    if(typeof fn == 'function') {
        var k = 0;
        var len = this.length;
        // 封装for循环过程
        for(; k < len; k++) {
            // 将每一项的运算操作丢进fn里，利用call方法指定fn的this指向与具体参数
            temp.push(fn.call(context, this[k], k, this))
        }
    } else {
        console.error('TypeError: '+ fn +' is not a function.');
    }

    // 返回每一项运算结果组成的新数组
    return temp;
}

var newArr = [1, 2, 3, 4]._map(function(item) {
    return item + 1;
})
// [2, 3, 4, 5]
```

>在理解了map的封装过程之后，我们就能够明白为什么我们在使用map时，总是期望能够在第一个回调函数中有一个返回值了。在eslint的规则中，如果我们在使用map时没有设置一个返回值，就会被判定为错误。

明白了函数的隐式转换规则与call/apply在这种场景的使用方式，我们就可以尝试通过简单的例子来了解一下柯里化了。

### 3. 柯里化

在前端面试中有一个关于柯里化的面试题，流传甚广。
>实现一个add方法，使计算结果能够满足如下预期：
add(1)(2)(3) = 6
add(1, 2, 3)(4) = 10
add(1)(2)(3)(4)(5) = 15

很明显，计算结果正是所有参数的和，add方法每运行一次，肯定返回了一个同样的函数，继续计算剩下的参数。

当我们只调用两次时，可以这样封装：
```javascript
function add(a) {
    return function(b) {
        return a + b;
    }
}

console.log(add(1)(2));  // 3
```

如果只调用三次：
```javascript
function add(a) {
    return function(b) {
        return function (c) {
            return a + b + c;
        }
    }
}

console.log(add(1)(2)(3)); // 6
```

上面的封装看上去跟我们想要的结果有点类似，但是参数的使用被限制得很死，因此并不是我们想要的最终结果，我们需要通用的封装。应该怎么办？总结一下上面2个例子，其实我们是利用闭包的特性，将所有的参数，集中到最后返回的函数里进行计算并返回结果。因此我们在封装时，主要的目的，就是将参数集中起来计算。

```javascript
function add() {
    // 第一次执行时，定义一个数组专门用来存储所有的参数
    var _args = [].slice.call(arguments);

    // 在内部声明一个函数，利用闭包的特性保存_args并收集所有的参数值
    var adder = function () {
        var _adder = function() {
            [].push.apply(_args, [].slice.call(arguments));
            return _adder;
        };

        // 利用隐式转换的特性，当最后执行时隐式转换，并计算最终的值返回
        _adder.toString = function () {
            return _args.reduce(function (a, b) {
                return a + b;
            });
        }

        return _adder;
    }
    return adder.apply(null, [].slice.call(arguments));
}

// 输出结果，可自由组合的参数
console.log(add(1, 2, 3, 4, 5));  // 15
console.log(add(1, 2, 3, 4)(5));  // 15
console.log(add(1)(2)(3)(4)(5));  // 15
```

上面的实现，利用闭包的特性，主要目的是想通过一些巧妙的方法将所有的参数收集在一个数组里，并在最终隐式转换时将数组里的所有项加起来。因此我们在调用add方法的时候，参数就显得非常灵活。当然，也就很轻松的满足了我们的需求。

那么读懂了上面的demo，然后我们再来看看柯里化的特点，相信大家就会更加容易理解了。
>柯里化（英语：Currying），又称为部分求值，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回一个新的函数的技术，新函数接受余下参数并返回运算结果。


* 接收单一参数，因为要携带不少信息，因此常常以回调函数的理由来解决。
* 将部分参数通过回调函数等方式传入函数中
* 返回一个新函数，用于处理所有的想要传入的参数

在上面的例子中，我们可以将add(1, 2, 3, 4)转换为add(1)(2)(3)(4)。这就是部分求值。每次传入的参数都只是我们想要传入的所有参数中的一部分。当然实际应用中，并不会常常这么复杂的去处理参数，很多时候也仅仅只是分成两部分而已。

咱们再来一起思考一个与柯里化相关的问题。
>假如有一个计算要求，需要我们将数组里面的每一项用我们自己想要的字符给连起来。我们应该怎么做？想到使用join方法，就很简单。

```javascript
var arr = [1, 2, 3, 4, 5];

// 实际开发中并不建议直接给Array扩展新的方法
// 只是用这种方式演示能够更加清晰一点
Array.prototype.merge = function(chars) {
    return this.join(chars);
}

var string = arr.merge('-')

console.log(string);  // 1-2-3-4-5
```

>增加难度，将每一项加一个数后再连起来。那么这里就需要map来帮助我们对每一项进行特殊的运算处理，生成新的数组然后用字符连接起来了。实现如下：

```javascript
var arr = [1, 2, 3, 4, 5];

Array.prototype.merge = function(chars, number) {
    return this.map(function(item) {
        return item + number;
    }).join(chars);
}

var string = arr.merge('-', 1);

console.log(string); // 2-3-4-5-6
```

同理，但是如果我们又想要让数组每一项都减去一个数之后再连起来呢？
```javascript
var arr = [1, 2, 3, 4, 5];

Array.prototype.merge = function(chars, number) {
    return this.map(function(item) {
        return item - number;
    }).join(chars);
}

var string = arr.merge('-', 1);

console.log(string); // 0-1-2-3-4
```

机智的小伙伴肯定发现困惑所在了。我们期望封装一个函数，能同时处理不同的运算过程，但是我们并不能使用一个固定的套路将对每一项的操作都封装起来。于是问题就变成了和封装map的时候所面临的问题一样了。我们可以借助柯里化来搞定。

与map封装同样的道理，既然我们事先并不确定我们将要对每一项数据进行怎么样的处理，我只是知道我们需要将他们处理之后然后用字符连起来，所以不妨将处理内容保存在一个函数里。而仅仅固定封装连起来的这一部分需求。

```javascript
// 封装很简单，一句话搞定
Array.prototype.merge = function(fn, chars) {
    return this.map(fn).join(chars);
}

var arr = [1, 2, 3, 4];

// 难点在于，在实际使用的时候，操作怎么来定义，利用闭包保存于传递num参数
var add = function(num) {
    return function(item) {
        return item + num;
    }
}

var red = function(num) {
    return function(item) {
        return item - num;
    }
}

// 每一项加2后合并
var res1 = arr.merge(add(2), '-');

// 每一项减1后合并
var res2 = arr.merge(red(1), '-');

// 也可以直接使用回调函数，每一项乘2后合并
var res3 = arr.merge((function(num) {
    return function(item) {
        return item * num
    }
})(2), '-')

console.log(res1); // 3-4-5-6
console.log(res2); // 0-1-2-3
console.log(res3); // 2-4-6-8
```

### 4. 柯里化通用式

通用的柯里化写法其实比我们上边封装的add方法要简单许多:
```javascript

var currying = function(fn) {
    var args = [].slice.call(arguments, 1);

    return function() {
        // 主要还是收集所有需要的参数到一个数组中，便于统一计算
        var _args = args.concat([].slice.call(arguments));
        return fn.apply(null, _args);
    }
}

var sum = currying(function() {
    var args = [].slice.call(arguments);
    return args.reduce(function(a, b) {
        return a + b;
    })
}, 10)

console.log(sum(20, 10));  // 40
console.log(sum(10, 5));   // 25

```

### 5. 柯里化与bind

```javascript

Object.prototype.bind = function(context) {
    var _this = this;
    var args = [].slice.call(arguments, 1);

    return function() {
        return _this.apply(context, args)
    }
}

```

这个例子利用call与apply的灵活运用，实现了bind的功能。在前面的几个例子中，我们可以总结一下柯里化的特点：

* 接收单一参数，将更多的参数通过回调函数来搞定;
* 返回一个新函数，用于处理所有的想要传入的参数;
* 需要利用call/apply与arguments对象收集参数；
* 返回的这个函数正是用来处理收集起来的参数。


参考來源：简书 过去式丶 http://www.jianshu.com/p/5e1899fe7d6b
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
