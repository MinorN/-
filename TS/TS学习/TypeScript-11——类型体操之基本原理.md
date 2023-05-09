# TypeScript-11——类型体操之基本原理

## 体操基本原理

```typescript
//	比如想实现if(a<= b) true else false
type A = 1
type B = 1 | 2

type Result = A extends B ? true : false
```

如何判断一个空的元组呢？

```typescript
//	判断空元组
type A = []
type IsEmptyArray<Arr extends unknown[]> = Arr['length'] extends 0 ? true : false
type Result = IsEmptyArray<A>
```

如何判断一个非空元组？

```typescript
// 判断非空元祖
// 当然，把上边的true false 调换过来也可以实现

type A = [1]
type NotEmpty<Arr extends unknownp> = Arr extends [...infer X,infer Last] ? true : false
type Result = NotEmpty<A>
```

 `...infer X`是什么？？，`infer Last`又是什么？

换个方式来看一下

```typescript
type A = [1]
type NotEmpty<Arr extends unknownp> = Arr extends [...unknown [],unknown []] ? true : false
type Result = NotEmpty<A>

// [...unknown [],unknown []] 可以看成把前面那项展开，后面固定有一项，那么是不是就代表里面有内容，那么就可以判断元组是否非空
```

`infer`是什么，之后再细说

再来看看一个例子，我们如何翻转一个元组

```typescript
type A = ["wo","shi","MinorN"]
type Reverse<Arr extends unknown[]> = Arr extends [...infer Rest,infer Last]?		[Last,...Reverse<Rest>]:Arr
type Result = Reverse<A>
```

先判断当前元组是不是一个空的元祖，每次把Last放到第一个，递归调用即可

再来看一个

```typescript
type A = ["wo","shi","MinorN"]
type Result1 = A extends [infet First, ...infet Rest] ? First : never	// 'wo'
type Result2 = A extends [infet First, ...infer Rest] ? Rest : never	// ["shi","MinorN"]
```

## 元组体操

### 变长元组

```typescript
type A = [1]
type B = [...A, 2]
type C = [3,4]
type D = [...A, ...C]
```

### 取元组中的一个

```typescript
type D = [1,2,3,4]	// 注意！！！是类型！1 2 3 4 都是类型！！我们在学习TS
// 取最后一个
type Last<T> = T extends [...infer Rest, infer L] ? L : never
type E = Last<D>	// 4
// 取除了最后一个的所有
type NotLast<T> = T extends [...infer Rest, infer L] ? Rest : never
type E = Last<D>	// [1,2,3]
```

## 字符串体操

```typescript
type A = 'minorN'
type B = Capitalize<A>	// 'MinorN'
```

`Capitalize` 是 `ts` 内置的，还内置了三个，总共是四个，如下

* Uppercase：全大写
* Lowercase：全小写
* Capitalize：首字母大写
* Uncapitalize：首字母小写

```typescript
// 模版字符串
type A = 'hello'
type B = 'MinorN'
type Result = `${A} ${B}`	// 'hello MinorN'
```

### 如何获取字符串的第一个？

```typescript
type A = 'hello MinorN'
type First<T extends string> = T extends `${infer F}${string}` ? F : never
type Result = First<A>	// 'h'
```

### 如何获取字符串的最后一个？

```typescript
type A = 'hello MinorN'
type First<T extends string> = T extends `${string}${infer F}` ? F : never
type Result = First<A>	// 'ello MinorN'
// 不是我们想要的
```

经过如上操作发现并不是我们想要的，那么该如何做呢？

我们可以获取元组的最后一个，那么我们是不是可以吧字符串转换成元组，然后直接取元组最后一个就可以了

```typescript
// 获取元组最后一个
type Last<T extends unknown[]> = T extends [...infet Rest, infet L] ? L : never 

// 字符串变成元组
type StringToTuple<T extends string> = T extends `${infer F}${infer R}` ? [F, ...StringToTuple<R>] : []
type A = 'hello MinorN'
type LastOfString<T extends string> = Last<StringToTuple<T>>
type Result = LastOfString<A>	// 'N'
```

### 字符串转联合类型

```typescript
type StringToUnion<T extends string> = 
	T extends `${infer F}${infer R}` ? F |  StringToUnion<R> : never
```

## infer

说了这么多，来稍微介绍一下 `infer`，`infer`可以理解成`let(js)`，也就是声明了一个东西

我们拿上面一个例子来举例

```typescript
type NotEmpty<Arr extends unknownp> = Arr extends [...infer X,infer Last] ? true 
```

可以看到这里`infer X`和 `infer Last`，如果不写`infer`，`TS`会去找，X，Last 是什么？结果找不到报错，加上这个`infer`，也就是告诉`TS`，这是我声明的一个类型

官方地址 :[infer](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html)
