# CSS、Sass、Scss

## 描述

+ 指层叠样式表 (Cascading Style Sheets)
+ Sass (Syntactically Awesome StyleSheets)，是由buby语言编写的一款css预处理语言，和html一样有严格的缩进风格，和css编写规范有着很大的出入，是不使用花括号和分号的，所以不被广为接受。 Sass 是一款强化 CSS 的辅助工具，是对 CSS 的扩展，它在 CSS 语法的基础上增加了变量 (variables)、嵌套 (nested rules)、混合 (mixins)、继承(extend)、导入 (inline imports) 等高级功能，这些拓展令 CSS 更加强大与优雅。使用 Sass 以及 Sass 的样式库（如 Compass）有助于更好地组织管理样式文件，以及更高效地开发项目， 其后缀是.sass。
+ SCSS (Sassy CSS)，一款css预处理语言，SCSS 是 Sass 3 引入新的语法，其语法完全兼容 CSS3，并且继承了 Sass 的强大功能。也就是说，任何标准的 CSS3 样式表都是具有相同语义的有效的 SCSS 文件。SCSS 需要使用分号和花括号而不是换行和缩进。SCSS 对空白符号不敏感，其实就和css3语法一样，其后缀名是分别为 .scss。

## CSS、Sass、Scss三者关系

### css和sass的关系

sass是由buby语言编写的一款css预处理语言，和html一样有严格的缩进风格，和css编写规范有着很大的出入，是不使用花括号和分号的，所以不被广为接受。

### css和scss的关系

SCSS 和 CSS 写法无差别，这也是 Sass 后来越来越受大众喜欢原因之一。简单点说，把你现有的“.css”文件直接修改成“.scss”即可使用。

### sass和scss的关系

sass和scss其实是一样的css预处理语言，SCSS 是 Sass 3 引入新的语法，其后缀名是分别为 .sass和.scss两种。
SASS版本3.0之前的后缀名为.sass，而版本3.0之后的后缀名.scss。
两者是有不同的，继sass之后scss的编写规范基本和css一致，sass时代是有严格的缩进规范并且没有‘{}’和‘；’。
而scss则和css的规范是一致的。

#### scss的安装

如果使用Node.js，还可以通过运行以下命令使用npm安装Sass
`npm install -g sass`

#### scss的用法

