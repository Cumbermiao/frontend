# Markdown

Markdown 是一种轻量级标记语言，它允许人们使用易读易写的纯文本格式编写文档，Markdown 编写的文档后缀为 .md, .markdown。

VSCode 打开.md文件。Ctrl + Shift + V，即可预览。然后双击相应位置即可修改对应内容

## Markdown 标题

1.使用 = 和 - 标记一级和二级标题
一级标题
=======
二级标题
-------

## 2.使用 # 号标记
# 一级标题
## 二级标题

## Markdown 段落
段落的换行是使用两个以上空格加上回车

## 字体
*斜体文本*  或者  _斜体文本_
**粗体文本**  或者  __粗体文本__
***粗斜体文本***  或者  ___粗斜体文本___

## 分割线
你可以在一行中用三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西。你也可以在星号或是减号中间插入空格。下面每种写法都可以建立分隔线
***

* * *

*****

- - -

----------

## 删除线
~~删除线~~

## 下划线
<u>带下划线文本</u>

## 脚注
创建脚注格式类似这样 [^RUNOOB]。

[^RUNOOB]: 我是脚注

## Markdown 列表

Markdown 支持有序列表和无序列表

### 无序列表使用星号(*)、加号(+)或是减号(-)作为列表标记：
* 第一项
* 第二项
+ 第一项
+ 第二项
- 第一项
- 第二项

### 有序列表使用数字并加上 . 号来表示
1. 第一项
2. 第二项

### 列表嵌套
列表嵌套只需在子列表中的选项添加四个空格即可
* 第一项：
    - 第一项嵌套的第一个元素
    - 第一项嵌套的第二个元素

1. 第一项：
    1. 第二项嵌套的第一个元素
    2. 第二项嵌套的第二个元素

## Markdown 区块    
> Markdown 区块引用是在段落开头使用 > 符号
> 然后后面紧跟一个空格符号
另外区块是可以嵌套的
> 一个>最外层
> > 两个>>第一层嵌套
> > > 三个>>>第二层嵌套

### 区块中使用列表
> 1. 第一项
> 2. 第二项
> + 第一项
> + 第二项


>7.打开应用程序使用。
### 列表中使用区块
* 第一项
    > 菜鸟教程
    > 学的不仅是技术更是梦想
* 第二项

## Markdown 代码
`printf()` 函数
### 代码区块
#### 代码区块使用 4 个空格或者一个制表符（Tab 键）。
   
#### 用 ``` 包裹一段代码，并指定一种语言（也可以不指定）：
```javascript
$(document).ready(function () {
    alert('RUNOOB');
});
```
## Markdown 链接
[我是一个百度链接](http://www.baidu.com)

或者

<http://www.baidu.com>

### 高级链接
这个链接用 1 作为网址变量 [Google][1]
这个链接用 md 作为网址变量 [Runoob][md]
然后在文档的结尾为变量赋值（网址）

  [1]: http://www.baidu.com/
  [md]: http://www.baidu.com/

## Markdown 图片
开头一个感叹号 !
接着一个方括号，里面放上图片的替代文字
接着一个普通括号，里面放上图片的网址，最后还可以用引号包住并加上选择性的 'title' 属性的文字。
![RUNOOB 图标](http://static.runoob.com/images/runoob-logo.png)

![RUNOOB 图标](http://static.runoob.com/images/runoob-logo.png "RUNOOB")

当然，也可以对图片网址使用变量
这个链接用 1 作为网址变量 [RUNOOB][1].
然后在文档的结尾为变量赋值（网址）
Markdown Shortcuts
[1]: http://static.runoob.com/images/runoob-logo.png

Markdown 还没有办法指定图片的高度与宽度，如果你需要的话，你可以使用普通的 <img> 标签。
<img src="http://static.runoob.com/images/runoob-logo.png" width="50%">

## Markdown 表格
Markdown 制作表格使用 | 来分隔不同的单元格，使用 - 来分隔表头和其他行。
|  表头   | 表头  |
|  ----  | ----  |
| 单元格  | 单元格 |
| 单元格  | 单元格 |

### 对齐方式

我们可以设置表格的对齐方式：

-: 设置内容和标题栏居右对齐。
:- 设置内容和标题栏居左对齐。
:-: 设置内容和标题栏居中对齐。

|  我是表头我是表头   | 我是表头我是表头  | 我是表头我是表头  |
|  :-  | :-:  | -:  |
| 左对齐 | 居中对齐 | 右对齐 |
| 表格 | 表格 | 表格 |

## Markdown 高级技巧

### 支持的 HTML 元素
不在 Markdown 涵盖范围之内的标签，都可以直接在文档里面用 HTML 撰写

### 转义
Markdown 使用了很多特殊符号来表示特定的意义，如果需要显示特定的符号则需要使用转义字符，Markdown 使用反斜杠转义特殊字符：
**文本加粗** 
\*\* 正常显示星号 \*\*
Markdown 支持以下这些符号前面加上反斜杠来帮助插入普通的符号：
\\   反斜线
\`   反引号
\*   星号
\_   下划线
\{\}  花括号
\[\]  方括号
\(\)  小括号
\#   井字号
\+   加号
\-   减号
\.   英文句点
\!   感叹号

### 公式
当你需要在编辑器中插入数学公式时，可以使用两个美元符 $$ 包裹 TeX 或 LaTeX 格式的数学公式来实现。提交后，问答和文章页会根据需要加载 Mathjax 对数学公式进行渲染。