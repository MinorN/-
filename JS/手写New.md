# 手写New

本质就是理解 new 做了什么？

1. 创建this
2. 绑定原型
3. 运行当前的函数
4. 返回this

```js
function mynew(func,...rest){
    const obj = {}
    obj.__proto__ = func.prototype
    // 上面两行也可以写成 Object.create(func.prototype)
    let result = func.apply(obj,rest)
    if(typeof result === 'object' && result !== null){
        return result
    }
    // 上面是对函数的返回值做判断
    return obj
}

// eg:
function Cat(name){
    this.name = name
    // return [] 这就是上面对函数返回值类型做判断的原因
}

Cat.prototype.say = function(){
    console.log(this.name)
}

let c = mynew(Cat,'MinorN')
```

