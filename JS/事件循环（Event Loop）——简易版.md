# 事件循环（Event Loop）

首先写出来一个不需要事件循环的代码

```js
console.log('1')
console.log('2')
// 1 2
```

加入`setTimeout`后的代码

```js
console.log('1')
setTimeout(()=>{
    console.log('3')
})
console.log('2')
// 1 2 3
```

加入了`Promise.resolve('x').then(fn)`

```js
console.log('1') // 任务
Promise.resolve('x').then(()=>{
    console.log('ti') // 小任务
})
setTimeout(()=>{
    console.log('3') // 加班
})
console.log('2') // 任务
// 1 2 ti 3
```

小任务可以无限地加，导致`console.log('3')`无法执行。所以设置了超限，也就是限制了小任务执行的次数。

```js
console.log('1')
queueMicrotask(()=>{
    console.log('m1')
    queueMicrotask(()=>{
        console.log('m2')
    })
}) // 小任务
console.log('2')
```

- 安排任务(Macrotask)：

  `setTimeout`/`setInterval`/`requestAnimationFrame`等

- 安排小任务(microtasks)

  `Promise`/`queueMicrotask`/`MutationObserver`

执行顺序：任务=>小任务=>小任务=>...超限

​				   上班=>加班=>加班=>...ICU

```js
console.log('1')
setTimeout(()=>{
    console.log('大')
    queueMicrotask(()=>{
        console.log('小2')
    })
})
queueMicrotask(()=>{
        console.log('小')
})
console.log('2')
// 1 2 小 大 1 2 小 大 小2
```

```js
console.log('1')
setTimeout(()=>{
    console.log('大')
})
queueMicrotask(()=>{
    console.log('小')
    setTimeout(()=>{
    	console.log('大2')
	})
})
console.log('2')
// 1 2 小 大 大2
```

小任务可以插队！！！