---
title: 前端基础进阶09 记忆
category:
  - 前端基础进阶
tags:
  - JavaScript
date: 2017-08-12 10:17:06
---

函数可以将先前操作的结果记录在某个对象中，从而避免无谓的重复计算。这种优化被称之为 `记忆(Memorization)` 。JavaScript的数组和对象要实现这种优化是很方便的。
<!-- more -->
>在计算机领域，记忆主要用于加速程序计算的一种优化技术，它使得函数避免重复验算之前已被处理的输入，而返回已经缓存的结果。

比如说，我们用 递归 的方式来计算 `斐波那契（fibonacci）`数列。这种数列的特点是，前面相邻的两项之和等于后一项的值，最前面的两项是 0 和 1。

```javascript
var fibonacci = function (n) {
    return n < 2 ? n : fibonacci (n-1) + fibonacci (n-2);
};
for( var i=0; i <= 10; i++) {
    console.log('//' + i + ':' + fibonacci (i));
}
//0:0
//1:1
//2:1
//3:2
//4:3
//5:5
//6:8
//7:13
//8:21
//9:34
//10:55
```
这样是正确运行的，但是却做了很多无谓的工作，fibonacci 函数调用自身很多次去计算可能已经被计算过的值。如果增加了记忆功能，就可以显著减少运算量。

我们在一个名为 memo 的数组中存放我们的存储结果，存储结果是可以隐藏在 闭包 中。当函数被调用时，这个函数首先检查运算结果是否已经存在，如果已经存在，就立即返回这个结果。

```javascript
var fibonacci = function () {
    var memo = [0,1];
    var fib = function (n) {
        var result = memo[n];
        if (typeof result !== 'number') {
            result = fib (n-1) + fib (n-2);
            memo[n] = result;
        }
        return result;
    };
    return fib;
}();
for( var i=0; i <= 10; i++) {
    console.log('//' + i + ':' + fibonacci (i));
}
```
这个函数返回了同样的结果，但调用自身的次数大为减少，仅仅是为了去取得存储。

我们可以将这种技术推而广之，编写一个函数来帮助我们来构造带记忆功能的函数。如下：memoizer 函数取得一个初始的 memo 数组和 formula 函数。它返回一个管理 memo 存储和在需要时间调用 formula 函数的 recur 函数。我们把这个 recur 函数和它的参数传递给 formula 函数。
```javascript
var memoizer = function (memo,formula) {
    var recur = function (n) {
        var result = memo[n];
        if (typeof result !== 'number') {
            result = formula (recur,n);
            memo[n] = result;
        }
        return result;
    };
    return recur;
}
```
现在，我们可以使用 memoizer 函数来定义 fibonacci 函数，提供初始的 memo数组和 formula函数：
```javascript
var fibonacci = memoizer ([0,1], function (recur,n) {
    return recur (n-1) + recur (n-2);
});
//来调用
for( var i=0; i <= 10; i++) {
    console.log('//' + i + ':' + fibonacci (i));
}
```

通过设计这种产生另一个函数的函数，极大的减少了工作量。例如，要产生一个可记忆的阶乘函数，我们只需要提供基本的阶乘公式即可：
```javascript
var factorial = memoizer([1,1],function (recur,n) {
    return n * recur (n-1);
})
```
完整代码如下：
```javascript
var memoizer = function (memo,formula) {
    var recur = function (n) {
        var result = memo[n];
        if (typeof result !== 'number') {
            result = formula (recur,n);
            memo[n] = result;
        }
        return result;
    };
    return recur;
};
var factorial = memoizer([1,1],function (recur,n) {
    return n * recur (n-1);
});
for( var i=0; i <= 10; i++) {
    console.log('//' + i + ':' + factorial (i));
}
```

参考书籍：JavaScript语言精粹（修订版）第四章
