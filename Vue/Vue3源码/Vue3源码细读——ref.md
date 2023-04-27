# Vue3源码细读——ref

`ref`所在的文件路径`'packages/reactivity/src/ref.ts'`

由于本人水平有限，所有观点仅个人理解，欢迎大佬补充修正！！！

先在改文件下搜索了`ref`，然后看到了这里

```typescript
export function ref<T extends Ref>(value: T): T
export function ref<T>(value: T): Ref<UnwrapRef<T>>
export function ref<T = any>(): Ref<T | undefined>
export function ref(value?: unknown) {
  return createRef(value, false)
}

```

这里 最主要的是最后一个函数，发现`return`了一个`createRef(value, false)`

按照思路，找到`createRef`

```typescript
function createRef(rawValue: unknown, shallow: boolean) {
  if (isRef(rawValue)) {
    return rawValue
  }
  return new RefImpl(rawValue, shallow)
}
```

这里发现内部做了判断，如果传递进来的本来就是`ref`，那么就可以直接返回这个值，如果不是`ref`，那就返回一个`RefImpl`的实例,接下来我们看看这个实例（构造函数）做了什么

```typescript
class RefImpl<T> {
  private _value: T	// 私有变量 _value
  private _rawValue: T	// 私有变量 _rawValue

  public dep?: Dep = undefined	// dep 目前是干什么的不知道
  public readonly __v_isRef = true	// 只读，说明当前是个 ref

  // 第一次进来会进入 set
  constructor(value: T, public readonly __v_isShallow: boolean) {
    this._rawValue = __v_isShallow ? value : toRaw(value)	// 还记得ref的第二个参数是false，可以理解成不是浅响应，这时候 _rawValue 需要取到原始值（对象）
    this._value = __v_isShallow ? value : toReactive(value)	// 同理，只不过这个 _value 取得是Proxy代理的对象
  }

  get value() {
    // 读取，返回的是当前代理的 _value
    trackRefValue(this)	// 不知道是什么，先不管
    return this._value
  }

  set value(newVal) {
     // 修改
    const useDirectValue = this.__v_isShallow || isShallow(newVal) || isReadonly(newVal)
    // 发现上面有isShallow，isReadonly,进入源码看看,先看下面哦
    
    // 看完之后可以判断出当前 useDirectValue === false
    newVal = useDirectValue ? newVal : toRaw(newVal)
    // newVal 取到 原始值（原始对象）
    if (hasChanged(newVal, this._rawValue)) {
      // 这里很重要，拿当前值和旧值做对比，hasChanged根据名字可以判断出是用来判断是否发生改变的函数
      this._rawValue = newVal
      this._value = useDirectValue ? newVal : toReactive(newVal)
      // 这里 _value 是 toReactive(newVal)，这个toReactive做了什么先不管，等到 reactive 源码阅读的时候在进行讲解，目前就理解变成一个 Peoxy 的代理即可
      triggerRefValue(this, newVal)	// triggerRefValue不知道是什么先不管
    }
  }
}
```

`isShallow，isReadonly`源码：`packages/reactivity/src/reactive.ts`

```typescript
export const enum ReactiveFlags {
  SKIP = '__v_skip',
  IS_REACTIVE = '__v_isReactive',
  IS_READONLY = '__v_isReadonly',
  IS_SHALLOW = '__v_isShallow',
  RAW = '__v_raw'
}

export function isReadonly(value: unknown): boolean {
  return !!(value && (value as Target)[ReactiveFlags.IS_READONLY])
}

export function isShallow(value: unknown): boolean {
  return !!(value && (value as Target)[ReactiveFlags.IS_SHALLOW])
}
```

稍微看完了一下`ref`的源码，知道了为什么我们在控制台`log`的时候为什么会有`_value`啊什么的函数，其实这些都是为了方便我们去进行一个判断对比，尤其是`_rawValue`，存储了上一次`ref`的值，这样子可以省去不必要的更新，比如

```js
const name = ref('MinorN')

name.value = 'MinorN'
```

在这种情况下，新值和旧值没有区别，就不必更新，节省了性能（不必要的开销），可以自己去试一下哦，比如页面一个`button`，点击按钮修改值，然后`watch`这个`ref`，会发现`watch`并没有执行

还记得刚才我们忽略的两个函数吗？`trackRefValue`和`triggerRefValue`，它们的声明同样在`ref.ts`这个文件里面，我们来看看

