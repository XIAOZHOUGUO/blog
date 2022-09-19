---
title: type-challanges 记录
date: 2022-05-30 11:43:03
category:
  - TypeScript
tags:
  - type-challanges
---

TypeScript 类型体操姿势合集记录 [type-challenges/README.zh-CN.md at main · type-challenges/type-challenges (github.com)](https://github.com/type-challenges/type-challenges/blob/main/README.zh-CN.md)
<!-- more -->
#### 298 - Length of String

>计算字符串的长度，类似于 `String#length`，[在 Github 上查看](https://tsch.js.org/298/zh-CN)。

```typescript
type LengthOfString<
    S extends string,
    arr extends unknown[] = []
> = S extends `${infer first}${infer rest}`
	? LengthOfString<rest,[...arr,first]> : arr['length']
```

#### 459 - Flatten

> 在这个挑战中，你需要写一个接受数组的类型，并且返回扁平化的数组类型。[在 Github 上查看](https://tsch.js.org/459/zh-CN)，例如:

```typescript
type flatten = Flatten<[1, 2, [3, 4], [[[5]]]]> // [1, 2, 3, 4, 5]
```

```typescript
type FlattenArrayVal<S extends any,T extends unknown[]=[]> = S extends unknown[]
	? S extends [infer first,...infer rest]
		?  FlattenArrayVal<rest, first extends unknown[]
        	? FlattenArrayVal<first,T>
            : [...T,first]>
		: T
	: S
type Flatten<S extends unknown[],T extends unknown[]=[]> =
	S extends [infer first,...infer rest]
	? Flatten<FlattenArrayVal<rest>, [...T,FlattenArrayVal<first>]>
	: T
```

解释：类型 `flattenArrayVal`的作用是处理数组中的每一项，非数组类型直接返回原样，数组类型返回扁平化后的数组，给`Flatten`使用。

#### 527 - Append to object

> 实现一个为接口添加一个新字段的类型。该类型接收三个参数，返回带有新字段的接口类型。[在 Github 上查看](https://tsch.js.org/527/zh-CN)，例如:

```typescript
type Test = { id: '1' }
type Result = AppendToObject<Test, 'value', 4> // expected to be { id: '1', value: 4 }
```

```typescript
type AppendToObject<T extends Object, U extends string, V> = {
  [K in keyof T | U]:  K extends keyof T ? T[K] : V
}
```

#### 529 - Absolute

> 实现一个接收string,number或bigInt类型参数的`Absolute`类型,返回一个正数字符串。[在 Github 上查看](https://tsch.js.org/529/zh-CN)，例如：

```typescript
type Test = -100;
type Result = Absolute<Test>; // expected to be "100"
```

```typescript
type Absolute<T extends number | string | bigint> =
	`${T}` extends `-${infer V}` ? V : `${T}`
```

#### 501 - String to Union

> 实现一个将接收到的String参数转换为一个字母Union的类型。[在Github 上查看](https://tsch.js.org/531/zh-CN)，例如：

```typescript
type Test = '123';
type Result = StringToUnion<Test>; // expected to be "1" | "2" | "3"
```

```typescript
type StringToUnion<T extends string> = T extends ""
	? never
	:  T extends `${infer first}${infer rest}`
		? first | StringToUnion<rest>
		: T
```

#### 599 - Merge

> 将两个类型合并成一个类型，第二个类型的键会覆盖第一个类型的键。[在 Github 上查看](https://tsch.js.org/599/zh-CN)，例如：

```typescript
type foo = {
  name: string;
  age: string;
}

type coo = {
  age: number;
  sex: string
}

type Result = Merge<foo,coo>; // expected to be {name: string, age: number, sex: string}
```

```typescript
type Merge<F extends Object, S extends Object> = {
  [K in keyof F | keyof S]: K extends keyof S ? S[K] : K extends keyof F ? F[K] : never
}
```

#### 612 - KebabCase

> FooBarBaz` -> `foo-bar-baz，[在Github上查看](https://tsch.js.org/612)。

```typescript
type FirstLowcase<T extends string> = T extends `${infer F}${infer R}`
  ? F extends Lowercase<F> ? T : `${Lowercase<F>}${R}`
  : T
type KebabCase<S extends string> = S extends `${infer F}${infer R}`
  ? R extends FirstLowcase<R>
      ? `${FirstLowcase<F>}${KebabCase<R>}`
      : `${FirstLowcase<F>}-${KebabCase<FirstLowcase<R>>}`
  : S
```

```typescript
type KebabMap = {
  A: "a"
  B: "b"
  C: "c"
  D: "d"
  E: "e"
  F: "f"
  G: "g"
  H: "h"
  I: "i"
  J: "j"
  K: "k"
  L: "l"
  M: "m"
  N: "n"
  O: "o"
  P: "p"
  Q: "q"
  R: "r"
  S: "s"
  T: "t"
  U: "u"
  V: "v"
  W: "w"
  X: "x"
  Y: "y"
  Z: "z"
}

type KebabCase<
  S extends string,
  U extends string = ""
> = S extends `${infer Target}${infer R}`
  ? Target extends keyof KebabMap
    ? U extends ""
      ? KebabCase<R, `${U}${KebabMap[Target]}`>
      : KebabCase<R, `${U}-${KebabMap[Target]}`>
    : KebabCase<R, `${U}${Target}`>
  : U

```

#### 645 - Diff

> 获取两个接口类型中的差值属性。[在 Github 上查看](https://tsch.js.org/645/zh-CN)，例如：

```typescript
type Foo = {
  a: string;
  b: number;
}
type Bar = {
  a: string;
  c: boolean
}

type Result1 = Diff<Foo,Bar> // { b: number, c: boolean }
type Result2 = Diff<Bar,Foo> // { b: number, c: boolean }
```

第一种：

```typescript
type Diff<O extends object, O1 extends object> = {
  [K in (keyof Omit<O & O1, keyof (O | O1)>)]:
    K extends keyof O ? O[K] : K extends keyof O1 ? O1[K]:neve
```

第二种：

```typescript
type Diff<O extends object, O1 extends object> = {
  [K in keyof Omit<O1, keyof O> | keyof Omit<O, keyof O1>]:
    K extends keyof O ? O[K] : K extends keyof O1 ? O1[K]:never
}
```

#### 949 AnyOf

> 在类型系统中实现类似于 Python 中 `any` 函数。类型接收一个数组，如果数组中任一个元素为真，则返回 `true`，否则返回 `fasle`。如果数组为空，返回 `false`。[在Github上查看](https://tsch.js.org/949/zh-CN)，例如：

```typescript
type Sample1 = AnyOf<[1, '', false, [], {}]> // expected to be true.
type Sample2 = AnyOf<[0, '', false, [], {}]> // expected to be false.
```

第一种：

```typescript
type FalsyVal<V> = V extends [] | Record<string, never> | '' | 0 | false ? false : true
type AnyOf<T extends readonly any[]> = true extends FalsyVal<T[number]> ? true : false
```

第二种：

```typescript
type FalsyVal = [] | Record<string, never> | '' | 0 | false
type AnyOf<T extends readonly any[]> = T extends [infer Head, ...infer Tail] ? Head extends FalsyVal ? AnyOf<Tail> : true : false
```

#### 1042 IsNever

> Implement a type IsNever, which takes input type `T`. If the type of resolves to `never`, return `true`, otherwise `false`. [在Github上查看](https://tsch.js.org/1042)。For example:

```typescript
type A = IsNever<never>  // expected to be true
type B = IsNever<undefined> // expected to be false
type C = IsNever<null> // expected to be false
type D = IsNever<[]> // expected to be false
type E = IsNever<number> // expected to be false
```

第一种：

```typescript
// https://github.com/microsoft/TypeScript/issues/31751#issuecomment-498526919
// https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types
type IsNever<T> = [T] extends [never] ? true : false
```

第二种：

```typescript
type IsNever<T> = {[key: string]: T} extends {[key: string]: never} ? true : false;
```

