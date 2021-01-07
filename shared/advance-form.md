# advance-form
> 基于jsx + element-ui 配置化渐进增强的表单组件

## 应用场景
> 通过配置化的形式进行表单类新建、编辑、及表格头部的筛选

## 使用方法
```
  <template>
    <advance-form
      ref="advanceForm"
      :rules="rules"
      :form-items="formItems"
      :form-footer="formFooter"
      label-width="80px"
    >
      <template slot="formItem" slot-scope="scope">
        <el-date-picker
          v-if="scope.prop === 'date'"
          v-model="scope.model.date"
          type="daterange"
        />
      </template>
    </advance-form>
  </template>
  <script>
    export default {
      data() {
        return {
          formItems: [
            {
              label: '节日名称',
              prop: 'name',
              itemType: 'input',
              rules: { required: true, trigger: 'change', validator: rules.FormValidate.Form().required },
              child: {
                placeholder: '请输入节日名称'
              }
            },
            {
              label: '日期',
              prop: 'date',
              useSlot: true,
              initialValue: []
            },
            {
              label: '节日',
              prop: 'description',
              itemType: 'select',
              selectArr: []
              child: {
              }
            }
          ]
          formFooter: {
            actions: [
              {
                text: '确定',
                type: 'primary',
                submit: true,
                loading: false,
                click: this.handleSubmit
              },
              {
                text: '取消',
                reset: true,
                click: this.closeDialog
              }
            ]
          },
        }
      }
    }
  </script>
```

## 字段简述
### formItems
```
  formItems: [
    {
      label: '名称', // 表单label名称
      prop: 'name', // 表单prop值
      initialValue: '', 表单默认值
      itemType: 'input', // 表单类型。目前：[input, upload, button, select, date, step]
      rules: { required: true, trigger: 'change', validator: rules.FormValidate.Form().required }, // 校验规则
      styles: {}, // 样式
      child: { // 表单类型本身属性值（可为element-ui 组件上各种属性值）
        placeholder: '请输入节日名称',
        events: {} //  表单类型本身方法，如change等
      }
    }
  ]
```

### formFooter
> 设计初衷为表单底部提交按钮区域。后续迭代后，可通过refs  访问组件方法 ，故该配置项为可选项
```
  formFooter: { // 配置项为对象类型，可定义任意form-item 属性
    actions: [ // 表单底部按钮
      {
        text: '确定',
        type: 'primary',
        submit: true, // 是否为提交类型，会返回真个表单值
        loading: false, // 点击时显示loading
        click: this.handleSubmit // 点击事件
      },
      {
        text: '取消',
        reset: true, // 是否为重置类型，会自动重置内容
        click: this.closeDialog // 点击事件
      }
    ]
  }
```

## 开发过程
### renderForm
```
  render(h) {
    const form = this.$_renderForm(h)
    return (
      <div class='advance-form'>
        {form}
      </div>
    )
  }

  $_renderForm(h) {
    const { rules, model, formItems, inlineFormItemsRight, pure = false } = this
    const props = {
      ...this.$attrs
    }
    const { styles = {}} = this.$attrs
    const form = (
      <Form
        rules={rules}
        style={styles}
        {...{
          on: {
            ...this.$listeners
          },
          props: {
            model,
            ...props
          }
        }}
        ref='form'
      >
        // pure 参数为是否为单纯表单结构，false的话会按照左右两栏来进行标签放置（主要出于css设置，后续将优化为单一结构）
        {
          pure
            ? this.$_renderAllFormItems(h, formItems)
            : <div class='form-main'>
              <div class='form-left'>
                {this.$_renderAllFormItems(h, formItems)}
              </div>
              <div class='form-right'>
                {this.$_renderAllFormItems(h, inlineFormItemsRight)}
              </div>
            </div>
        }
        {
          this.$_renderFooter(h)
        }
      </Form>
    )
    return form
  }
```

### renderAllFormItems
```
  $_renderAllFormItems(h, formItems) {
    return formItems
      .map(formItem => {
        const {
          useSlot = false,
          itemType = ''
        } = formItem

        if (useSlot) {
          // 使用插槽列
          return this.$_renderSlotItem(h, formItem)
        } else if (itemType === 'input') {
          return this.$_renderInputItem(h, formItem)
        } 
        ...
        ...
      })
  }
```

### renderItem
```
  $_renderInputItem(h, formItem) {
    const {
      model
    } = this
    const {
      label,
      events = {},
      prop = '',
      tips = '',
      disabled = false,
      styles = {},
      child = {},
      ...rest
    } = formItem
    const {
      events: childEvents = {},
      suffix = '',
      ...childRest
    } = child
    return (
      <FormItem
        label={label}
        prop={prop}
        style = {styles}
        class='form-item'
        {...{
          props: rest,
          on: {
            ...events
          }
        }}
      >
        <Input
          vModel={model[prop]}
          rows={rows}
          {...{
            props: childRest,
            on: {
              ...childEvents
            }
          }}
        >
          {
            suffix && <span slot='append'>{suffix}</span>
          }
        </Input>
        {
          tips ? <p>{tips}</p> : null
        }
      </FormItem>
    )
  }
```

### 其他方法
```
  // 设置初始化值
  init(formItems) {
    this.model = formItems.reduce((acc, cur) => cur.prop ? { ...acc, [cur.prop]: cur.value || cur.initialValue || '' } : acc, {})
  }

  // 重置属性值
  resetModel() {
    this.model = this.allFormItems.reduce((acc, cur) => cur.prop ? { ...acc, [cur.prop]: cur.initialValue || '' } : acc, {})
    this.$nextTick(() => {
      // 清除校验
      this.$refs.form.clearValidate()
    })
  }

  // 更新属性值（提供外部使用）
  updateModel(prop, value) {
    this.$set(this.model, prop, value)
  }
```

## 参考
* [VUE elementUI table](https://github.com/awfifnypm/vue-element-table)