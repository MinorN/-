# TypeScript—— 深入函数（2）

## 函数重载

也就是同名函数，为了解决接受参数类型不同、接受参数个数不同，或者都不同

```typescript
// 参数类型不同，没有必要重载了
function print(x: string | number | boolean){
    console.log(x)
}
// 参数个数不同，使用重载，也就是同名不同参
function createDate(n: number): Date;
function createDate(year: number,mouth: number,date: number):Date;
function createDate(a: number, b?:number, c?:number): Date{
    if(a !== undefined && b !== undefined && c !== undefined){
        return new Date(a,b,c)
    }else if( a!==undefined && b === undefined && c === undefined){
       	return new Date(a)
    }else{
        throw new Error('只接受一个或三个参数')
    }
    // 也可以使用 arguments.length 来判断，但是TS会不知道argument是什么意思，会报错，不推荐
}
```

## 指定 this 类型

```typescript
type Person = {
    name: string
}
function f(this: Person,word: string){
    console.log(this.name + word)
}
// 怎么调用，直接掉？错了，传的参数不是this，可以试试
f('hello')
// 如何传
// 1. 拼凑
const p: Person &{f?: typeof f} = {
    name: 'MinorN'
}
p.f = f
p.f('hello')
// 2. f.call(this)
f.call(p,'hi')
// 3. f.apply(this) 接受的第二个参数是个数组
f.apply(p,['hi'])
// 4. f2 = f.bind(this,p1,p2,p3) 绑定，提前把参数传过去
const f2 = f.bind(p)
f2('hi') // 相当于 f.bind(p)('hi')
```

## `...`与参数

### 剩余参数

把剩下的参数都收集起来

```typescript
function sum(...argus: number[]){
    let result = 0
    for(let i = 0;i< argus.length;i++){
        result+=argus[i]
    }
    return result
    // return argus.reduce((result,n)=>result+n,0)
}
sum(1,2,3)
sum(1,2,3,4,5,6,7)
// 当然也可以这样
function sum(name: string,...argus: number[]){}
```

注：剩余参数必须写在最后，要不然会有歧义，我什么时候停止收集参数？

### 展开参数

```typescript
function sum(name:number,...argus: number[]){
    f(argus) // 报错，f需要的是number，但是你传递了数组
    // 1. f.apply(null,argus)
    // 2. f(...argus)
}
function f(...array: number[]){
    console.log(array)
}
```

## `as const` 是什么

```typescript
let a1 = 'b' // string，因为let会修改
const a2 = 'b' // 类型是 'b'，因为const不能被修改
let a3 = 'b' as const // 类型是'b'，因为as const
// 很难受，为啥会有这样的情况？
const array = [1,'hi']
// array 的类型？ 1. (number | string)[]  2. readonly [1,'hi']
// 类型是 (number | string)[]，但是还能够push，只不过不能把整个数组修改 array = 100
const array = [1,'hi'] as const 
// 类型是 readonly [1,'hi'] ，这时候不能push
```

`as const` 也并不是都能用的，只有`enum`,`boolean`,`number`,`array`,`object`,`string`

```typescript
let a = (1+2) as const // 报错
```

```typescript
function f(){
    const a = [1,2]
    sum(...a) // 报错，因为a的类型是number[]，不知道到底有几个
    const b = [1,2] as const 
    sum(...b) // 正确
}
function sum(a:number,b:number){
    return a+b
}
```

## 参数对象的析构

```typescript
type Config = {
    url: string,
    method: 'GET' | 'POST' | 'PATCH' | 'DELETE',
    data?: unknown,
    headers?: unknown
}
function ajax(config: Config){
    const { url , method, ...rest } = config // 析构语法
}
function ajax({ url , method , ...rest }: Config){
    console.log(url,method,rest)
}
// 参数默认值
function ajax({ url , method , ...rest } :Config = {method:'GET',url:''}){
    console.log(url,method,rest)
}
function ajax({ url , method , ...rest } = {method:'GET',url:''}) as Config{
    console.log(url,method,rest)
}
```