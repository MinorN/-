# TypeScript——类型兼容

## 为什么要有类型兼容？

因为实际工作中往往无法做到类型一致

```typescript
const config = {
    a: 1,
    b: 2,
    c: 3,
    d: 4
}
// 比如test 只需要三个属性
const test = ({a,b,c})=>{
    creturn a + b + c
}
const newConfig = lodash.pick(config,['a','b','c'])
// lodash.pick 表示从config里面取key = a b c
test(config)
test(newConfig)
```

## 什么是类型兼容

通俗来讲，你有的，我都有，我有的，你没有

`Eg：y有的，x都有，那么x兼容y，任何用y的地方都能用x`

```typescript
type A = string | number
const a:A = 'hi'
```

## 接口如何兼容

常用的就是父子接口的兼容

```typescript
interface baba {
    name: string
}
interface erzi extends baba {
    age: number
}
let objChild: erzi = {
    name: 'MinorN',
    age: 18
}
let objParent: baba
objParent = objChild
```

## 函数如何兼容

函数有参数和返回值

1. 参数个数不同，能兼容吗？

```typescript
let one = (a: number) =>{
    //
}
let two = (a: number,b: number) =>{
    //
}
two = one // 不报错
one = two // 报错
// 想想，写JS的时候是不是会少写参数？
arr.forEach((item,index,arr)=>{}) // 是不是很少用到第三个参数？
```

2. 参数类型不同，能兼容吗？

```typescript
interface Event {
    targer: string
}
interface MouseEvent extends Event {
    x: number,
    y: number
}
let listener = (e: Event) => {}
let mouseListener = (e: MouseEvent) =>{}
mouseListener = listener
listener = mouseListener // 报错
```

