# 在 vue 中使用 JSX

## JSX 配置

1. 安装依赖
```yarn add @vue/babel-preset-jsx @vue/babel-helper-vue-jsx-merge-props -D ```

2. 配置 babel ， 具体参数配置参考 [文档](https://github.com/vuejs/jsx/tree/dev/packages/babel-preset-jsx) 。
```"presets": ["@vue/babel-preset-jsx"]```

## JSX 使用

1. 循环语句
```js
render(){
  const arr = [1,2,3,4]
  return (
    <ul>
      {
        arr.map(item=> {
          // 逻辑处理
          return (<li>{item}</li>)
        })
      }
    </ul>
  )
}
```

2. 条件判断
```js
render(){
  const showLabel = true
  const status = 'edit'

  return (
    <div>
      <input type='radio' name='gender'>
      {showLabel && (<label>性别</label>)}

      {
        status === 'edit' ? (<input type='text'>) : (<span>文字</span>)
      }
    </div>
  )
}

```

3. 动态属性
 
属性区分参考[文档](https://cn.vuejs.org/v2/guide/render-function.html#%E6%B7%B1%E5%85%A5%E6%95%B0%E6%8D%AE%E5%AF%B9%E8%B1%A1)
```js
render(){
  const inheritProps = {
    on: {
      click: function(){}
    },
    attrs: {
      disabled: false
    }
  }
  return (
    <div>
      <button {...inheritProps}>submit</button>
    </div>
  )
}
```

4. slot 传递

常用于组件封装场景, 通过 `this.$slots.default` 方式调用

## JSX 的优势

比 createElement 简单， 比 template 灵活。

```js
render(h){
  return h('div',{ class: 'form-cell'},[
    h('input', { class:'form-input' attrs: {type:'text', value:this.value}, on: {input: this.handleInput}})
  ])
}
```

```js
render(){
  return (
    <div class='form-cell'>
      <input type='text' class='form-input' vModel={this.value}>
    </div>
  )
}
```








