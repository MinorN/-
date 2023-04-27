# ES6——Set、Map

ES6新增了两种数据结构`Set`和`Map`(其实还有`WeakMap`和`WeakSet`，这两个先按下不表)，本篇文章就带你了解一下这两个数据结构

## Set

Set 是一种叫做集合的数据结构，或者也可以理解成‘桶’，是由一堆**无序的、相关联的、并且不重复的内存结构**组成的组合

你也可以把它近似看成是一个数组，但是其中每个成员都是唯一的，不会重复

```js
const s = new Set() // 构造了一个Set的数据结构
```

方法：

1. add
2. delete
3. has
4. clear

```js
const s = new Set() // 构造了一个Set的数据结构
s.add('hello').add('MinorN').add('MinorN')
console.log(s) // 你会发现，MinorN只有一个，对应上了Set的内容唯一
s.delete('hello')
console.log(s)
s.has('MinorN') // 返回值是一个boolean
s.clear() // 直接清空
```

如何遍历：

1. keys：键名
2. values：键值
3. entries：键值对
4. forEach：每个成员

这就不过多解释了，可以自己上手试一下

用处：目前来看，使用最多的场景就是**数组去重**和**字符串去重**：

```js
// 数组去重
const arr = [1,1,2,3,3,5,7,8,9,9]
const arr2 = [...new Set(arr)]

const str = '123355891'
const str2 = [...new Set(str)].join('') // 123589
// 转换成数组=>数组去重=>拼接成字符串


// 其实想想，那么是不是能够取并集、交集和差集呢？
const arr1 = [1,2,3]
const arr2 = [3,4,5]
// 并集
const union = new Set([...arr1,...arr2])
// 交集
const inter = new Set([...a].filter(item=>b.has(item)))
// 差集
const difference = new Set([...a].filter(item=>!b.has(item)))
```

## Map

Map 是键值对的有序列表，键、值都可以是任意类型的，可以理解成字典

```js
const map = new Map()
```

方法：

1. size：注意，这是一个属性，也就是 `map.size` 即可
2. set
3. get
4. has
5. delete
6. clear

```js
const map = new Map()

map.set('name','MinorN')
map.set('age','18').set('sex','男')
console.log(map)
console.log(map.size)  // 3
map.get('age') // 18
// 如果get取的key不在map中，则返回undefined
map.has('age') // true boolean
```

遍历也是同理，这里不过多介绍了

## Question：Map 和 JS的对象有什么区别？为什么需要有Map？

刚才看下来，是不是觉得`map`不就是`js`的对象吗？也可以这么理解，但是其实还是有很大区别的

1. Map 的 键的类型可以是任意的，而`js`的不行

```js
const map = new Map()
map.set('1','Hello').set(1,'MinorN').set('{id:1}','id')
console.log(map) // 可以自己log看一下，js的对象就做不到这样的格式
```

2. Map 可以直接进行遍历，`js`的对象会有一些限制

```js
// 常规的对象里面，为了遍历keys,values，必须for...in或者转换成数组，而且for...in还有一些限制，只能遍历可以枚举的属性
// 但是Map可以直接 for...of 或者 forEach来直接遍历，多方便
```

```js
// 来看看比较有趣的遍历Map
const map = new Map()
map.set('1','Hello').set(1,'MinorN').set('{id:1}','id')
console.log(...map.keys())
console.log(...map.values())
```

