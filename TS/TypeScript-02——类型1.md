# TypeScript中的类型——（1）

## js/ts中有哪些数据类型？

`JS`：

* null
* undefined
* string
* number
* boolean
* bigint
* symbol
* object（含Array、Function...）

`TS`：

* 以上所有
* void
* never
* enum（枚举）
* unknown
* any
* 再加上自定义类型`type(类型别名)`、`interface`

## 如何理解TS的数据类型？

比如：`type number = 1| 1.1 | ....` 所有的数字包含在一起

也就是，TS的数据类型是满足某一条件的集合

Question:` a:number = 2` 和 `a = new Number(2)`有什么区别?

Answer:  `a = 2` 表示只是数字，在内存中村的是二进制，`a = new Number(2)` 还会包含一些方法，比如`toFixed`等等。

所以JS中的Numer、String只用于包装对象，在TS里面也不用。

## 问题：type Object 表示的范围也太大了

所以在`TS`总不用`Object`

那么如何在TS里面描述对象的数据类型？

1. 用`class/constructor`描述

```typescript
const a : Function = ()=>{console.log('hello word')}
```

2. 用`type`或`interface`描述

```typescript
   type Person = {
       name:string,
       age:number
   }
   
   const person:Person = {
       name:'hello',
       age:18
   }
   // 如果person中缺少name或者age都会报错,多了也会报错
   // 如果对应的类型不一样也会报错
```

```typescript
   type A = {
       [k:string]:number
   }
   
   const a:A ={
       name:1,
       123:6
   }
   // 表示对象的key必须是string，值必须是number
```

   那么？`key`的类型可不可以不是`string`？——还可以是number和symbol

```typescript
   type A = {
       [k:number]:number
   }
   // 上面叫做 索引签名
   
   const a:A ={
       name:1, // 会报错
       123:6
   }
   const b:A={
       123:6 //不会报错
   }
   
   type B = {
       [k:symbol]:number
   }
   const s = new Symbol()
   const c:B = {
       [s]:1  // 为什么不写成 s:1，因为这里s会被当成string，所以symbol要被[]包裹
}
```

还可以这么写

```typescript
type A = Record<string,number>
// 和上边 A 是一样的
```

## 数组对象 该怎么描述？

   ```typescript
   const arr:Array = [1,2,3]  //直接报错
   const arr:Array<number> = [1,2,3]  // 不会报错
   
   // 还有如下写法
   type A = string[] // 等价于 type A = Array<string>
   const a:A = ['name','age']
   
   type B = number[]  // 等价于 type B = Array<number>
   const b:B = [1,2,3] 
   
   type F = [string[],number[]]
   const f:F = [['name','age'],[1,2]]
   ```

   由于Array不精确，所以一般使用`Array<?>`或者`string[]`等来描述数组

## 函数对象，该怎么描述？

```typescript
type FnA = (a:number,b:number) => number
// 表示函数接受两个参数，第一个参数类型为number，第二个也为number，返回值类型为number
const a:FnA = ()=>{
	return 1
}
// 不报错，不是接受两个参数嘛？后续讲，涉及到类型兼容
const a:FnA = (x,y)=>{
	return 1
}
// 把鼠标放到x，y上，会显示x，y的类型，这叫做类型推导
type FnB = (a:number,b:string) => void
// 表示函数接受两个参数，第一个参数类型为number，第二个为string，返回值类型为void(空)
```

来看个恶心的

```typescript
type FnWithThis = (this:Person,name:string) => void
type Person = {name:string,age:number}
const sayHi:FnWithThis = function(){
    console.log('hi' + this.name) // 这使用了this，所以不能用箭头函数
}
const x:Person = {name:'nihao',age:18}
sayHi.call(x,'Jack')
```

## 描述其他对象

一般使用`class`

```typescript
const d:Date = new Date()
const r:RegExp = /ab+c/
const r2:RegExp = new RegExp('ab+c')
const m:Map<string,number> = new Map()
m.set('age',18)
const s:Set<number> = new Set()
s.add(1)
```



   