[sass中文网](https://www.sass.hk/)

##### 1.使用变量

sass使用$符号来标识变量(老版本的sass使用!来标识变量。)

```
$highlight-color: #F90;
.selected {
  border: 1px solid $highlight-color;
}

//编译后

.selected {
  border: 1px solid #F90;
}
```

##### 2. 嵌套CSS 规则

css中重复写选择器是非常恼人的。如果要写一大串指向页面中同一块的样式时，往往需要一遍又一遍地写同一个ID,像这种情况，sass可以让你只写一遍，且使样式可读性更高。

```
#content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  aside { background-color: #EEE }
}

//编译后

#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```

###### 2.1父选择器的标识符&

```
article a {
  color: blue;
  :hover { color: red }
}

编译后

article a {
  color: blue;
  &:hover { color: red }
}
```

###### 2-2. 群组选择器的嵌套

在CSS里边，以,的形式定义群组选择器。群组选择器的规则会对群组中任何一个选择器的元素生效，当需要在某个特定的容器元素内对这样一个群组选择器进行修饰，css的写法会让你在群组选择器中的每一个选择器前都重复一遍容器元素的选择器。
sass的嵌套特性会把每一个内嵌选择器的规则都正确地解出来：

```
  .container {
    h1, h2, h3 {margin-bottom: .8em}
  }
```

翻译后

```
  .container h1, .container h2, .container h3 { margin-bottom: .8em }
```

对于内嵌在群组选择器内的嵌 套规则，处理方式也一样

###### 2-3. 子组合选择器和同层组合选择器：>、+和~

用子组合选择器>，选择紧跟着的子元素中的元素
同层相邻组合选择器+，选择紧跟的元素
同层全体组合选择器~，选择所有跟着的同层元素

###### 2-4. 嵌套属性

除了选择器，属性也可以进行嵌套
嵌套属性的规则是这样的：把属性名从中划线-的地方断开，在根属性后边添加一个冒号:，紧跟一个{ }块，把子属性部分写在这个{ }块中。

```
  nav {
    border: 1px solid #ccc {
    left: 0px;
    right: 0px;
    }
  }
```

翻译后

```
  nav {
    border: 1px solid #ccc;
    border-left: 0px;
    border-right: 0px;
  }
```

##### 3. 导入SASS文件

sass的@import规则在生成css文件时就把相关文件导入进来。这意味着所有相关的样式被归纳到了同一个css文件中，而无需发起额外的下载请求。
使用sass的@import规则并不需要指明被导入文件的全名。你可以省略.sass或.scss文件后缀

###### 3-1. 使用SASS部分文件

你通常只想生成少数几个css文件。那些专门为@import命令而编写的sass文件，并不需要生成对应的独立css文件，这样的sass文件称为局部文件。sass局部文件的文件名以下划线开头。这样，sass就不会在编译时单独编译这个文件输出css，而只把这个文件用作导入。

###### 3-2. 默认变量值

假如你写了一个可被他人通过@import导入的sass库文件，你可能希望导入者可以定制修改sass库文件中的某些值。使用sass的!default标签可以实现这个目的。它很像css属性中!important标签的对立面，不同的是!default用于变量，含义是：如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。

```
  $fancybox-width: 400px !default;
  .fancybox {
    width: $fancybox-width;
  }
```

###### 3-3. 嵌套导入

跟原生的css不同，sass允许@import命令写在css规则内。这种导入方式下，生成对应的css文件时，局部文件会被直接插入到css规则内导入它的地方。举例说明，有一个名为_blue-theme.scss的局部文件，内容如下：

```
  aside {
    background: blue;
    color: white;
  }
```

然后把它导入到一个CSS规则内，如下所示：

```
  .blue-theme {@import "blue-theme"}
```

//生成的结果跟你直接在.blue-theme选择器内写_blue-theme.scss文件的内容完全一样。

```
  .blue-theme {
    aside {
      background: blue;
      color: #fff;
    }
  }
```

##### 4. 混合器

如果你的整个网站中有几处小小的样式类似（例如一致的颜色和字体），那么使用变量来统一处理这种情况是非常不错的选择。但是当你的样式变得越来越复杂，你需要大段大段的重用样式的代码，独立的变量就没办法应付这种情况了。你可以通过sass的混合器实现大段样式的重用。
混合器使用@mixin标识符定义,这个标识符给一大段样式赋予一个名字，这样你就可以轻易地通过引用这个名字重用这段样式。下边的这段sass代码，定义了一个非常简单的混合器，目的是添加跨浏览器的圆角边框。

```
  @mixin rounded-corners {
    -moz-border-radius: 5px;
    -webkit-border-radius: 5px;
    border-radius: 5px;
  }
```

然后就可以在你的样式表中通过@include来使用这个混合器，放在你希望的任何地方。@include调用会把混合器中的所有样式提取出来放在@include被调用的地方。

```
  notice {
    background-color: green;
    border: 2px solid #00aa00;
    @include rounded-corners;
  }
```

编译后

```
  .notice {
    background-color: green;
    border: 2px solid #00aa00;
    -moz-border-radius: 5px;
    -webkit-border-radius: 5px;
    border-radius: 5px;
  }
```

实际上，混合器太好用了，一不小心你可能会过度使用。大量的重用可能会导致生成的样式表过大，导致加载缓慢。所以，首先我们将讨论混合器的使用场景，避免滥用。

###### 4-1. 给混合器传参

可以通过在@include混合器时给混合器传参，来定制混合器生成的精确样式。当@include混合器时，参数其实就是可以赋值给css属性值的变量。如果你写过JavaScript，这种方式跟JavaScript的function很像：

```
  @mixin link-colors($normal, $hover, $visited) {
    color: $normal;
    &:hover { color: $hover; }
    &:visited { color: $visited; }
  }
```

当混合器被@include时，你可以把它当作一个css函数来传参。如果你像下边这样写：

```
  a {
    @include link-colors(blue, red, green);
  }
```

编译后

```
  a { color: blue; }
  a:hover { color: red; }
  a:visited { color: green; }
```

sass允许通过语法$name: value的形式指定每个参数的值,这种形式的传参，参数顺序就不必再在乎了，只需要保证没有漏掉参数即可：

```
  a {
      @include link-colors(
        $normal: blue,
        $visited: green,
        $hover: red
    );
  }
```

##### 5. 使用选择器继承来精简CSS

使用sass的时候，最后一个减少重复的主要特性就是选择器继承.选择器继承是说一个选择器可以继承为另一个选择器定义的所有样式。这个通过@extend语法实现

```
//通过选择器继承继承样式
  .error {
    border: 1px solid red;
    background-color: #fdd;
  }
  .seriousError {
    @extend .error;
    border-width: 3px;
  }
```

.seriousError将会继承样式表中任何位置处为.error定义的所有样式
.seriousError不仅会继承.error自身的所有样式，任何跟.error有关的组合选择器样式也会被.seriousError以组合选择器的形式继承

```
  /.seriousError从.error继承样式
  .error a{  //应用到.seriousError a
    color: red;
    font-weight: 100;
  }
  h1.error { //应用到hl.seriousError
    font-size: 1.2rem;
  }
```
