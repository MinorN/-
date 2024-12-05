# TypeScript——深入函数（1）

## 深入对象语法

```typescript
type Person = {
    name: string
}
interface Person {
    name: string
}
type Hash = {
    // 索引签名
    [k:string]: number,
    length: numer
}
type Hash = {
    // 映射类型，多用于范型，先铺垫一下
    [k in string]: number
    // 只要写了这一行，就不能再加其他属性了
}
// 问号表示可选
type Person = {
    name: string,
    age?: number
}
const person: Person = {
    name: 'MinorN'
}
// 不会报错，因为age是可选

// readonly 表示只读
type Person = {
    readonly id: number,
    readonly parent: string[]
    name: string,
    age?: number
}
const person: Person = {
    id: 1,
    name: 'MinorN',
    age: 18,
    parent: ['baba','mama']
}
person.id = 2 // 报错，因为id是readonly
person.parent = ['baba','erzi','mama'] // 报错，因为是readonly
person.parent[0] = 'nainai' // 不报错，因为只能管一层
```

## 深入函数

对象的语法全部都适用于函数

```typescript
type F = {
    (a:number.b:number): number
}
// 等价于
type F1 = (a:number.b:number) => number
// 普通声明函数采用F1的写法，如果想给函数加点属性，采取F的写法，如
type F = {
    (a:number.b:number): number,
    count: number
}
const f: F = (x,y)=>{
    return x + y
}
// 报错，count is missing
f.count = 1
// 报错消失
```

## 声明函数的四种方式

```typescript
function f(a: number): 返回值类型 {
    // ...
}
// 返回值类型可以不写, ts会自行推断
type F = (x: number) => number
const f: F = function (){
    // 
}
// 或者
const f = function (a:number): number{
    // 
}
type F = (x: number) => number
const f: F = (a:number) => {
	//    
}
const f: F = (a:number): number => {
	//    
}

```

1. 先写类型，然后把类型放在左边，比如上面倒数第二个例子
2. 先写箭头函数，再取类型
3. 先写普通函数，再取类型
4. 先写类型，在写函数

## 类型谓词

其实前面已经讲过了，可以去看联合类型里面的类型收窄

## 函数柯里化

```typescript
const add = (a: number,b: number) => a + b

const createAdd = (a: number) =>{
    return (b: number) => {
        return a + b
    }
}
const add6 = createAdd(6)
add6(14) // 6 + 14
```

