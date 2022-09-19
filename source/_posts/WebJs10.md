---
title: 前端基础进阶10 详解面向对象、构造函数、原型与原型链
category:
  - 前端基础进阶
tags:
  - JavaScript
date: 2017-08-13 17:09:01
---

面向对象可以说是JavaScript中很重要的一个部分，也是很难理解的重点，我们先来看一看关于面向对象的一些基本功。
<!-- more -->

### 1. 对象的定义

ECMA-262把对象定义为：“**无序属性的集合，其属性可以包含基本值、对象或者函数。**”严格来讲， 这就相当于说对象是一组没有特定顺序的值。对象的每个属性或方法都有一个名字，而每个名字都映射 到一个值。正因为这样（以及其他将要讨论的原因），我们可以把ECMAScript的对象想象成散列表：无非就是一组名值对，其中值可以是数据或函数。

也就是说，在JavaScript中，对象无非就是由一些列无序的key-value对组成。其中value可以是基本值，对象或者函数。
```javascript
// 这里的person就是一个对象
var person = {
    name: 'Tom',
    age: 18,
    getName: function() {},
    parent: {}
}
```

#### 1.1 创建对象

创建对象的方式有很多种，我们可以通过new的方式创建一个对象。
>var obj = new Object();

也可以通过对象字面量的方式来创建一个对象。
>var obj = {};

当我们想要给我们创建的简单对象添加方法时，可以这样表示。
```javascript
// 可以这样
var person = {};
person.name = "TOM";
person.getName = function() {
    return this.name;
}
// 也可以这样
var person = {
    name: "TOM",
    getName: function() {
        return this.name;
    }
}
```

#### 1.2 访问对象的属性和方法

假如我们有一个简单的对象如下：
```javascript
var person = {
    name: 'TOM',
    age: '20',
    getName: function() {
        return this.name
    }
}
```
当我们想要访问他的name属性时，可以用如下两种方式访问：
>person.name 或者 person['name']

如果我们想要访问的属性名是一个变量时，常常会使用第二种方式。例如我们要同时访问person的name与age，可以这样写：
```javascript
['name', 'age'].forEach(function(item) {
    console.log(person[item]);
})
```
>这种方式一定要重视，记住它以后在我们处理复杂数据的时候会有很大的帮助。

### 2. 工厂模式

虽然 Object 构造函数或对象字面量都可以用来创建单个对象，但这些方式有个明显的缺点：使用同一个接口创建很多对象，会产生大量的重复代码。
```javascript
var perTom = {
    name: 'TOM',
    age: 20,
    getName: function() {
        return this.name
    }
};
var perJake = {
    name: 'Jake',
    age: 22,
    getName: function() {
        return this.name
    }
}
```

为解决这个问题，人们开始使用工厂模式的一种变体。顾名思义，`工厂模式` 就是我们提供一个模子，然后通过这个模子复制出我们需要的对象。我们需要多少个，就复制多少个。考虑到在 ECMAScript 中无法创建类，开发人员就发明了一种函数，用函数来封装以特定接口创建对象的细节,如下面的例子所示。
```javascript
var createPerson = function(name, age) {
    // 声明一个中间对象，该对象就是工厂模式的模子
    var o = new Object();
    // 依次添加我们需要的属性与方法
    o.name = name;
    o.age = age;
    o.getName = function() {
        return this.name;
    }
    return o;
}
// 创建两个实例
var perTom = createPerson('TOM', 20);
var PerJake = createPerson('Jake', 22);
```

工厂模式虽然解决了创建多个相似对象的问题，但却没有解决对象识别的问题（即怎样知道一个对象的类型）。使用 `instanceof` 可以识别对象的类型，如下例子：
```javascript
var obj = {};
var foo = function() {};
console.log(obj instanceof Object);  // true
console.log(foo instanceof Function); // true
```

