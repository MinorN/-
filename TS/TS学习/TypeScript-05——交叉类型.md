# TypeScript——交叉类型

## 什么是交叉类型

就是交集！

```typescript
type A = string & number // A 就是 never ，因为string和number没有交集
```

## 什么时候用交叉类型

一般用于描述对象

```typescript
type Left = {left: string}
type Right = {right: string}
type C = Left & Right // 表示既有left，又有right
```

接口也能求交集

```typescript
interface Color {
    color: string
}
interface Circle {
    radius: number
}
type ColorCircle = Color & Circle
```

还可以模拟继承

```typescript
type Person = {
    name: string,
    age: number
}
type User = Person & {
    id: number,
}
const my: User = {
    name:'MinorN',
    age: 18,
    id: 1
}
// Question: 如果有重复的属性，且冲突，怎么办？
// Eg:
type Person = {
    name: string,
    age: number,
    id: string
}
type User = Person & {
    id: number,
} // 这时候User类型不报错
const my: User = {
    name:'MinorN',
    age: 18,
    id: 1 // 报错，此时id被TS理解成never类型
}
// 来看看 interface 怎么做
interface Person  {
    name: string,
    age: number,
    id: string
}
interface User extends Person {
    id: number,
} // 这时候User类型报错，这也是 type 和 interface 的区别
```