```typescript
// 说在前面：
// __DEV__这个是Vue的一个区分开发环境和生产环境的变量，开发环境需要提供代码的报错啊等一些具体信息，但是开发环境不需要，所以，我们在看到__DEV__这个变量的时候，可以忽略，不会影响到对代码的理解
// get
export function trackRefValue(ref: RefBase<any>) {
  if (shouldTrack && activeEffect) {
    ref = toRaw(ref)
    // 这里的ref是原始对象
    if (__DEV__) {
      // 什么，你还在看__DEV__？直接去看else！
      trackEffects(ref.dep || (ref.dep = createDep()), {
        target: ref,
        type: TrackOpTypes.GET,
        key: 'value'
      })
    } else {
      trackEffects(ref.dep || (ref.dep = createDep()))	// 这个又是啥？先不管
    }
  }
}

// set
export function triggerRefValue(ref: RefBase<any>, newVal?: any) {
  ref = toRaw(ref)
  // 这里的ref是原始对象
  const dep = ref.dep
  // 还记得我们的 dep 是 undefined吗？下面代码就不用看了，那么这个代码是干什么用的呢？等之后有用到再来看吧
  if (dep) {
    if (__DEV__) {
      triggerEffects(dep, {
        target: ref,
        type: TriggerOpTypes.SET,
        key: 'value',
        newValue: newVal
      })
    } else {
      triggerEffects(dep)
    }
  }
}
```

看完了吧，好像就是取到原始对象，然后继续调用方法，这里我们来看一下`trackEffects`这个函数：`packages/reactivity/src/effect.ts`

```typescript
// 还记得我们是没有dep的吗？我们这个函数是这么使用的
// trackEffects(ref.dep || (ref.dep = createDep()))
export const createDep = (effects?: ReactiveEffect[]): Dep => {
  const dep = new Set<ReactiveEffect>(effects) as Dep
  dep.w = 0
  dep.n = 0
  return dep
}
// 这个 createDep 好像就是创建了个 Set 然后把Set的 w 、n 设置为0，这是在干什么？看不懂先不管
export const wasTracked = (dep: Dep): boolean => (dep.w & trackOpBit) > 0
export const newTracked = (dep: Dep): boolean => (dep.n & trackOpBit) > 0
// trackOpBit 不用管，先不理会
// createDep newTracked 路径: packages/reactivity/src/dep.ts

let effectTrackDepth = 0	// 但是有其他地方修改了这个 effectTrackDepth 先不管
// effectTrackDepth 当前正在递归跟踪的数量
const maxMarkerBits = 30
// 按位跟踪标记最多支持 30 级递归。
export function trackEffects(
  dep: Dep,
  debuggerEventExtraInfo?: DebuggerEventExtraInfo
) {
  let shouldTrack = false
  if (effectTrackDepth <= maxMarkerBits) {
    if (!newTracked(dep)) {
      // 还记得dep我们的 n 是 0 吗？
      dep.n |= trackOpBit
      shouldTrack = !wasTracked(dep)
    }
  } else {
    shouldTrack = !dep.has(activeEffect!)
  }

  if (shouldTrack) {
    dep.add(activeEffect!)
    // 往 dep 里面 添加 activeEffect ，activeEffect是什么之后简单介绍
    activeEffect!.deps.push(dep)
    // 关联
    // __DEV__！太好了，直接不看
    if (__DEV__ && activeEffect!.onTrack) {
      activeEffect!.onTrack(
        extend(
          {
            effect: activeEffect!
          },
          debuggerEventExtraInfo!
        )
      )
    }
  }
}
```

这里，我用文字来简单说一下`activeEffect`，我们它目前是出现在我们ref的读取操作里面的对吧，那么什么会触发我们的读取呢？很简单，比如一些函数中用到了它，那就会触发读操作，Vue是一个响应式框架，在你改变值的时候，视图也跟着改变，其实就是将当前的这个`ref`和那些函数做了关联，也就是`activeEffect`，那其实，当我们第一次进行读取的时候就得把这个关联存起来（正如代码里面使用Set进行存储一样），然后在我改变的时候，我可以通过这些关系对其进行更新（也就是响应），从而达到响应式的感觉，所以，你可以发现，在`get`的时候起的函数名叫做`trackRefValue`（track本身就是跟踪、踪迹），那么，我们就可以有理由的判断，当我们对`ref`进行`set`操作的时候会触发`triggerEffects`，这里面一定做了的事情就是把刚才我们存储起来的关系进行依次的更新

好了，关于`ref`的源码暂且先到这里，如果哪地方有错误，希望大佬们指出，我也很希望自己进步！