随着 JavaScript 的发展，又一个新模式出现了。在工厂模式的基础上，我们需要使用构造函数的方式来解决这个麻烦。

### 3. 构造函数模式

为了能够判断实例与对象的关系,可以使用构造函数模式将前面的例子重写如下：
```javascript
var Person = function(name, age) {
    this.name = name;
    this.age = age;
    this.getName = function() {
        return this.name;
    }
}
var p1 = new Person('Ness', 20);
console.log(p1.getName());  // Ness
console.log(p1 instanceof Person); // true
console.log(p1 instanceof Object); // true
```
在这个例子中，Person()函数取代了 createPerson()函数。我们注意到，Person()中的代码 除了与 createPerson()中相同的部分外，还存在以下不同之处：

* 没有显式地创建对象
* 直接将属性和方法赋给了 this 对象
* 没有 return 语句

>此外，还应该注意到函数名 Person 使用的是大写字母 P。按照惯例，构造函数始终都应该以一个大写字母开头，而非构造函数则应该以一个小写字母开头。

new关键字让构造函数具有了与普通函数不同的许多特点，而new的过程中，执行了如下过程：

* 声明一个中间对象
* 将该中间对象的原型指向构造函数的原型
* 将构造函数的this，指向该中间对象
* 返回该中间对象，即返回实例对象


#### 3.1 将构造函数当做函数

构造函数与其他函数的唯一区别，就在于调用它们的方式不同。
```javascript
// 当作构造函数使用
var person = new Person("Nicholas", 29, "Software Engineer");
person.getName(); //"Nicholas"
// 作为普通函数调用
Person("Greg", 27, "Doctor"); // 添加到 window
window.getName(); //"Greg"
// 在另一个对象的作用域中调用
var o = new Object();
Person.call(o, "Kristen", 25, "Nurse");
o.getName(); //"Kristen"
```
这个例子中的前两行代码展示了构造函数的典型用法，即使用new操作符来创建一个新对象。接下来的两行代码展示了不使用new操作符调用Person()会出现什么结果：属性和方法都被添加给window对象了。有读者可能还记得，当在全局作用域中调用一个函数时，this对象总是指向Global对象（在浏览器中就是window对象）。因此，在调用完函数之后，可以通过window对象来调用sayName()方法，并且还返回了"Greg"。最后，也可以使用call()或者apply()在某个特殊对象的作用域中调用Person函数，这里是在对象o的作用域中调用的，因此调用后o就拥有了所有属性和sayName()方法。

#### 3.2 构造函数的问题

构造函数模式虽然好用，但也并非没有缺点。使用构造函数的主要问题，就是每个方法都要在每个 实例上重新创建一遍。在前面的例子中，person1 和 person2 都有一个名为 getName()的方法，但那 两个方法不是同一个 Function 的实例。不要忘了——ECMAScript 中的函数是对象，因此每定义一个函数，也就是实例化了一个对象。
>不同实例上的同名函数是不相等的
console.log(p1.getName === p2.getName); //false

要解决这个问题，通过把函数定义转移到构造函数外部来解决这个问题：
```javascript
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.getName = getName;
}
function getName(){
    console.log(this.name);
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```
在这个例子中，我们把 sayName()函数的定义转移到了构造函数外部。而在构造函数内部，我们将 sayName 属性设置成等于全局的 sayName 函数。这样一来，由于 sayName 包含的是一个指向函数 的指针，因此 person1 和 person2 对象就共享了在全局作用域中定义的同一个 sayName()函数。这 样做确实解决了两个函数做同一件事的问题，可是新问题又来了：在全局作用域中定义的函数实际上只 能被某个对象调用，这让全局作用域有点名不副实。而更让人无法接受的是：如果对象需要定义很多方 法，那么就要定义很多个全局函数，于是我们这个自定义的引用类型就丝毫没有封装性可言了。好在，这些问题可以通过使用原型模式来解决。

### 4. 原型

