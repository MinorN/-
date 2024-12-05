# TypeScript——泛型进阶

## example 4

```typescript
type Person = {
    name:strgin,
    age:number，
    id:number
}
type Readonly<T> = {
    readonly [K in keyof T] : T[K]
}
type X1 = Readonly<Person>

type Partial<T> = {
    [K in keyof T] ?: T[K]
}
type X2 = Partial<Person>

type Required<T> = {
    [K in keyof T] -?: T[K]
}
type X3 = Required<Person>

type Record<Key extends string|number|symbol,Value> = {
    [k in Key] : Value
}
// key 只有三种情况 string number symbol
type X4 = Record<string,number>

type Exclude<A,B> = A extends B ? never : A
// 看A的每一项在不在B里面，其实就是 乘法分配率
// 1 extends 1|2 ？ =>never
// 2 extends 1|2 ？ =>never
// 3 extends 1|2 ？ =>3
type X5 = Exclude<1|2|3, 1|2>  // 3

type Extract<A,B> = A extends B ? A : never
// 同理
type X6 = Extract<1|2|3, 2|4>  // 2

type Omit<T,K> = {
    [Key in keyof T as Key extends K ? never : Key] : T
}
// ？？？？？
// 分成两段
// Key in keyof T ,就是 Key必须是 T 的 key
// 后面 as 是断言,接上前面的，如果得出来的Key 在K里面，就是never，否则保留
type X7 = Omit<Person,'name'|'age'>
// Omit 是忽略

// 取 name age
type Pick<T,Key extends keyof T> = {
    [K in key] : T[K]
}
type X8 = Pick<Person, 'name'| 'age'>

// 如何用Pick来实现Omit
type Omit<T,Key extends keyof T> = Pick<T, Exclude<keyof T,Key>>

```

## example 5

```typescript
type Person = {
    readonly name:strgin,
    readonly age:number，
    readonly id:number
}

type Mutable<T> = {
    -readonly [K in keyof T]: T[K]
}

type Y = Mutable<Person> // 从 只读 变为 可写
```

## 推荐练习

[Type Chanllenges](https://github.com/type-challenges/type-challenges)

