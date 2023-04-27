# TypeScript——泛型

## 什么是泛型？

泛：多；泛型：多种类型

**只要能看懂JS的函数，其实就能看懂TS的泛型**

```typescript
// js
const f = (a,b)=>a+b
const result = f(1,2)
// 返回的是值

// ts
type F<A,B> = A | B
type Result = F<string,number> // string | number
// 返回的是类型
```

## 为什么会有泛型？

```typescript
function echo(n:string | number | boolean){
    return n
}
// Q:echo的返回值类型是什么呢？
// A: string | number |boolean ? 错误，是要有一一对应的关系，接受string返回string，接受number返回number

// 那么手动进行类型收窄？
function echo(n:string | number | boolean){
    switch(typeof n){
        case 'number':
            return n
            break
        case 'string':
            return n
            break
        case 'boolean':
            return n
            break
    }
}
// 可以试试？还是做不到！
```

## example 1

```typescript
type Union<A, B> = A | B

type Union3<A, B, C> = A | B | C

type Intersect<A, B> = A & B

type Intersect3<A, B, C> = A & B & C

interface List<A> {
    [index: number]: A
}
type X = List<string>
type Y = List<string | number>

interface Hash<V> {
    [key: string]: V
}

interface Hash<V = string> {
    [key: string]: V
}
// 默认值为 string
```

## example 2

```typescript
type Person = {name : string}

type LikeString<T> = T extends string ? true : false
type LikeNumber<T> = T extends number ? 1 : 2
type LikePerson<T> = T extends Person ? 'yes' : 'no'
type R1 = LikeString<'hi'>  // true
type R2 = LikeString<true>  // false
type S1 = LikeNumber<666666>  // 1
type S2 = LikeNumber<false>  // 2
type T1 = LikePerson<{name:'minorn',xxx:1}>  // yes
type T2 = LikePerson<{xxx:1}>  // no
```

规则：(**前提是使用泛型**)

1. 如果传的类型是never，条件类型不适用，返回的类型是never

```typescript
const a = LikeString<never>  // never
```



2. 如果T为联合类型，则**分开计算**

```typescript
type ToArray<T> = T extends unknown ? T[] : never

type Result = ToArray<string | number>
// string[] | number[]
```

## 在泛型中使用keyof

```typescript
type Person = {name:sting,age:number}
type GetKeys<T> = keyof T
type Result = GetKeys<Person>  // 'name' | 'age'
```

## example 3

```typescript
type Person = {name:sting,age:number}
type GetKeyType<T,K extends keyof T> = T[K]
type Result = GetKeys<Person,'name'>  // string
```

