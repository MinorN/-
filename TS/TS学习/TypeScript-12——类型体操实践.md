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

