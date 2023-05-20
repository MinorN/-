# 手写简化版Promise

```js
class Promise2{
    #status = 'pending', // 私有属性，不被用户修改
    q = [],
    constructor(fn){
        const resolve = (data) =>{
            this.#status = 'fulfilled'
            const f1f2 = this.q.shift()
            const x = f1f2[0].call(undefined,data)
            if()
        }
        const reject = ()=>{
            this.#status = 'rejected'
        }
        fn.call(undefined,resolve,reject)
    },
    then(f1,f2){
        this.q.push([f1,f2])
    },
}


const p = new Promise(function(resolve,reject){
    // 成功
    resolve(data)
    // 失败
    reject(reason)
})

p.then(f1,f2)
```

