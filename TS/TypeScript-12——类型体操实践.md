# TypeScript-12——类型体操实践1

先放类型体操实践的地址：[type-challenge](https://github.com/type-challenges/type-challenges/blob/main/README.zh-CN.md)

## 实现TS内置的Pick

描述：**从类型 `T` 中选择出属性 `K`，构造成一个新的类型**。

```typescript
type MyPick<T,K extends keyof T> = {
    [P in K]: T[p]
}
```

## 实现TS内置的 Parameters 类型

```typescript
// Eg
const foo = (arg1: string, arg2: number): void => {}

type FunctionParamsType = MyParameters<typeof foo> // [arg1: string, arg2: number]
```

```typescript
type MyParameters<T extends (...args:any[])=>any> = {
    T extends (...args:infer X)=>unknown ? X : never
}
// T 继承 一个函数，函数的入参是 any ，出参也是 any
```

## 实现 Awaited

描述：假如我们有一个 Promise 对象，这个 Promise 对象会返回一个类型。在 TS 中，我们用 Promise 中的 T 来描述这个 Promise 返回的类型。请你实现一个类型，可以获取这个类型。

```typescript
// Eg
type ExampleType = Promise<string>
type Result = MyAwaited<ExampleType> // string
```

```typescript
type MyAwaited<T extends Promise<any>> = {
    T extends Promise<infer X> ? 
    	X extends Promise<any> ? MyAwaited<X> : X
    : T
}
// 多一层是为了防止Promise里面嵌套Promise
```

## 实现Zip（4471）

描述：In This Challenge, You should implement a type `Zip<T, U>`, T and U must be `Tuple`

```typescript
// Eg
type exp = Zip<[1, 2], [true, false]> // expected to be [[1, true], [2, false]]
```

```typescript
type MyZip<T extends any[],K extends any[]> = {
    A extends [infer AFirst,...infer ARest]
    	?B extends [infer BFirst,...infer BRest]
    		?[[AFirst,BFirst],...Zip<ARest,BRest>]
			:[]
		:[nixuy]
}
```

## 实现IsTuple（4484）

描述：Implement a type `IsTuple`, which takes an input type `T` and returns whether `T` is tuple type.

也就是判断是否是元组

```typescript
// Eg
type case1 = IsTuple<[number]> // true
type case2 = IsTuple<readonly [number]> // true
type case3 = IsTuple<number[]> // false
```

```typescript
type IsTuple<T> = {
    [T] extends [never] 
    ? fasle
    : T extends readonly any[]
    	? number extends T['length'] 
    		? false 
    		: true
    	: T extends {length: number}
    		? false
    		: true
}
```

## 实现Join（5310）

描述：Implement the type version of Array.join, Join<T, U> takes an Array T, string or number U and returns the Array T with U stitching up.

```typescript
type Res = Join<["a", "p", "p", "l", "e"], "-">; // expected to be 'a-p-p-l-e'
type Res1 = Join<["Hello", "World"], " ">; // expected to be 'Hello World'
type Res2 = Join<["2", "2", "2"], 1>; // expected to be '21212'
type Res3 = Join<["o"], "u">; // expected to be 'o'
```

```typescript
type Join<T extends string[],K extends string | number> = {
    T extends [infer First extends string, ...infer Rest extends string[]]
    	? Rest['length'] extends 0 
    		? First
    		: `${First}${K}${Join<Rest,K>}`
    	: ''
}
```
