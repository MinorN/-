# TypeScript中的类型——（2）

## `any`和`unknown`

`any`：表示可以是任意类型，也就是所有类型

`unknown`：表示不知道，那么怎么用？

```typescript
const a:unknown = 1
console.log(a)
a.toFixed() // 报错，因为类型是unknown
// 怎么用？
const a:unknown = await axios.get('xxx') // 一开始不知道类型
(a as number).toFixed()
```

## `never`类型

`never`是不属于任何类型，可以理解成集合，那么为什么又`never`这个类型呢？

可以用来做检查

```typescript
type A = string & number
// 问：A的类型是什么？string和number的交集？怎么可能有交集？
// 所以产生了never
// 所以never不是用来声明类型的，是用来推断的，所以写ts里面不能出现never
```

## `enum`类型

### 什么时候用`enum`？

```typescript
// example 1
enum A {
    todo = 0, // 逗号分隔 表示里面枚举值对应的真实值
    done, // 默认是往上+1
    archived,
    deleted
}
let status:A = 0
status = A.done // status = 1

// example 2
enum Permission {
    None = 0, // 0000
    Read = 1 << 0, // 左移0位 0001
    Write = 1 << 1, // 0010
    Dealete = 1 << 2, // 0100
    Manage = Read | Write | Delete, // 0111
}
type User = {
    permission:Permission
}
const user = {
    permission: 0b0101, // 0b 表示二进制
}
if((user.permission & Permission.write) === Permission.Write){
    console.log('拥有写权限')
}
```

### 什么时候不用`enum`

```typescript
enum Fruit {
    apple = 'apple',
    banana = 'banana',
    watermelon = 'watermelon'
}
let f:Fruit = Fruit.apple
f = Fruit.watermelon
// 很蠢

type Fruit = 'apple' | 'banana' | 'watermelon'
let f:Fruit = 'apple'
f = 'watermelon'
```

## `type`和`interface`

### 什么时候用`type`？

基本上什么时候都能用`type`，`type`——类型别名，给其他类型取个名字

```typescript
type Name = string
const a:Name = 'hi' // 等价于 const a:string = 'hi'
// 还可以做一些操作（之后再说）
type False = 0 | '' | null | undefined | false
type Points = number[]
type Fn = (a:number,b:number) => number
type FnWidthProps = {
    (a:number,b:number):number
    props:string
}
const f:FnWidthProps = (x,y)=>{
    return x+y
} // 报错 props 不见了
f.props = 'hi' // 报错消失
```

```typescript
// 为什么type叫做类型别名？
type A = string
type B = A // 发现B是string，A并没有被记住
```

### 什么时候用`interface`？

用来声明接口，描述 **对象** 的属性（包括函数和非函数）

```typescript
interface A extends Array<string>{
    name: string
}
// 表示 A 是一个字符串数组,有一个属性叫做name
type A1 = Array<string> & {
    name: string
}
// 等价于 A

interface Fn {
    (a:number,b:number): number
}
const f:Fn = (x,y)=>{return x+y}
```

### `type` vs `interface`

1. `interface` 只描述对象，`type` 描述所有数据
2. `interface` 是类型声明，`type` 是别名
3. `type` 不可重新赋值，`inerface` 会自动合并，对外`API`尽量用 `interface`，方便拓展，对内`API`尽量用 `type`

```typescript
type A = number
A = string // 错误，不可以重新赋值

interface X {
    name: string
}
interface X {
    age: number
}
const a:X = {
    name:'nihao',
    age:18
}
// interface 会合并
```

## `void` 类型

```typescript
type Fn = () => void
const f:Fn = () =>{
    return 'xxx'
}
// 不会报错，虽然return了，但是用不了
```

