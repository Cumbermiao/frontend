# 新增/编辑自助报表代码优化

## 所做工作
1. 拆分组件
2. 代码优化

### 拆分组件
1. 将指标、维度、过滤器封装成一个组件（CheckContent）
   * 增加具名插槽，解决维度中日期的下拉框问题
   * 使用`$emit`将checkbox选中的值传回父组件，切换场景、重置时，需要在父组件中进行`this.$refs.targetCheck.checkedList = []`，将数据清空。
   * 值得注意的是，因为维度checkbox改变时，过滤器需要作出相应变化，所以在`latitudeChange()`方法中，不仅需要将`add.vue`中`this.selectList`进行修改，还需要执行`this.$refs.filterCheck.checkedList = this.selectList`，实现对组件中值的修改。
2. 将表格上方筛选条件进行封装（SelectContent）
    * 将日期，select选中的值等相关方法从add.vue中分离，提高可读性
    * checkbox中过滤器中riqi和其他值发生变化时，组件不会再触发`mounted`钩子，所以需要通过监听触发`riqiSelectedChange()`、`selectDataChange()`方法，目的是为了修改日期组件初始值、获取对应下拉框中的值。
   

### 代码优化
1. 减少不必要变量使用  
   * 去除rightSelect、haveSelectRight，从selectData去除所需变量
   * toSave方法中，将param提前，减少重复定义，简化代码
2. 修改循环时直接对data中变量的直接操作，减少试图不必要更新
  ```  
    this.isDisabled.forEach((item, index) => {
      this.$set(this.isDisabled, index, true)
    })
  ```
  优化如下：
  ```
    const isDisabledRes = []
    this.isDisabled.forEach((item, index) => {
      isDisabledRes.push(true)
    })
    this.isDisabled = isDisabledRes
  ```
3.  使用includes减少逻辑嵌套
```
  const chartLatitudeArr = []
  const latitude = []
  Object.assign(latitude, this.latitudeList)
  latitude.forEach(item => {
    const findValue = this.latitudeListArr.find((value) => {
      return value.code === item
    })
    chartLatitudeArr.push(findValue)
  })
```
优化如下：
```
  const latitude = Object.assign([], this.latitudeList)
  const chartLatitudeArr = this.latitudeListArr.filter(item => {
    return latitude.includes(item.code)
  })
```

## 疑问与待改进
1. then里面去除对code的判断，并进行结构赋值
```
  dataTableList(param1).then(res => {
    if (res.code === 999999) {
      if (res.data && res.data.table && Array.isArray(res.data.table)) {
        this.tableData = res.data.table
      }
    } else {
      console.warn(res.data)
    }
  }).catch(err => console.log(err))
```
优化如下：
```
  dataTableList(param1).then(({ data: { table = [] }}) => {
    if (table.length > 0) {
      this.tableData = table
    }
  }).catch(err => console.log(err))
```
2. 代码优化合并
```
  <el-date-picker
    v-if="dateType === '日'"
    v-model="pickerTime"
    type="daterange"
    range-separator="至"
    start-placeholder="开始日期"
    end-placeholder="结束日期"
    value-format="yyyy-MM-dd"
    @change="picker"
  />
  <el-date-picker
    v-if="dateType === '月'"
    v-model="pickerTime"
    type="monthrange"
    range-separator="至"
    start-placeholder="开始月份"
    end-placeholder="结束月份"
    value-format="yyyy-MM-dd"
    @change="picker"
  />
```
优化如下：
```
  <el-date-picker
    v-model="pickerTime"
    :type="dateType === '日' ? 'daterange' : 'monthrange' "
    range-separator="至"
    :start-placeholder="dateType === '日' ? '开始日期' : '开始月份'"
    :end-placeholder="dateType === '日' ? '结束日期' : '结束月份'"
    value-format="yyyy-MM-dd"
    @change="picker"
  />
```
3. 
```
  const param1 = JSON.parse(JSON.stringify(param))
  param1['id'] = this.reportId       
```
优化如下：
```
  const param1 = {
    ...param,
    id:this.reportId
  }
```
4. 子组件checkCOntent数据