---
title: 一窥 vue3 响应式系统
category:
  - Vue3
tags:
  - reactive
date: 2022-05-25 10:07:59
updated: 2022-05-26 15:05:00
---

在 `Vue` 中，响应式系统采取的是`发布订阅`的模式，`Vue2` 中主要依赖  ES5 `Object.defineProperty` 的 API，在 `vue3` 中重写了响应式系统，使用的是 ES6 `Proxy` `Reflect`，在它们不受支持的情况下会降级使用 `Object.defineProperty`。今天我们来实现一个极其简易版的响应式系统。
<!-- more -->
### 前提

JS 本身并没有响应式的特性，那 Vue 是如何实现这一魔法的呢，我们先来看下：

```javascript
let product = {
  name: '西葫芦',
  price: 10,
  quantity: 1,
}
let total = product.quantity * product.price // 10
product.price = 12
console.log('total :>> ', total); // 10
```

`total` 并没有自动更新为 12。我们可以修改下：

```javascript
let product = {
  name: '西葫芦',
  price: 10,
  quantity: 1,
}
let total = 0;
const effect = () => {
  total = product.quantity * product.price
}
effect() //total: 10
product.price = 12
effect() //total: 12
```

但此时我们仍然需要手动执行下 `effect` 才能让 `total` 的值变成"响应式"，如何能够自动更新呢？我们可以先修改下：

```javascript
const targetMap = new WeakMap();
function track(target, key) {
  let depsMap = targetMap.get(target)
  if (!depsMap) {
    targetMap.set(target, (depsMap = new Map()))
  }
  let deps = depsMap.get(key)
  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  deps.add(effect)
}
function trigger(target, key) {
  const depsMap = targetMap.get(target)
  if (!depsMap) {
    return
  }
  const deps = depsMap.get(key)
  if (deps) {
    deps.forEach(effect => effect())
  }
}

let product = {
  name: '西葫芦',
  price: 10,
  quantity: 1,
}
let total = 0;
const effect = () => {
  total = product.quantity * product.price
}
track(product, 'price')
product.price = 12
trigger(product, 'price') //total: 12
```

如上，我们收集了依赖，当它被改变时，触发更新，使得 `total` 更新成功，这里使用到了 ES6 中的`WeakMap` `Map` `Set`。

> WeakMap  对象是一组键值对的集合，其中的**键是弱引用对象，而值可以是任意**。WeakMap 的 key 是不可枚举的，因为 WeakMap 中每个键对自己所引用对象的引用都是弱引用，在没有其他引用和该键引用同一对象时这个对象将会被垃圾回收（相应的key则变成无效的）。

### 简易版本响应式

如何能让依赖发生变更时自动更新呢，我们更新如下：

```javascript
const targetMap = new WeakMap();
function track(target, key) {
  let depsMap = targetMap.get(target)
  if (!depsMap) {
    targetMap.set(target, (depsMap = new Map()))
  }
  let deps = depsMap.get(key)
  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  deps.add(effect)
}
function trigger(target, key) {
  const depsMap = targetMap.get(target)
  if (!depsMap) {
    return
  }
  const deps = depsMap.get(key)
  if (deps) {
    deps.forEach(effect => effect())
  }
}

const reactive = (target) => {
  const handler = (target, {
    get(target, key, receiver) {
      console.log('get key :>> ', key);
      track(target, key)
      return Reflect.get(target, key, receiver)
    },
    set(target, key, value, receiver) {
      console.log('set key :>> ', key);
      const oldValue = target[key]
      const result = Reflect.set(target, key, value, receiver)
      if (oldValue !== value) {
        trigger(target, key)
      }
      return result
    }
  })
  return new Proxy(target, handler)
}

let product = reactive({
  name: '西葫芦',
  price: 10,
  quantity: 1,
})
let total = 0;
const effect = () => {
  total = product.quantity * product.price
}
effect() // 建立 quantity 和 price 的依赖和副作用
console.log('total :>> ', total); // 10
product.price = 12
console.log('total :>> ', total); // 12
```

成功了🙌，但是呢，如果我们在末尾加上一个

```javascript
console.log('name :>>', product.name)
```

此时，由于读取了 `name` ，由于拦截会重新进入到 `getter` 中，会重新 `track`，会为 `name` 建立多余的依赖和副作用 effect，虽然副作用本身和 name 并无关系。所以，我们可以在此基础上优化之：

