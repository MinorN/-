# TS的运行和调试

## ## 如何运行TS

```tsx
const a = 1 + 2
console.log(a)
// js代码，浏览器、node可直接运行
const a :number = 3
console.log(a)
// 报错 放在Deno里面可以正常运行
// 在线运行 https://www.mycompiler.io/new/deno
```

## 什么是类型擦除？

从`ts`转换成`js`的过程就叫做类型擦除。

## 如何类型擦除？

1. `esbuild`
2. `swc`
3. `tsc`
4. `babel`
