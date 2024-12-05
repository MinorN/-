# TypeScript——联合类型

## 联合类型是什么？

联合类型，也可以理解成并集(`|`)

```typescript
type A = number
type B = string
type C = A | B  // 表示C的类型是A和B类型的并集

type A = {name:string}
type B = {age:number}
type C = A | B
const c:C = {
    name: 'nihao',
    age: 18
}
const d:C = {
    name: 'nihao'
}
const e:C = {
    age: 18
}
```

## 如何使用联合类型？

```typescript
const f = (a:number | string) => {
    // a[0] 错误
    // 既不能把a当作number，也不能当作string
    // 那么怎么使用a变量呢
}
// 想办法把类型区分开————类型收窄(Narrowing)
const f = (a:number|string) => {
    if(typeof a === 'number'){
        a.toFixed(2)
    }else{
        a.split(',')
    }
}
```

## 用`JS`来做类型收窄

```typescript
// typeof 没有办法对所有的类型进行判断 
// 比如typeof null = object; typeof [] = object
```

### 使用`instanceof`

```typescript
const f = (a:Date | Date[]) =>{
    if(a instanceof Data){
        // Date
    }else{
        // Date[]
    }
}
```

缺点：

1. 不知道简单类型
2. 不支持`TS`独有的类型

```typescript
type Person = {
    name: string
}
type Animal = {
    name: string
}
const f = (a:Person | Animal) =>{
    // ???区分不了了
}
```

### 用`in`来做类型收窄

```typescript
type Person = {
    name: string
}
type Animal = {
    xxx: string
}
const f = (a:Person | Animal) =>{
    if('name' in a){
        // Person
    }else if('xxx' in a){
        // Animal
    }
}
// 只适合部分对象
```

### 使用JS中判断类型的函数来区分

```typescript
const f = (a:string | string[]) =>{
    if(Array.isArray(a)){
        // string[]
    }else {
        // string
    }
}
// 依然无法支持TS的独有类型
```

那么有没有完全的方法？

## 类型谓词`is`

```typescript
type Rect = {
    height: number,
    width: number
}
type Circle = {
    center: [number.number],
    radius: number
}

const f = (a: Rect | Circle) =>{
    if(isRect(a)){
        // Rect
    }else{
        // Circle
    }
}

function isRect(x: Rect | Circle): x is Rect{
    return 'height' in x && 'width' in x
}
function isCircle(x: Rect | Circle): x is Circle{
    return 'radius' in x && 'center' in x
}
// x is Rect 如果写成 boolean
// 那么 if 判断就是只能是true/false TS还是无法知道是什么类型（虽然我们自己知道了）
```

优点：支持所有`TS`类型；缺点：写起来麻烦

那么，有没有更简单的方法？

## 可辩别联合

```typescript
type A = {kind: 'string',value: string}
type B = {kind: 'number',value: number}

const f1 = (a: A|B ) => {
	if(a.kind === 'string'){
        // A
    }else{
        // B
    }
}
// 调用很傻
f1({kind:'string',value:'18'})
// 一般 React 常用
```

优点：让复杂类型的收窄变成简单类型的对比

如何用？总结起来：同名，可辩别的key

## 思考题

### `any` =  所有类型的联合？（不包含`never/unknow/void`）为什么？

不是，之前理解成所有类型是因为没有学习到类型收窄，因为联合类型就不能用了，先得判断类型才能使用

```typescript
const f = (a: any) =>{
    a.split()
    a.join()
    a.toFixed()
}
// 都不报错，有种 any 是在不停改变自己的类型的感觉
// 如果不想使用类型检查，那就用 any
```

### 什么 =  所有类型的联合？（不包含`never/unknow/void`）为什么？

`unknown`，表示是未知类型，我的选择太多，我不知道怎么选，可以去收窄
