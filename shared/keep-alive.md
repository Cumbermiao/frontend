# keep-alive分享
> 抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在组件的父组件链中。

## 特性
* 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。
* 当组件在 <keep-alive> 内被切换，它的 activated 和 deactivated 这两个生命周期钩子函数将会被对应执行，且 activated 和 deactivated 将会在 <keep-alive> 树内的所有嵌套组件中触发。
* 不会在函数式组件中正常工作，因为它们没有缓存实例。

## 作用
* 主要用于保留组件状态或避免重新渲染。

## 缺点
* 常驻内存影响性能
* keep-alive不能缓存多层级路由菜单问题

## api
* include - 逗号分隔字符串、正则表达式或一个数组。只有名称匹配的组件会被缓存。
* exclude - 逗号分隔字符串、正则表达式或一个数组。任何名称匹配的组件都不会被缓存。
* max - 数字。最多可以缓存多少组件实例。

### 举个🌰
```
    <keep-alive :include="Home" :exclude="blackList" :max="amount">
        <component :is="currentComponent"></component>
    </keep-alive>
```

## 原理粗解
### keep-alive 方法定义
```
    // src/core/components/keep-alive.js
    export default {
      name: 'keep-alive',
      abstract: true, // 判断当前组件虚拟dom是否渲染成真是dom的关键（Vue在初始化生命周期的时候，为组件实例建立父子关系会根据abstract属性决定是否忽略某个组件）

      props: {
        include: patternTypes, // 缓存白名单
        exclude: patternTypes, // 缓存黑名单
        max: [String, Number] // 缓存的组件实例数量上限
      },

      created () {
        this.cache = Object.create(null) // 缓存虚拟dom
        this.keys = [] // 缓存的虚拟dom的健集合
      },

      destroyed () {
        for (const key in this.cache) { // 删除所有的缓存
          pruneCacheEntry(this.cache, key, this.keys)
        }
      },

      mounted () {
        // 实时监听黑白名单的变动
        this.$watch('include', val => {
          pruneCache(this, name => matches(val, name))
        })
        this.$watch('exclude', val => {
          pruneCache(this, name => !matches(val, name))
        })
      },

      render () {
        // 先省略...
      }
    }

```

### 缓存过程
> init -> $mount -> compile -> render -> vnode -> patch -> dom
1. keep-alive的渲染是在patch阶段，这是构建组件树（虚拟DOM树），并将VNode转换成真正DOM节点的过程。
2. 在patch阶段，会执行createComponent函数。keep-alive组件作为父组件，它的render函数会先于被包裹组件执行；那么就只执行时解析道keepAlive的值是true，后面的逻辑不再执行；再次访问被包裹组件时，是已经缓存的组件实例会执行insert逻辑，这样就直接把上一次的DOM插入到了父元素中。
3. 一般的组件，每一次加载都会有完整的生命周期，即生命周期里面对应的钩子函数都会被触发，而keep-alive包裹的组件不再进入$mount过程
4. 执行insert逻辑时会调用了activateChildComponent函数递归地去执行所有子组件的activated钩子函数。

## 参考
* [彻底揭秘keep-alive原理](https://juejin.im/post/5cce49036fb9a031eb58a8f9#heading-7)
* [keep-alive组件级缓存](https://juejin.im/post/5b407c2a6fb9a04fa91bcf0d#heading-5)
