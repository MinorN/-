# 在JS项目中使用TS

## `*.d.ts`

比如声明了一个`types.d.ts`文件，内容如下

```typescript
type User = {
    name: string;
    ageL number;
}
```

可以直接在其他文件中使用

```typescript
// main.ts
type A = User
```

也就是声明了`.d.ts`文件中的类型**全局可用**

**但是，如果 `.d.ts` 文件中存在 `import` 或者 `export` 则只在当前模块生效**

Q：如果我非要加 `import` 或者 `export`还想全局生效，怎么办？

```typescript
// types.d.ts
declare global{
    interface User{
        name: string;
        age: string;
    }
}

export {}
```

## `.d.ts` +`js`

```js
// js 文件： a.js
window.add = function(n){
    return n + 1
}

// ts 文件: b.ts
const x = add(1)
// 你会发现用不了，这时候就需要声明类型

// .d.ts 文件 或其他
const add: (n: number) => number
// 这句话等价于
// type Add = (n: number) => number
// const add: Add
// 然后就能使用了
```

## 浏览器的类型声明来自哪里？

是不是自己写代码的时候会写到如下代码

```typescript
const app = document.getElementById('app')
```

那么，为什么我们打出`document`会自动提示方法呢？它的类型是什么？`app`的类型是`HTMLElement | null`，`HTMLElement`又是从哪来的？

可以去看`tsconfig.json`

```json
"lib":["DOM",...]
// DOM就是全局的变量
```

## React 18 类型在哪？

需要自己安装类型

```bash
npm i -d @types/react@18
```

## Vue3类型在哪？

Vue3 是基于TS写的，所以不需要自己手动安装类型

## Node.js 类型在哪？

需要自己安装类型

```bash
npm i -d @types/node@版本
```