```javascript
let activeEffect = null;
const targetMap = new WeakMap();

function effect(eff) {
  activeEffect = eff;
  eff();
  activeEffect = null;
}
function track(target, key) {
  if (activeEffect) {
    let depsMap = targetMap.get(target)
    if (!depsMap) {
      targetMap.set(target, (depsMap = new Map()))
    }
    let deps = depsMap.get(key)
    if (!deps) {
      depsMap.set(key, (deps = new Set()))
    }
    deps.add(activeEffect)
  }
}
function trigger(target, key) {
  const depsMap = targetMap.get(target)
  if (!depsMap) {
    return
  }
  const deps = depsMap.get(key)
  if (deps) {
    deps.forEach(effect => effect())
  }
}

const reactive = (target) => {
  const handler = (target, {
    get(target, key, receiver) {
      console.log('get key :>> ', key);
      track(target, key)
      return Reflect.get(target, key, receiver)
    },
    set(target, key, value, receiver) {
      console.log('set key :>> ', key);
      const oldValue = target[key]
      const result = Reflect.set(target, key, value, receiver)
      if (oldValue !== value) {
        trigger(target, key)
      }
      return result
    }
  })
  return new Proxy(target, handler)
}

let product = reactive({
  name: '西葫芦',
  price: 10,
  quantity: 1,
})
let total = 0;
effect(() => {
  total = product.quantity * product.price
})
console.log('total :>> ', total); // 10
product.price = 12
console.log('total :>> ', total); // 12
```

如上，已经比较好的实现了我们想要的效果。让我们来看看下面的测试用例：

```javascript
let product = reactive({
  name: '西葫芦',
  price: 10,
  quantity: 1,
})
let total = 0;
let saleTotal = 0;
effect(() => {
  total = product.quantity * product.price
})
effect(() => {
  saleTotal = total * 0.8
})
console.log('total,saleTotal :>> ', total, saleTotal); //10 8
product.price = 12
console.log('total,saleTotal :>> ', total, saleTotal); //12 8
```

`total` 确实响应式更新了，但是 `saleTotal` 并没有相对应更新，这是因为这里的 `total` 并不是响应式数据，那如何让它变为响应式数据呢，

1. 我们可以借助已经实现的 `reactive` ，包装如下，不过有些繁琐🤣

```javascript
const ref = (initVal) => {
  return reactive({ value: initVal })
}
let product = reactive({
  name: '西葫芦',
  price: 10,
  quantity: 1,
})
let total = ref(0);
let saleTotal = 0;
effect(() => {
  total.value = product.quantity * product.price
})
effect(() => {
  saleTotal = parseFloat(total.value * 0.8).toFixed(2) / 1
})
console.log('total,saleTotal :>> ', total.value, saleTotal); //10 8
product.price = 12
console.log('total,saleTotal :>> ', total.value, saleTotal); //12 9.6
```

2. 使用 ES5 提供的对象访问器或者 JS 的计算属性 `Object Accessors`。

```javascript
const ref = (raw) => {
  const r = {
    get value() {
      track(r, 'value')
      return raw
    },
    set value(newVal) {
      console.log('newVal,raw :>> ', newVal, raw);
      if (newVal !== raw) {
        raw = newVal
        trigger(r, 'value')
      }

    }
  }
  return r
}
let product = reactive({
  name: '西葫芦',
  price: 10,
  quantity: 1,
})
let total = ref(0);
let saleTotal = 0;
effect(() => {
  total.value = product.quantity * product.price
})
effect(() => {
  saleTotal = parseFloat(total.value * 0.8).toFixed(2) / 1
})
console.log('total,saleTotal :>> ', total.value, saleTotal); //10 8
product.price = 12
console.log('total,saleTotal :>> ', total.value, saleTotal); //12 9.6
```

接下来实现 `Vue3`中的 真正的计算属性`computed`：

```javascript
const computed = (getter) => {
  const result = ref()
  effect(() => result.value = getter())
  return result
}
let product = reactive({
  name: '西葫芦',
  price: 10,
  quantity: 1,
})
const total = computed(() => product.price * product.quantity)
const saleTotal = computed(() => parseFloat(total.value * 0.8).toFixed(2) / 1)
console.log('total,saleTotal :>> ', total.value, saleTotal.value); //10 8
product.price = 12
console.log('total,saleTotal :>> ', total.value, saleTotal.value); //12 9.6
```
