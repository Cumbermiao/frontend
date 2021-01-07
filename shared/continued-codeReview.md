# 系统配置--系统信息--告警设置--持续周期（continued.vue） code-review

## 定义公共变量
1. `text`页面名称
2. `type`页面英文名称，用于调用通用接口的参数
3. 将限制条件（大于0的整数），放入`inputValidate.js`
因为告警设置中几个页面很类似，有些公共的地方可以复用，所以将变量集中起来，减少修改、以及出错率。


## 完善逻辑
1. 因为Switch点击后会自动修改他绑定的值，所以在更新接口的添加了catch里面，在接口发生异常的时候将Switch绑定的值复原。   
  ```
    handleDisable(scope) {
      this.editLoading = true
      const { row: { id, state }, $index: index } = scope
      this.handleUpdate({ id, state, typeCode: this.type }).catch(() => {
        this.$refs.advanceTable.updateData((data) => {
          data.splice(index, 1, { ...data[index], state: state === 1 ? 0 : 1 })
        })
      }).finally(() => {
        this.editLoading = false
      })
    }
  ```
2. 因为在新增/编辑弹框和整个页面中存在loading，所以在请求接口之后，无论成功与否都得关闭loading。
```
  this.handleAdd(formData).then(() => {
    this.$refs.advanceTable.init()
    this.closeDialog()
  }).finally(() => {
    this.toggleLoading()
  })
```

## 说在后面的
因为本页面使用了`advance-table`和`advance-table`组件，减少了大量的代码与逻辑处理，并且组件中提供了一些获取数据以及清空表单等方法。成熟后可以考虑升级老版本页面。
