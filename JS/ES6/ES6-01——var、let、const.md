# ES6——var、let、const

## var

声明**变量**可用，在ES6没出之前，基本都是用var来进行变量声明的

1. var会变量提升

网上很多教程都会说，var会变量提升，let不会，什么什么的，那么猜猜下面会log什么？

```js
var a = 1 
console.log(a)
console.log(b)
var b = 2 
```

先会`log`出来`1`，然后是`undefined`

```js
// 所谓变量提升，指的是把变量的声明提升，注意是变量的声明
// 上面的代码就会看成
var a
a = 1
var b
console.log(a)
console.log(b)
b = 1
```

2. 其实var还有一个比较致命的缺点

```js
var name = 'MinorN'
var step = 1

if(step > 0){
    var name = 'fk!'
}

console.log(name)
// 这时候log出来什么？？
// fk!
```

## let

let 是目前声明**变量**的首选，它解决了刚才所说var的问题

1. 不会变量提升
2. 有块级作用域

```js
// 1.不会变量提升
let a = 1 
console.log(a)
console.log(b)
let b = 2 
// 直接报错 ReferenceError：b is not defined

// 2.有块级作用域
let step = 1
if(step > 0){
    let hello = 'MinorN'
}
console.log(hello)  
// 报错 RferenceError: hello is not defined
// 需要注意哦，在浏览器F12页面最好不要去用name这个字段，因为被占用了
```

需要注意的是：let 可以进行更新但是不能重新声明

```js
let a = 1
let a = 2
// SyntaxError: Identifier 'a' has already been declared
```

## const

const 是用来声明**常量**的，它和let一样，有块级作用域，同时不能重新声明，并且const也不能更新，那么，看看如下例子：

```js
// 1.const 重新声明
const a = 1
const a = 2
// SyntaxError: Identifier 'a' has already been declared

// 2.块级作用域
let step = 1
if(step > 0){
    const hello = 'MinorN'
}
console.log(hello)  
// RferenceError: hello is not defined

// 3. 来看看下面这个
const result = []
result.push('xxx')
// 不报错，为什么？
// 由于本人水平有限，我的理解是这样的:
// const看成一个特工，现在安排监视一个房子，现在这个特工比较傻，他就监听房子，并不在乎房子里面有什么，只要保证这个房子一直是这样的就行了，不要有损坏、推倒重盖
```

## 总结

|                                      | var  | let  | const |
| ------------------------------------ | :--: | :--: | :---: |
| 是否可重复声明                       |  是  |  否  |  否   |
| 是否变量提升（也可以说成暂时性死区） |  是  |  否  |  否   |
| 是否拥有块级作用域                   |  否  |  是  |  是   |
| 是否可修改                           |  是  |  是  |  否   |

## 结论

能用const尽量使用const，比如声明数组、map，其他情况下尽量使用let，避免或者说不要使用var