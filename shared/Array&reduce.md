# js 数组方法的高级用法

## reduce

```
  // callback：回调函数(必选)
  // accumulator：累计器完成计算的返回值(必选)
  // currentValue：当前元素(必选)
  // index：当前元素的索引(可选)
  // array：当前元素所属的数组对象(可选)
  // initialValue：初始值(可选)
  arr.reduce(callback(accumulator, currentValue, [index], [array]), [initialValue])
```

### 用法

- 求和 （基础用法）
  ` const sum = (arr = []) => arr.reduce((t, v) => t + v, 0)`

- 个数统计
  `const count = (arr = []) => arr.reduce((t, v) => (t[v] = (t[v] || 0) + 1, t), {})`

- 数组分割
  ```
    const chunk = (arr = [], size = 2) => arr.reduce((t, v) => (t[t.length - 1].length === size ? t.push([v]) : t[t.length - 1].push(v), t), [[]])

    const arr = [1, 2, 3, 4, 5]
    chunk(arr, 2); // [[1, 2], [3, 4], [5]]
  ```

- 数组扁平
  `const flat = (arr = []) => arr.reduce((t, v) => Array.isArray(v.children) ? [...t, ...v.children] : v, [])`

- 数组去重
  `const uniq = (arr = []) => arr.reduce((t, v) => t.includes(v) ? t : [...t, v], [])`

- 数组成员独立拆解

  ```
    const unweave = (arr = []) => {
      return arr.reduce(
          (t, v) => (v.forEach((w, i) => t[i].push(w)), t),
          Array.from({ length: Math.max(...arr.map(v => v.length)) }).map(v => [])
      );
    }
    const arr = [["a", 1, true], ["b", 2, false]];
    unweave(arr); // [["a", "b"], [1, 2], [true, false]]
  ```

- 数组转对象

```
  const people = [
    { area: "GZ", name: "YZW", age: 27 },
    { area: "SZ", name: "TYJ", age: 25 }
  ]
  const array2object = (arr = []) => arr.reduce((t, v) => ({...t, [v.name]: v}), {})
```

- 顺序执行 promise （不通过 then 的链式调用，不使用 async，未知执行任务的情况下）
  `const queueTasks = (arr = []) => arr.reduce((all, current) => all.then(current), Promise.resolve())`

### 效率
> 代替 map、filter方法。 reduce > map > forEach > for
```
  const a = arr.map(v => v * 2)
  const b = arr.reduce((t, v) => [...t, v * 2], [])

  const c = arr.filter(v => v > 1)
  const d = arr.reduce((t, v) => v > 1 ? [...t, v] : t, [])
```

- chrome 环境下循环效率比较
```
  // 创建一个长度为100000的数组
  const list = [...new Array(100000).keys()]

  // for
  console.time("for")
  let result1 = 0
  for (let i = 0; i < list.length; i++) {
      result1 += i + 1
  }
  console.timeEnd("for");
  // for: 11.96728515625 ms

  // forEach
  console.time("forEach")
  let result2 = 0
  list.forEach(v => (result2 += v + 1))
  console.timeEnd("forEach")
  // forEach: 9.20703125 ms

  // map
  console.time("map")
  let result3 = 0
  list.map(v => (result3 += v + 1, v))
  console.timeEnd("map")
  // map: 7.30712890625 ms

  // reduce
  console.time("reduce")
  const result4 = list.reduce((t, v) => t + v + 1, 0)
  console.timeEnd("reduce")
  // reduce: 5.846923828125 ms

```

### 数组的扩展
#### 数组方法总揽
| 分类         | 方法                                                                  |
| ------------ | --------------------------------------------------------------------- |
| 修改原始数组 | push、pop、shift、unshift、reverse、splice、sort、copyWithin、forEach |
| 返回新的数组 | join、concat、slice、map、reduce、reduceRight、flat、flatMap、filter  |
| 返回布尔值 | isArray、every、some、includes  |
| 数组查找 | find、findIndex、indexOf、lastIndexOf  |
| 其他 | from、of、entries、keys、values、fill  |

#### flat
> 按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。flat() 方法会移除数组中的空项

```
  var arr1 = [1, 2, [3, 4]];
  arr1.flat(); 
  // [1, 2, 3, 4]

  var arr2 = [1, 2, [3, 4, [5, 6]]];
  arr2.flat();
  // [1, 2, 3, 4, [5, 6]]

  var arr3 = [1, 2, [3, 4, [5, 6]]];
  arr3.flat(2);
  // [1, 2, 3, 4, 5, 6]

  //使用 Infinity，可展开任意深度的嵌套数组
  var arr4 = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]]];
  arr4.flat(Infinity);
  // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

#### flatMap
> 对数组的每个成员执行一个函数，相当于执行Array.prototype.map(),然后对返回值组成的数组执行flat()方法。flatMap只能展开一层数组

- 在一个 map() 期间增加或去除一些项 (mdn)
```
  let a = [5, 4, -3, 20, 17, -33, -4, 18]
  a.flatMap( (n) => (n < 0) ? [] : (n % 2 == 0) ? [n] : [n-1, 1] )
  // expected output: [4, 1, 4, 20, 16, 1, 18]
```

### question
> 千分位最简便的实现？