我们创建的每一个函数，都可以有一个 `prototype（原型）` 属性，该属性指向一个对象。这个对象，就是我们这里说的原型。使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法。换句话说，不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中。

当我们在创建对象时，可以根据自己的需求，选择性的将一些属性和方法通过prototype属性，挂载在原型对象上。而 **每一个new出来的实例，都有一个 `__proto__ `属性，该属性指向构造函数的原型对象，通过这个属性，让实例对象也能够访问原型对象上的方法。**因此，当所有的实例都能够通过__proto__访问到原型对象时，原型对象的方法与属性就变成了共有方法与属性，它们可以被所有的实例对象访问。

```javascript
// 声明构造函数
function Person(name, age) {
    this.name = name;
    this.age = age;
}
// 通过prototye属性，将方法挂载到原型对象上
Person.prototype.getName = function() {
    return this.name;
}
var p1 = new Person('tim', 10);
var p2 = new Person('jak', 22);
console.log(p1.getName === p2.getName); // true
```
![图示](http://upload-images.jianshu.io/upload_images/599584-2fc7dad23d112791.png?imageMogr2/auto-orient/strip%7CimageView2/2)
通过图示我们可以看出，**构造函数的prototype与所有实例对象的__proto__都指向原型对象。而原型对象的 `constructor` 指向构造函数。**

**当我们访问实例对象中的属性或者方法时，会优先访问实例对象自身的属性和方法。**
```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.getName = function() {
        console.log('this is constructor.');
    }
}
Person.prototype.getName = function() {
    return this.name;
}
var p1 = new Person('tim', 10);
p1.getName(); // this is constructor.
```
在这个例子中，我们同时在原型与构造函数中都声明了一个getName函数，运行代码的结果表示原型中的访问并没有被访问。

我们还可以通过 `in` 来判断，一个对象是否拥有某一个属性/方法，无论是该属性/方法存在与实例对象还是原型对象。另外使用 `hasOwnProperty()`方法可以检测一个属性是存在于实例中，还是存在于原型中。这个方法（不 要忘了它是从Object 继承来的）只在给定属性存在于对象实例中时，才会返回true。
```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.getName = function() {
    return this.name;
}
var p1 = new Person('tim', 10);
console.log('name' in p1); // true
console.log('getName' in p1); // true
console.log(p1.hasOwnProperty('name')); //true
console.log(p1.hasOwnProperty('getName')); // false
```

in的这种特性最常用的场景之一，就是判断当前页面是否在移动端打开。
>isMobile = 'ontouchstart' in document;
// 很多人喜欢用浏览器UA的方式来判断，但并不是很好的方式。

#### 4.1 简单的原型语法

根据前面例子的写法，如果我们要在原型上添加更多的方法，可以这样写：
```javascript
function Person() {};
Person.prototype.getName = function() {}
Person.prototype.getAge = function() {}
Person.prototype.sayHello = function() {}
... ...
```

除此之外，我还可以使用更为简单的写法。
```javascript
function Person() {};
Person.prototype = {
    constructor: Person,
    getName: function() {},
    getAge: function() {},
    sayHello: function() {}
}
```
这种字面量的写法看上去简单很多，但是有一个需要特别注意的地方。Person.prototype = {}实际上是重新创建了一个{}对象并赋值给Person.prototype，这里的{}并不是最初的那个原型对象。因此它里面并不包含constructor属性。为了保证正确性，我们必须在新创建的{}对象中显示的设置constructor的指向。即上面的constructor: Person。

### 5. 原型链

原型对象其实也是普通的对象。几乎所有的对象都可能是原型对象，也可能是实例对象，而且还可以同时是原型对象与实例对象。这样的一个对象，正是构成原型链的一个节点。因此理解了原型，那么原型链并不是一个多么复杂的概念。

我们知道所有的函数都有一个叫做toString的方法。那么这个方法到底是在哪里的呢？先随意声明一个函数：
>function foo() {}

那么我们可以用如下的图来表示这个函数的原型链:
![原型链](http://upload-images.jianshu.io/upload_images/599584-da97dde356289ade.png?imageMogr2/auto-orient/strip%7CimageView2/2)
其中foo是Function对象的实例。而Function的原型对象同时又是Object的实例。这样就构成了一条原型链。原型链的访问，其实跟作用域链有很大的相似之处，他们都是一次单向的查找过程。因此实例对象能够通过原型链，访问到处于原型链上对象的所有属性与方法。这也是foo最终能够访问到处于Object原型对象上的toString方法的原因。

基于原型链的特性，我们可以很轻松的实现继承。

### 6. 继承

ECMAScript中描述了原型链的概念，并将原型链作为实现继承的主要方法。其基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。简单回顾一下构造函数、原型和实例的关系：**每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。**

我们常常结合构造函数与原型来创建一个对象。因为构造函数与原型的不同特性，分别解决了我们不同的困扰。因此当我们想要实现继承时，就必须得根据构造函数与原型的不同而采取不同的策略。

我们声明一个Person对象，该对象将作为父级，而子级cPerson将要继承Person的所有属性与方法。
```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.getName = function() {
    return this.name;
}
```

首先我们来看构造函数的继承。在上面我们已经理解了构造函数的本质，它其实是在new内部实现的一个复制过程。而我们在继承时想要的，就是想父级构造函数中的操作在子级的构造函数中重现一遍即可。我们可以通过call方法来达到目的。

```javascript
// 构造函数的继承
function cPerson(name, age, job) {
    Person.call(this, name, age);
    this.job = job;
}
```

而原型的继承，则只需要将子级的原型对象设置为父级的一个实例，加入到原型链中即可。
```javascript
// 继承原型
cPerson.prototype = new Person(name, age);
// 添加更多方法
cPerson.prototype.getLive = function() {}
```

![图示](http://upload-images.jianshu.io/upload_images/599584-c77eb714f66b8185.png?imageMogr2/auto-orient/strip%7CimageView2/2)
当然关于继承还有更好的方式，这里就不做深入介绍了，以后有机会再详细解读吧。

### 7. 小结

ECMAScript支持面向对象（OO）编程，但不使用类或者接口。对象可以在代码执行过程中创建和增强，因此具有动态性而非严格定义的实体。在没有类的情况下，可以采用下列模式创建对象。

>* 工厂模式，使用简单的函数创建对象，为对象添加属性和方法，然后返回对象。这个模式后来 被构造函数模式所取代。
* 构造函数模式，可以创建自定义引用类型，可以像创建内置对象实例一样使用 new 操作符。不过，构造函数模式也有缺点，即它的每个成员都无法得到复用，包括函数。由于函数可以不局 限于任何对象（即与对象具有松散耦合的特点），因此没有理由不在多个对象间共享函数。
* 原型模式，使用构造函数的 prototype属性来指定那些应该共享的属性和方法。组合使用构造 函数模式和原型模式时，使用构造函数定义实例属性，而使用原型定义共享的属性和方法。 JavaScript 主要通过原型链实现继承。原型链的构建是通过将一个类型的实例赋值给另一个构造函
数的原型实现的。这样，子类型就能够访问超类型的所有属性和方法，这一点与基于类的继承很相似。 原型链的问题是对象实例共享所有继承的属性和方法，因此不适宜单独使用。解决这个问题的技术是借 用构造函数，即在子类型构造函数的内部调用超类型构造函数。这样就可以做到每个实例都具有自己的属性，同时还能保证只使用构造函数模式来定义类型。**使用最多的继承模式是组合继承，这种模式使用原型链继承共享的属性和方法，而通过借用构造函数继承实例属性。**

参考來源：简书 过去式丶 http://www.jianshu.com/p/15ac7393bc1f
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处
参考书籍：JavaScript高级程序设计（第三版）第六章 面向对象的程序设计
