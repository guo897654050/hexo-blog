---
title: typescript的使用及总结
date: 2022-07-06 19:47:08
tags: typescript
---
### ts数组上的方法
如何理解`T[number]`和`T['length']`
1. `T[number]`获取到数组的元素，为联合类型。
2. `T['length']`获取数组的长度。
```
type A = ['a', 'b', 'c']
type B = A['length'] //3
type C = A[number] // 'a'| 'b' | 'c'

type A = string[];

type B = A['length']; // number;
type C = A[number]; //string;

```
### ts类型分发和收窄类型

```
type r1 = string | number extends string ? 1 : 2 // 2; 由于类型是收窄的，所以返回false;

type R<T> = T extends string ? 1 : 2;
type Test = string | number;
type r2 = R<Test> // 2 | 1; 由于传入的泛型，是不确定的类型，因此联合类型会进行分发 string extends string | number extends number 
```

any收窄类型会有些特殊，any可以这么理解`any 代表了任何可能的类型，当我们使用 any extends 时，它包含了“让条件成立的一部分”，以及“让条件不成立的一部分”。`
```
type a = any extends number ? 1 : 2 // 1; 虽然类型收窄了，但是按照上述理解可以把any理解为string extends number | number extends number

type b = any[] extends number[] ? 1 : 2 // 1;这里是由于[]包裹的对象不会进行分发，相当于any extends number 为true
```

### ts的infer的使用
1.  `infer`必须搭配`extends`使用。
2.  可以把`infer`理解为占位符。
3.  推断的结果必须在`extends`位true的分支使用。

例子：promise的类型推断。
```
type ExampleType = Promise<string>

type Result = <MyAwaited<ExampleType> // string

// 请实现MyAwaited类型来获取promise的T类型

type MyAwaited<T> = T extends promise<infer R>
    ? R extends Promise<any> 
      ? MyAwaited<R>
      : R
    : never
```
从上述例子可以看出`infer`的使用，首先使用`extends`关键字， 同时推断出R为promise<T>类型，若仍未为promise<any>类型，则递归执行。

<!-- more -->
### ts的联合类型的使用
1. 需要注意的是当联合类型使用`extends`会进行分发，相当于每个联合类型都会执行`extends`方法。
2. 想要不进行联合类型的分发可以使用`[T]`包裹。

分别看两个例子,首先是联合类型的分发。
1. 第一个例子
```
// 描述，将联合类型转化为交叉类型。
type UnionToIntersection<U> =  (U extends any ? (args: U) => void : never) extends 
  (args: infer R) => void ? R :never
// 测试用例
type U0 = UnionToIntersection<string | number> // never
type U1 = UnionToIntersection<{ name: string } | { age: number }> // { name: string; } & { age: number; }
```
我们将例子拆开看，以`U0`为例，U的联合类型每一个执行一次这个方法`(U extends any ? (args: U) => void : never) `则会生成`(args: string) => void`|`(args: number) => void`,同时推断出一个类型R，能同时满足上述的要求,那么满足该条件一定是`string&number`推断出为`never`。

2. 第二个例子，
```
// 描述，实现一个`IsNever`工具类型，判断指定的类型是否为 never 类型
type IsNever = [T] extends [never] ? true : false;
type I0 = IsNever<never> // true
type I1 = IsNever<never | string> // false
type I2 = IsNever<null> // false
```
上述将联合类型使用`[]`包裹，那么就不会进行分发，`string|never` 不可能extends`never`因此返回false。

### ts内置类型的实现
1. Pick
```
type Pick<T, keys extends keyof T> = {
  [key in keys]: T[key]
}

type Foo = {
  a: 1,
  b: 2
}

type q = Pick<Foo, 'a'>, // {a: 1}

```

2. Omit
```
type Omit<T, keys extends keyof T> = Pick<T, Exclude<T, keys>>

type Foo = {
  a: 1,
  b: 2
}

type q = Omit<Foo, 'a'>, // {b: 2}

```
3. Partial
```
type Partial<T> = {
  [K in keyof T]?: T[K]
}

type Foo = {
  a: 1,
  b: 2
}

type q = Partial<Foo>, // {a?:1, b?: 2}
```
4. Exclude,联合类型分发
```
type Exclude<T, U> = T exntends U ? never : T;

type Foo = {
  a: 1,
  b: 2
}

type q = Exclude<keyof Foo, 'a'>, // { b: 2}

```
5. Required
```
type Required<T> = {
  [K in keyof T]-?: T[K]
}

type Foo = {
  a?: 1,
  b?: 2
}

type q = Required<Foo> // {a: 1, b: 2}
```

6. returnType
```
type ReturnType<T> = T extends (args: any) => infer R ? R : never;

function foo(x: number): Array<number> {
  return [x];
}
type fn = ReturnType<typeof foo>; // Array<number>

```

### ts的as const 的使用
1. `as const`会使得目标对象变成只读。
2. `as const `会将类型变成元组。
3. `string number boolean`  字面量类型都不会被扩展；
```
{
    readonly name: "foo";
    readonly age: 12;
}
*/
const readonlyObj = { name: 'foo', age: 12 } as const

/**
readonly [1, "foo", "bar"],并非推断出的[number, string, string]类型
*/
const readonlyTuple = [1, 'foo', 'bar'] as const;

```

例子: 实现下述ts类型
```

declare function PromiseAll<T extends any[]>(value: readonly [...T]): Promise<{[K in keyof T ]: T[K] extends Promise<infer R> ? R : T[K]}>

const promiseAllTest1 = PromiseAll([1, 2, 3] as const)
const promiseAllTest2 = PromiseAll([1, 2, Promise.resolve(3)] as const)
const promiseAllTest3 = PromiseAll([1, 2, Promise.resolve(3)])

type cases = [
  Expect<Equal<typeof promiseAllTest1, Promise<[1, 2, 3]>>>,
  Expect<Equal<typeof promiseAllTest2, Promise<[1, 2, number]>>>,
  Expect<Equal<typeof promiseAllTest3, Promise<[number, number, number]>>>,
]


```
