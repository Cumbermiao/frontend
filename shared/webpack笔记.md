[参考文件](https://github.com/TigerHee/shareJS/blob/master/webpack4.md)
## 安装前先npm初始化

```
npm init -y
npm i webpack webpack-cli -D
```


```js
let path = require('path')   // 相对路径变绝对路径
let HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
  mode: 'production', // 模式 默认 production development
  entry: './src/index',    // 入口
  output: {
    filename: 'bundle.[hash:8].js',   // hash: 8只显示8位
    path: path.resolve(__dirname, 'dist'),
    publicPath: ''  // // 给所有打包文件引入时加前缀，包括css，js，img，如果只想处理图片可以单独在url-loader配置中加publicPath
  },
 plugins: [new HtmlWebpackPlugin()]

}
```
## 本地服务

`npm i webpack-dev-server -D`

```
devServer: {
  port: 3000,
  progress: true          // 滚动条
  contentBase: './build'  // 起服务的地址
  open: true              // 自动打开浏览器
  compress： true         // gzip压缩
}
```


## 复制html

`npm i html-webpack-plugin -D`

```
let HtmlWebpackPlugin = require('html-webpack-plugin')
plugins: [ // 放着所有webpack插件
  new HtmlWebpackPlugin({ // 用于使用模板打包时生成index.html文件，并且在run dev时会将模板文件也打包到内存中
    template: './index.html', // 模板文件
    filename: 'index.html', // 打包后生成文件
    hash: true, // 添加hash值解决缓存问题
    minify: { // 对打包的html模板进行压缩
      removeAttributeQuotes: true, // 删除属性双引号
      collapseWhitespace: true // 折叠空行变成一行
    }
  })
]

```

[html-webpack-plugin#options](https://github.com/jantimon/html-webpack-plugin#options)

## 处理css

`npm i css-loader style-loader -D`

```
// css-loader   作用：用来解析@import这种语法
// style-loader 作用：把 css 插入到head标签中
// loader的执行顺序： 默认是从右向左（从下向上）
module: {    // 模块
  rules: [   // 规则
    // style-loader 把css插入head标签中
    // loader 功能单一
    // 多个loader 需要 []
    // 顺便默认从右到左
    // 也可以写成对象方式
    {
      test: /\.css$/,   // css 处理
      // use: 'css-loader'
      // use: ['style-loader', 'css-loader'],
      use: [
        // {
        //     loader: 'style-loader',
        //     options: {
        //         insertAt: 'top' // 将css标签插入最顶头  这样可以自定义style不被覆盖
        //     }
        // },
        MiniCssExtractPlugin.loader,
        'css-loader', // css-loader 用来解析@import这种语法,
        'postcss-loader'
      ]
    }
  ]
}
```


## 处理less

`npm i less-loader`

```
{
  test: /\.less$/,   // less 处理
  // use: 'css-loader'
  // use: ['style-loader', 'css-loader'],
  use: [
    MiniCssExtractPlugin.loader, 
    // 这样相当于抽离成一个css文件， 如果希望抽离成分别不同的css, 需要再引入MiniCssExtractPlugin，再配置
    'css-loader', // css-loader 用来解析@import这种语法
    'postcss-loader',
    'less-loader' // less-loader less -> css
    // sass node-sass sass-loader
    // stylus stylus-loader
  ]
}
```

[less-loader](https://webpack.js.org/loaders/less-loader/#src/components/Sidebar/Sidbar.jsx)

## 抽离css文件，通过link引入

`yarn add mini-css-extract-plugin -D`

[mini-css-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin)

```
let MiniCssExtractPlugin = require('mini-css-extract-plugin')

// 压缩css

plugins: [
  new MiniCssExtractPlugin({
      filename: 'css/main.css'
  })
]

{
  test: /\.css$/,   // css 处理
  use: [
    // 此时不需要style-loader
    MiniCssExtractPlugin.loader,   // 抽离
    'css-loader', // css-loader 用来解析@import这种语法,
    'postcss-loader'
  ]
}

```

抽离css插件文件时可使用`optimize-css-assets-webpack-plugin`优化压缩css以及js文件

## 压缩css和js

```
// 用了`mini-css-extract-plugin`抽离css为link需使用`optimize-css-assets-webpack-plugin`进行压缩css,使用此方法压缩了css需要`uglifyjs-webpack-plugin`压缩js
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin")
const UglifyJsPlugin = require("uglifyjs-webpack-plugin")

module.exports = {
  optimization: {              // 优化项
    minimizer: [
      new UglifyJsPlugin({     // 优化js
        cache: true,           // 是否缓存
        parallel: true,        // 是否并发打包
        // sourceMap: true     // 源码映射 set to true if you want JS source maps
      }),
      new OptimizeCSSAssetsPlugin({})    // css 的优化
    ]
  },
  mode: 'production',
  entry: '',
  output: {},
}

```

## 给css加上兼容浏览器的前缀

`yarn add postcss-loader autoprefixer -D`

```
// css
{
  test: /\.css$/,   // css 处理
  // use: 'css-loader'
  // use: ['style-loader', 'css-loader'],
  use: [
    // {
    //     loader: 'style-loader',
    //     options: {
    //         insertAt: 'top' // 将css标签插入最顶头  这样可以自定义style不被覆盖
    //     }
    // },
    MiniCssExtractPlugin.loader,
    'css-loader', // css-loader 用来解析@import这种语法,
    'postcss-loader'
  ]
}
// less
{
  test: /\.less$/,   // less 处理
  // use: 'css-loader'
  // use: ['style-loader', 'css-loader'],
  use: [
    // {
    //     loader: 'style-loader',
    //     options: {
    //         insertAt: 'top' // 将css标签插入最顶头  这样可以自定义style不被覆盖
    //     }
    // },
    MiniCssExtractPlugin.loader,   // 这样相当于抽离成一个css文件， 如果希望抽离成分别不同的css, 需要再引入MiniCssExtractPlugin，再配置
    'css-loader', // css-loader 用来解析@import这种语法
    'postcss-loader',
    'less-loader' // less-loader less -> css
    // sass node-sass sass-loader
    // stylus stylus-loader
  ]
},
```

postcss 需要配置文档   `postcss.config1.js`

[postcss-loader](https://github.com/postcss/postcss-loader)

```
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
```

## es6 转 es5

`npm i babel-loader @babel/core  @babel/preset-env -D`

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [ //预设
              '@babel/preset-env' 
            ],
            plugins:[
              // 转es7的语法
              ["@babel/plugin-proposal-decorators", { "legacy": true }],
              ["@babel/plugin-proposal-class-properties", { "loose" : true }]
            ]
          }
        },
        exclude: /node_modules/
      }
    ]
  }
}

```


## 转es7的语法

```
// 转class
npm i @babel/plugin-proposal-class-properties -D

// 转装饰器
npm i @babel/plugin-proposal-decorators -D
```

配置如上

### 其他不兼容的高级语法

```
使用 @babel/polyfill
```

## 语法检查 eslint

`npm i eslint eslint-loader -S`

根目录添加 `.eslintrc.json` 配置文件

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'eslint-loader',
          options: {
            enforce: 'pre'  // previous优先执行  post-普通loader之后执行
          }
        }
      },
      {
        test: /\.js$/,      // mormal 普通的loader
        use: {
          loader: 'babel-loader',
          options: {
            presets: [ //预设
              '@babel/preset-env' 
            ]
          }
        },
        exclude: /node_modules/
      }
    ]
  }
}

```

## 全局变量引入

jquery的引入

```
npm i jquery -S
```


1. 暴露全局

`npm i expose-loader -D` 暴露全局的`loader`

#### 法1：

可以在js中 `import $ from 'expose-loader?$!jquery'`   // 全局暴露jquery为$符号

可以调用`window.$`

#### 法2：

也可在`webpack.config.js` 中配置 `rules`

```
module.exports = {
  module: {
    rules: [
      {
        test: require.resolve('jquery'),
        use: 'expose-loader?$'
      }
    ]
  }
}

```
以后在`.js`文件中引入

```
import $ from 'jquery'
```

#### 法3. 如何在每个模块中注入：

```
let webpack = require('webpack')

module.exports = {
  plugins: [
    new webpack.ProvidePlugin({
      $: 'jquery'
    })
  ]
}

之后代码内直接使用 $
```
#### 法4：

在`index.html`中通过`script`标签引入`jquery`, 但是在`js`中，用`import`会重新打包`jquery`,如何避免

从输出的bundle 中排除依赖

```
module.exports = {
  externals: { // 告知webpack是外部引入的，不需要打包
    jquery: 'jQuery'
  }
}

```

此时在index.js上

```
import $ from 'jquery'

console.log($)
```

## webpack图片打包

1. js中创建
2. css中引入
3. `<img src="">`

`yarn add file-loader -D`

适合一二情况

```
module.export={
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: 'file-loader'
      }
    ]
  }
}

```

默认会内部生成一张图片到build,生成图片的路径返回回来

第一种情况: 图片地址要`import`引入，直接写图片的地址，会默认为字符串

```
import logo from './logo.png'

let image = new Image()
image.src = logo
document.body.appendChild(image)
```

第二种情况: `css-loader`会将`css`里面的图片转为`require`的格式

```
div {
  background: url("./logo.png");
}
```

第三种情况: 解析`html`中的`image`

`yarn add html-withimg-loader -D`

```
{
  test: /\.html$/,
  use: 'html-withimg-loader'
}
```

## 当图片小于多少，用base64

`yarn add url-loader -D`

如果过大，才用`file-loader`

```
{
  test: /\.(png|jpg|gif)$/,
  // 当图片小于多少，用base64,否则用file-loader产生真实的图片
  use: {
    loader: 'url-loader',
    options: {
      limit: 200 * 1024,          // 小于200k变成base64
      // outputPath: '/img/',     // 打包后输出地址
      // publicPath: ''           // 给资源加上域名路径
    }
  }
}
```

## 打包文件分类

1.图片:

```
{
  test: /\.(png|jpg|gif)$/,
  // 当图片小于多少，用base64,否则用file-loader产生真实的图片
  use: {
    loader: 'url-loader',
    options: {
      limit: 1,  // 200k 200 * 1024
      outputPath: 'img/'   // 打包后输出地址 在dist/img
    }
  }
},
```

2.css:

```
plugins: [
  new MiniCssExtractPlugin({
    filename: 'css/main.css'
  }),
]
```

## 希望输出的时候，给这些`css\img`加上前缀，传到服务器也能访问

```
output: {
  filename: 'bundle.[hash:8].js',   // hash: 8只显示8位
  path: path.resolve(__dirname, 'dist'),
  publicPath: 'http://www.mayufo.cn'  // 给静态资源统一加
},
```


## 如果只希望处理图片

```
{
  test: /\.(png|jpg|gif)$/,
  // 当图片小于多少，用base64,否则用file-loader产生真实的图片
  use: {
    loader: 'url-loader',
    options: {
      limit: 1,  // 200k 200 * 1024
      outputPath: '/img/',   // 打包后输出地址
      publicPath: 'http://www.mayufo.cn'
    }
  }
}
```

## 打包多页应用

```
// 多入口
let path = require('path')
let HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  mode: 'development',
  entry: {
    home: './src/index.js',
    other: './src/other.js'
  },
  output: {
    filename: "[name].js",
    path: path.resolve(__dirname, 'dist2')
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './index.html',
      filename: 'home.html',
      chunks: ['home']
    }),
    new HtmlWebpackPlugin({
      template: './index.html',
      filename: 'other.html',
      chunks: ['other', 'home']   // other.html 里面有 other.js & home.js
    }),
  ]
}

```

## 配置`source-map`

`yarn add @babel/core  @babel/preset-env babel-loader  webpack-dev-server -D`

```
module.exports = {
  devtool: 'source-map' // 增加映射文件调试源代码
}
```
1. 源码映射 会标识错误的代码 打包后生成独立的文件 大而全 「source-map」
2. 不会陈胜单独的文件 但是可以显示行和列  「eval-source-map」
3. 不会产生列有行，产生单独的映射文件  「cheap-module-source-map」
4. 不会产生文件 集成在打包后的文件中 不会产生列有行 「cheap-module-eval-source-map」


## `watch` 改完代表重新打包实体

```
module.exports = {
  watch: true,
  watchOptions: {
    poll: 1000,              // 每秒监听1000次
    aggregateTimeout: 300,   // 防抖，当第一个文件更改，会在重新构建前增加延迟
    ignored: /node_modules/  // 对于某些系统，监听大量文件系统会导致大量的 CPU 或内存占用。这个选项可以排除一些巨大的文件夹，
  },
}
```


## `webpack`的其他三个小插件

1. `cleanWebpackPlugin`

每次打包之前删掉dist目录
`yarn add clean-webpack-plugin -D`

[clean-webpack-plugin](https://github.com/johnagan/clean-webpack-plugin)

```
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  output: {
    path: path.resolve(process.cwd(), 'dist'),
  },
  plugins: [
    new CleanWebpackPlugin('./dist')
  ]
}
```

2. `copyWebpackPlugin`

一些静态资源也希望拷贝的dist中

`yarn add copy-webpack-plugin -D`

```
const CopyWebpackPlugin = require('copy-webpack-plugin')

module.exports = {
  plugins: [
    new CopyWebpackPlugin([
      {from: 'doc', to: './dist'}
    ])
  ]
}
```

3. `bannerPlugin`内置模块

版权声明

```
const webpack = require('webpack');

new webpack.BannerPlugin('hello world')
// or
new webpack.BannerPlugin({ banner: 'hello world'})

```

## `webpack` 跨域

设置一个服务,由于`webpack-dev-server`内含`express`

[express](https://expressjs.com/zh-cn/starter/hello-world.html)

`server.js`

```
// express

let express = require('express')

let app = express();

app.get('/api/user', (res) => {
  res.json({name: 'mayufo'})
})

app.listen(3000)   // 服务端口在3000
```

写完后记得`node server.js`

访问 `http://localhost:3000/api/user` 可见内容


`index.js`

```
// 发送一个请求
let xhr = new XMLHttpRequest();

// 默认访问 http://localhost:8080  webpack-dev-server 的服务 再转发给3000
xhr.open('GET', '/api/user', true);

xhr.onload = function () {
  console.log(xhr.response)
}

xhr.send();

```


`webpack.config.js`

```
module.exports = {
  devServer: {
    proxy: {
      '/api': 'http://localhost:3000'
    }
  },
}
```

## 1.如果后端给的请求没有API 「跨域」

```
// express

let express = require('express')

let app = express();


app.get('/user', (res) => {
  res.json({name: 'mayufo'})
})

app.listen(3000)   // 服务端口在3000
```


请求已api开头, 转发的时候再删掉api

```
devServer: {
  proxy: {
    '/api': {
      target: 'http://localhost:3000',
      pathRewrite: {'^/api': ''}
    }
  }
}
```

## 2.前端只想单纯mock数据 「跨域」

```
devServer: {
  // proxy: {
  //     '/api': 'http://localhost:3000' // 配置一个代理
  // }
  //   proxy: {   // 重写方式 把请求代理到express 上
  //       '/api': {
  //           target: 'http://localhost:3000',
  //           pathRewrite: {'^/api': ''}
  //       }
  //   }
  before: function (app) {  // 勾子
    app.get('/api/user', (req, res) => {
      res.json({name: 'tigerHee'})
    })
  }
},
```

## 3.有服务端，不用代理, 服务端启动webpack 「跨域」

`server.js`中启动`webpack`

`yarn add webpack-dev-middleware -D`

`server.js`

```
// express

let express = require('express')
let webpack = require('webpack')
let app = express();


// 中间件
let middle = require('webpack-dev-middleware')

let config = require('./webpack.config')


let compiler = webpack(config)


app.use(middle(compiler))

app.get('/user', (req, res) => {
  res.json({name: 'mayufo'})
})


app.listen(3000)

```

## webpack解析resolve

以`bootstrap`为例

```
npm install bootstrap  -D
```

`index.js`

```
import 'bootstrap/dist/css/bootstrap.css'
```

报错
```
ERROR in ./node_modules/bootstrap/dist/css/bootstrap.css 7:0
Module parse failed: Unexpected token (7:0)
You may need an appropriate loader to handle this file type.
|  * Licensed under MIT (https://github.com/twbs/bootstrap/blob/master/LICENSE)
|  */
> :root {
|   --blue: #007bff;
|   --indigo: #6610f2;
 @ ./src/index.js 22:0-42
 @ multi (webpack)-dev-server/client?http://localhost:8081 ./src/index.js

```

这是因为`bootstrap` 4.0的css引入了新的特性，CSS Variables

安装
`npm install postcss-custom-properties --save-dev`


配置`webpack.config.js`
```
{
  test: /\.css$/,
  use: ['style-loader', 'css-loader', {
    loader: 'postcss-loader',
    options: {
      plugins: (loader) => [
        require("postcss-custom-properties")
      ]
    }
  }]
}
```

## 但是每次引入都很长，如何优雅引入

```
resolve: {
  // 在当前目录查找
  modules: [path.resolve('node_modules')],
  alias: {
      'bootstrapCss': 'bootstrap/dist/css/bootstrap.css'
  }
},
```

```
import 'bootstrapCss'  // 在node_modules查找
```

## 省略扩展名

extensions:

```
resolve: {
  // 在当前目录查找
  modules: [path.resolve('node_modules')],
  // alias: {
  //   'bootstrapCss': 'bootstrap/dist/css/bootstrap.css'
  // },
  mainFields: ['style', 'main'],   // 先用bootstrap中在package中的style,没有在用main
  // mainFiles: []  // 入口文件的名字 默认index
  extensions: ['.js', '.css', '.json']  // 当没有拓展命的时候，先默认js、次之css、再次之json
},
```

## 定义环境变量

`DefinePlugin` 允许创建一个在编译时可以配置的全局常量。这可能会对开发模式和生产模式的构建允许不同的行为非常有用。

```
let url = ''
if (DEV === 'dev') {
  // 开发环境
  url = 'http://localhost:3000'
} else {
  // 生成环境
  url = 'http://www.mayufo.cn'
}
```

`webpack.config.js`

```
new webpack.DefinePlugin({
  // DEV: '"production"',
  DEV: JSON.stringify('production'),
  FLAG: 'true',   // 布尔
  EXPRESSION: '1 + 1'   // 字符串 如果希望是字符串 JSON.stringify('1 + 1')
})
```

## 区分两个不同的环境

分别配置不同的环境

- `webpack.base4.js`   基础配置
- `webpack.dev4.js`    开发环境
- `webpack.prod4.js`   生产环境

`yarn add webpack-merge -D`


`npm run build -- -- config webpack.dev4.js`
`npm run build -- -- config webpack.build.js`

[官方文档](https://webpack.docschina.org/guides/production/)


`webpack.base4.js`

```
let path = require('path')
let HtmlWebpackPlugin = require('html-webpack-plugin')
let CleanWebpackPlugin = require('clean-webpack-plugin')

module.exports = {
  entry: {
    home: './src/index.js'
  },
  output: {
    filename: "[name].js",
    path: path.resolve(process.cwd(), 'dist3')
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [
              '@babel/preset-env'
            ]
          }
        }
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader', {
          loader: 'postcss-loader',
          options: {
            plugins: (loader) => [
              require("postcss-custom-properties")
            ]
          }
        }]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
      filename: 'index.html'
    })
  ]
}

```

`webpack.dev4.js`

```
let merge = require('webpack-merge')
let base = require('./webpack.base4.js')

module.exports = merge(base, {
  mode: 'development',
  devServer: {},
  devtool: 'source-map'
})

```

`webpack.prod4.js`

```
let merge = require('webpack-merge')
let base = require('./webpack.base4.js')

module.exports = merge(base, {
  mode: 'production'
})

```

`package.json`

```
"scripts": {
  "build": "webpack  --config webpack.prod4.js",
  "dev": "webpack-dev-server --config webpack.dev4.js"
},
```


## webpack 优化

`yarn add webpack webpack-cli html-webpack-plugin @babel/core babel-loader @babel/preset-env @babel/preset-react -D`

`webpack.config.js`

```
let path = require('path')
let HtmlWebpackPlugin = require('html-webpack-plugin')


module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [
              '@babel/preset-env',
              '@babel/preset-react'
            ]
          }
        }
      },
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
      filename: 'index.html'
    }),
  ]
}

```
##  优化：当某些包是独立的个体没有依赖

以jquery为例，`yarn add jquery -D`,它是一个独立的包没有依赖，可以在webpack配置中，配置它不再查找依赖

```
module: {
    noParse: /jquery/, // 不用解析某些包的依赖
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [
              '@babel/preset-env',
              '@babel/preset-react'
            ]
          }
        }
      },
  ]
}

```
运行`npx webpack`

从2057ms  -> 1946 ms

## 优化：规则匹配设置范围

```
rules: [
  {
    test: /\.js$/,
    exclude: '/node_modules/',   // 排除
    include: path.resolve('src'),  // 在这个范围内
    use: {
      loader: 'babel-loader',
      options: {
        presets: [
          '@babel/preset-env',
          '@babel/preset-react'
        ]
      }
    }
  }
```

尽量实用`include`,不使用`exclude`,使用绝对路径

## 优化：忽略依赖中不必要的语言包
`yarn add moment webpack-dev-server -D`

忽略掉`moment`的其他语言包

```
let webpack = require('webpack')

plugins: [
    new webpack.IgnorePlugin(/\.\/locale/, /moment/)
]

```

`index.js`

```
import moment from 'moment'

let r = moment().endOf('day').fromNow()  // 距离现在多少天
console.log(r);
```


从 1.2MB 到  800kb

## 动态链接库

`yarn add react react-dom`

正常使用

`webpack.config.js`

```
{
  test: /\.js$/,
  exclude: '/node_modules/',
  include: path.resolve('src'),
  use: {
    loader: 'babel-loader',
    options: {
      presets: [
        '@babel/preset-env',
        '@babel/preset-react'
      ]
    }
  }
}
```

`index.js`
```
import React from 'react'

import {render} from 'react-dom'


render(<h1>111111</h1>, window.root)
```

`index.html`

```
<div id="root"></div>
```

独立的将`react react-dom` 打包好, 打包好再引用，从而减少`webpack`每次都要打包`react`

创建`webpack.config.react.js`


```
let path = require('path')
let webpack = require('webpack')
module.exports = {
  mode: 'development',
  entry: {
    // test: './src/test.js'
    react: ['react', 'react-dom']
  },
  output: {
    filename: '_dll_[name].js',  // 产生的文件名
    path: path.resolve(__dirname, 'dist'),
    library: '_dll_[name]',     // 给输出的结果加个名字
    // libraryTarget: 'var'   // 配置如何暴露 library
    // commonjs 结果放在export属性上， umd统一资源模块, 默认是var
  },
  plugins: [
    new webpack.DllPlugin({
      name: '_dll_[name]',   // name === library
      path: path.resolve(__dirname, 'dist', 'manifest.json')  // manifest.json 定义了各个模块的路径
    })
  ]
}
```

[libraryTarget](https://webpack.docschina.org/configuration/output/#%E6%9A%B4%E9%9C%B2%E4%B8%BA%E4%B8%80%E4%B8%AA%E5%8F%98%E9%87%8F)

`manifest.json`就是一个任务清单or动态链接库，在这个清单里面查找react

`npx webpack --config webpack.config.react.js`

在`index.html`增加引用

```
<body>
<div id="root"></div>
<script src="/_dll_react.js"></script>
</body>
```

在webpack.config.js 中配置，现在动态链接库`manifest.json`中查找,如果没有再打包react
```
plugins: [
  new webpack.DllReferencePlugin({
    manifest: path.resolve(__dirname, 'dist', 'manifest.json')
  })
]

```

[DLLPlugin 和 DLLReferencePlugin](https://webpack.docschina.org/plugins/dll-plugin/#src/components/Sidebar/Sidebar.jsx)

`npm run build`

打包后的`bunle.js`文件变小

`npm run dev`

可以理解为先把react打包，后面每次都直接使用react打包后的结果

## 多线程打包`happypack`

`yarn add happypack`

`webpack.config.js`

```
let Happypack = require('happypack')


rules: [
  {
    test: /\.js$/,
    exclude: '/node_modules/',
    include: path.resolve('src'),
    use: 'happypack/loader?id=js'
  },
]

plugins: [
  new Happypack({
    id: 'js',
    use: [{
      loader: 'babel-loader',
      options: {
        presets: [
          '@babel/preset-env',
          '@babel/preset-react'
        ]
      }
    }]
  })
]
```

js启用多线程，由于启用多线程也会浪费时间，因此当项目比较大的时候启用效果更好

css启用多线程
```

{
  test: /\.css$/,
  use: 'happypack/loader?id=css'
}

new Happypack({
  id: 'css',
  use: ['style-loader', 'css-loader']
}),
```

## webpack 自带的优化

`test.js`

```
let sum = (a, b) => {
  return a + b + 'sum'
}

let minus = (a, b) => {
  return a - b + 'minus';
}

export default {
  sum, minus
}
```

1. 使用import 

`index.js`

```
import calc from './test'

console.log(calc.sum(1, 2));
```


import在生产环境下会自动去除没有用的代码`minus`，这叫`tree-shaking`，将没有用的代码自动删除掉


`index.js`

```
let calc = require('./test')
console.log(calc);   // es 6导出，是一个default的对象
console.log(calc.default.sum(1, 2));
```

require引入es6 模块会把结果放在default上,打包build后并不会把多余`minus`代码删除掉，不支持`tree-shaking`


2. 作用域的提升

`index.js`

```
let a = 1
let b = 2
let c = 3
let d = a + b + c

console.log(d, '---------');
```
打包出来的文件

```
console.log(r.default.sum(1,2));console.log(6,"---------")
```

在webpack中可以省略一些可以简化的代码

## 抽取公共代码

1. 抽离自有模块

`webpack.config.js`

```
module.exports = {
  optimization: {
    splitChunks: {             // 分割代码块，针对多入口
      cacheGroups: {           // 缓存组
        common: {              // 公共模块
          minSize: 0,          // 大于多少抽离
          minChunks: 2,        // 使用多少次以上抽离抽离
          chunks: 'initial'    // 从什么地方开始, 从入口开始
        }
      }
    }
  },
}
```
[SplitChunksPlugin](https://webpack.docschina.org/plugins/split-chunks-plugin/)


分别有a.js和b.js, index.js和other.js分别引入a和b两个js

`index.js`

```
import './a'
import './b'

console.log('index.js');
```

`other.js`

```
import './a'
import './b'

console.log('other.js');
```

`webpack.config.js`

```
module.exports = {
  optimization: {
    splitChunks: {             // 分割代码块，针对多入口
      cacheGroups: {           // 缓存组
        common: {              // 公共模块
          minSize: 0,          // 大于多少抽离
          minChunks: 2,        // 使用多少次以上抽离抽离
          chunks: 'initial'    // 从什么地方开始, 从入口开始
        }
      }
    }
  },
}
```

2. 抽离第三方模块

比如jquery

`index.js` 和 `other.js`分别引入

```
import $ from 'jquery'

console.log($);
```

修改`webpack.config.js`配置：

```
optimization: {
  splitChunks: {              // 分割代码块，针对多入口
    cacheGroups: {            // 缓存组
      common: {               // 公共模块
        minSize: 0,           // 大于多少抽离
        minChunks: 2,         // 使用多少次以上抽离抽离
        chunks: 'initial'     // 从什么地方开始,刚开始
      },
      vendor: {
        priority: 1,          // 增加权重, (先抽离第三方)
        test: /node_modules/, // 把此目录下的抽离
        minSize: 0,           // 大于多少抽离
        minChunks: 2,         // 使用多少次以上抽离抽离
        chunks: 'initial'     // 从什么地方开始,刚开始
      }
    }
  },
},
```

## 懒加载(延迟加载)

`yarn add @babel/plugin-syntax-dynamic-import  -D`

`source.js`

```
export default 'mayufo'
```

`index.js`

```
let button = document.createElement('button')
button.innerHTML = 'hello'
button.addEventListener('click', function () {
  console.log('click')
  // es6草案中的语法，jsonp实现动态加载文件
  import('./source.js').then(data => {
    console.log(data.default)
  })
})
document.body.appendChild(button)

```

`webpack.config.js`

```
{
  test: /\.js$/,
  exclude: '/node_modules/',
  include: path.resolve('src'),
  use: [{
    loader: 'babel-loader',
    options: {
      presets: [
        '@babel/preset-env',
        '@babel/preset-react'
      ],
      plugins: [
        '@babel/plugin-syntax-dynamic-import'
      ]
    }
  }]
}
```

## 热更新(当页面改变只更新改变的部分，不重新打包)

`webpack.config.js`

```
plugins: [
  new HtmlWebpackPlugin({
    template: './src/index.html',
    filename: 'index.html'
  }),
  new webpack.NameModulesPlugin(),          // 打印更新的模块路径
  new webpack.HotModuleReplacementPlugin()  // 热更新插件
]
```

`index.js`

```

import str from './source'

console.log(str);

if (module.hot) {
  module.hot.accept('./source', () => {
    console.log('文件更新了');
    require('./source')
    console.log(str);
  })
}

```

## tapable介绍 - SyncHook

[tapable](https://juejin.im/post/5abf33f16fb9a028e46ec352)

`webpack`本质上是一种事件流的机制，它的工作流程就是将各个插件串联起来，而实现这一切的核心就是`Tapable`，`webpack`中最核心的负责编译的`Compiler`和负责创建`bundles`的`Compilation`都是`Tapable`的实例。

`SyncHook` 不关心监听函数的返回值

`yarn add tabable`

`1.use.js`

```
let {SyncHook} = require('tapable')   // 结构同步勾子


class Lesson {
  constructor () {
    this.hooks = {
      // 订阅勾子
      arch: new SyncHook(['name']),
    }
  }
  start () {
    this.hooks.arch.call('may')
  }
  tap () {   //  注册监听函数
    this.hooks.arch.tap('node', function (name) {
      console.log('node', name)
    })
    this.hooks.arch.tap('react', function (name) {
      console.log('react', name)
    })
  }
}


let l = new Lesson()

l.tap();  //注册两个函数
l.start() // 启动勾子

```

`1.theory.js`

```
class SyncHook {  // 勾子是同步的
  constructor(args) {  // args => ['name']
    this.tasks = []
  }
  tap (name, task) {
    this.tasks.push(task)
  }
  call (...args) {
    this.tasks.forEach((task) => task(...args))
  }
}

let hook = new SyncHook(['name'])

hook.tap('react', function (name) {
  console.log('react', name);
})


hook.tap('node', function (name) {
  console.log('node', name);
})


hook.call('jw')
```


## tapable介绍 - SyncBailHook

`SyncBailHook`为勾子加了个保险，当`return`返回不是`undefine`就会停止

`2.use.js`

```
let {SyncBailHook} = require('tapable')   // 解构同步勾子

class Lesson {
  constructor () {
    this.hooks = {
      // 订阅勾子
      arch: new SyncBailHook(['name']),

    }
  }
  start () {
    // 发布
    this.hooks.arch.call('may')
  }
  tap () {   //  注册监听函数,订阅
    this.hooks.arch.tap('node', function (name) {
      console.log('node', name)
      return '停止学习'  // 会停止
      // return undefined
    })
    this.hooks.arch.tap('react', function (name) {
      console.log('react', name)
    })
  }
}


let l = new Lesson()

l.tap();  //注册两个函数
l.start() // 启动勾子

```

`2.theory.js`

```
class SyncBailHook {  // 勾子是同步的
    constructor(args) {  // args => ['name']
        this.tasks = []
    }
    tap (name, task) {
        this.tasks.push(task)
    }
    call (...args) {
        let ret;   // 当前函数的返回值
        let index = 0; // 当前要执行的第一个
        do {
            ret = this.tasks[index](...args)
        } while (ret === undefined  && index < this.tasks.length)
    }
}

let hook = new SyncBailHook(['name'])

hook.tap('react', function (name) {
    console.log('react', name);
    return '停止学习'
    // return undefined
})


hook.tap('node', function (name) {
    console.log('node', name);
})


hook.call('jw')

```

## tapable介绍 - SyncWaterfallHook

`SyncWaterfallHook`上一个监听函数的返回值可以传给下一个监听函数
 
`3.use.js`
 
```
let {SyncWaterfallHook} = require('tapable')   // 解构同步勾子

// waterfall 瀑布

class Lesson {
    constructor () {
        this.hooks = {
            // 订阅勾子
            arch: new SyncWaterfallHook(['name']),

        }
    }
    start () {
        // 发布
        this.hooks.arch.call('may')
    }
    tap () {   //  注册监听函数,订阅
        this.hooks.arch.tap('node', function (name) {
            console.log('node', name)
            return '学的不错'
        })
        this.hooks.arch.tap('react', function (name) {
            console.log('react', name)
        })
    }
}


let l = new Lesson()

l.tap();  //注册两个函数
l.start() // 启动勾子

```

`3.theory.js`

```
class SyncWaterfallHook {  // 勾子是同步的 - 瀑布
    constructor(args) {  // args => ['name']
        this.tasks = []
    }
    tap (name, task) {
        this.tasks.push(task)
    }
    call (...args) {
        let [first, ...others] = this.tasks;
        let ret = first(...args)
        others.reduce((a, b) => {
            return b(a);
        }, ret);

    }
}

let hook = new SyncWaterfallHook(['name'])

hook.tap('react', function (name) {
    console.log('react', name);
    return 'react Ok'
    // return undefined
})


hook.tap('node', function (name) {
    console.log('node', name);
    return 'node Ok'
})

hook.tap('webpack', function (data) {
    console.log('webpack', data);
})



hook.call('jw')


```

## tapable介绍 - SyncLoopHook

`SyncLoopHook`当监听函数被触发的时候，如果该监听函数返回`true`时则这个监听函数会反复执行，如果返回 `undefined` 则表示退出循环

`4.use.js`

```
let {SyncLoopHook} = require('tapable')   // 解构同步勾子

// 不返回undefined 会多次执行

class Lesson {
    constructor () {
        this.index = 0
        this.hooks = {
            // 订阅勾子
            arch: new SyncLoopHook(['name']),

        }
    }
    start () {
        // 发布
        this.hooks.arch.call('may')
    }
    tap () {   //  注册监听函数,订阅
        this.hooks.arch.tap('node',  (name) => {
            console.log('node', name)
            return ++this.index === 3 ? undefined : '继续学'
        })
        this.hooks.arch.tap('react',  (name) => {
            console.log('react', name)
        })
    }
}


let l = new Lesson()

l.tap();  //注册两个函数
l.start() // 启动勾子

```

`4.theory.js`

```
class SyncLoopHook {  // 勾子是同步的 - 瀑布
    constructor(args) {  // args => ['name']
        this.tasks = []
    }
    tap (name, task) {
        this.tasks.push(task)
    }
    call (...args) {
        this.tasks.forEach(task => {
            let ret
            do {
                ret = task(...args);
            } while(ret !== undefined)
        })
    }
}

let hook = new SyncLoopHook(['name'])
let total = 0
hook.tap('react', function (name) {
    console.log('react', name);
    return ++total === 3 ? undefined: '继续学'
})


hook.tap('node', function (name) {
    console.log('node', name);
})

hook.tap('webpack', function (data) {
    console.log('webpack', data);
})



hook.call('jw')

```


## `AsyncParallelHook` 与 `AsyncParallelBailHook`

异步的勾子分两种`串行`和`并行`

`并行`等待所有并发的异步事件执行后执行回调

注册的三种方法

1. 异步的注册方法`tap`
2. 异步的注册方法`tapAsync`， 还有个回调参数
3. `topPromise`,注册`promise`

调用的三种

1. call (同步)
2. callAsync （异步）
3. promise （异步）

这里介绍的是异步并行的

#### AsyncParallelHook 

不关心监听函数的返回值。

`5.use.js`

```
let {AsyncParallelHook} = require('tapable')   // 解构同步勾子

// 不返回undefined 会多次执行

class Lesson {
    constructor() {
        this.index = 0
        this.hooks = {
            // 订阅勾子
            arch: new AsyncParallelHook(['name']),

        }
    }

    start() {
        // 发布callAsync
        // this.hooks.arch.callAsync('may', function () {
        //     console.log('end');
        // })
        // 另一种发布promise
        this.hooks.arch.promise('may').then(function () {
                console.log('end');
            }
        )
    }

    tap() {   //  注册监听函数,订阅
        // 注册tapAsync
        // this.hooks.arch.tapAsync('node',  (name, callback) => {
        //     setTimeout(() => {
        //         console.log('node', name)
        //         callback()
        //     }, 1000)
        // })
        // this.hooks.arch.tapAsync('react',  (name, callback) => {
        //     setTimeout(() => {
        //         console.log('react', name)
        //         callback()
        //     }, 1000)
        // })
        // 另一种订阅 tapPromise
        this.hooks.arch.tapPromise('node', (name) => {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    console.log('node', name)
                    resolve()
                }, 1000)
            })
        })
        this.hooks.arch.tapPromise('react', (name) => {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    console.log('react', name)
                    resolve()
                }, 1000)
            })
        })
    }
}


let l = new Lesson()

l.tap();  //注册两个函数
l.start() // 启动勾子


```


`5.theory.js`

```
class AsyncParallelHook {  // 勾子是同步的 - 瀑布
    constructor(args) {  // args => ['name']
        this.tasks = []
    }

    tapAsync(name, task) {
        this.tasks.push(task)
    }

    tapPromise(name, task) {
        this.tasks.push(task)
    }
    callAsync(...args) {
        let finalCallback = args.pop()   // 拿出最终的函数
        let index = 0
        let done = () => {   // 类似promise.all的实现
            index++;
            if (index === this.tasks.length) {
                finalCallback();
            }
        }
        this.tasks.forEach(task => {
            task(...args, done) // 这里的args 已经把最后一个参数删掉
        })
    }

    promise(...args) {
        let tasks = this.tasks.map(task => task(...args))
        return Promise.all(tasks)
    }
}

let hook = new AsyncParallelHook(['name'])


// hook.tapAsync('react', function (name, callback) {
//     setTimeout(() => {
//         console.log('react', name);
//         callback()
//     }, 1000)
// })
//
// hook.tapAsync('node', function (name, callback) {
//     setTimeout(() => {
//         console.log('node', name);
//         callback()
//     }, 1000)
// })

// hook.tapAsync('webpack', function (name, callback) {
//     setTimeout(() => {
//         console.log('webpack', name);
//         callback()
//     }, 1000)
// })

hook.tapPromise('react', function (name, callback) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('react', name);
            resolve()
        }, 1000)
    })
})

hook.tapPromise('node', function (name, callback) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('node', name);
            resolve()
        }, 1000)
    })
})



//
// hook.callAsync('jw', function () {
//     console.log('end');
// })


hook.promise('jw').then(function () {
    console.log('end');
})


```


#### AsyncParallelBailHook

只要监听函数的返回值不为 `null`，就会忽略后面的监听函数执行，直接跳跃到`callAsync`等触发函数绑定的回调函数，然后执行这个被绑定的回调函数。

使用和原理与`SyncBailHook`相似


## 异步串行 —— AsyncSeriesHook

`串行 `one by one

`6.use.js`

```
let {AsyncSeriesHook} = require('tapable')   // 解构同步勾子


class Lesson {
    constructor() {
        this.index = 0
        this.hooks = {
            // 订阅勾子
            arch: new AsyncSeriesHook(['name']),

        }
    }

    start() {
        // 发布
        // this.hooks.arch.callAsync('may', function () {
        //     console.log('end');
        // })
        // 另一种发布
        this.hooks.arch.promise('may').then(function () {
                console.log('end');
            }
        )
    }

    tap() {   //  注册监听函数,订阅
        // this.hooks.arch.tapAsync('node',  (name, callback) => {
        //     setTimeout(() => {
        //         console.log('node', name)
        //         callback()
        //     }, 1000)
        // })
        // this.hooks.arch.tapAsync('react',  (name, callback) => {
        //     setTimeout(() => {
        //         console.log('react', name)
        //         callback()
        //     }, 1000)
        // })
        // 另一种订阅
        this.hooks.arch.tapPromise('node', (name) => {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    console.log('node', name)
                    resolve()
                }, 1000)
            })
        })
        this.hooks.arch.tapPromise('react', (name) => {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    console.log('react', name)
                    resolve()
                }, 1000)
            })
        })
    }
}


let l = new Lesson()

l.tap();  //注册两个函数
l.start(); // 启动勾子

```

`6.theory.js`

```
class AsyncSeriesHook {  //
    constructor(args) {  // args => ['name']
        this.tasks = []
    }

    tapAsync(name, task) {
        this.tasks.push(task)
    }

    tapPromise(name, task) {
        this.tasks.push(task)
    }

    callAsync(...args) {
        let finalCallback = args.pop()
        let index = 0;
        let next = () => {
            if (this.tasks.length === index) return finalCallback();
            let task = this.tasks[index++];
            task(...args, next);
        }
        next();
    }

    promise(...args) {
        // 将promise串联起来
        let [first, ...other] = this.tasks
        return other.reduce((p, n) => {
             return p.then(() => n (...args))
        }, first(...args))
    }
}

let hook = new AsyncSeriesHook(['name'])


// hook.tapAsync('react', function (name, callback) {
//     setTimeout(() => {
//         console.log('react', name);
//         callback()
//     }, 1000)
// })
//
// hook.tapAsync('node', function (name, callback) {
//     setTimeout(() => {
//         console.log('node', name);
//         callback()
//     }, 1000)
// })
//
// hook.tapAsync('webpack', function (name, callback) {
//     setTimeout(() => {
//         console.log('webpack', name);
//         callback()
//     }, 1000)
// })


hook.tapPromise('react', function (name, callback) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('react', name);
            resolve()
        }, 1000)
    })
})

hook.tapPromise('node', function (name, callback) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('node', name);
            resolve()
        }, 1000)
    })
})




// hook.callAsync('jw', function () {
//     console.log('end');
// })


hook.promise('jw').then(function () {
    console.log('end');
})

```

## 异步串行 —— AsyncSeriesWaterfallHook

上一个监听函数的中的`callback(err, data)`的第二个参数,可以作为下一个监听函数的参数


`7.use.js`

```
let {AsyncSeriesWaterfallHook} = require('tapable')   // 解构同步勾子


class Lesson {
    constructor() {
        this.index = 0
        this.hooks = {
            // 订阅勾子
            arch: new AsyncSeriesWaterfallHook(['name']),

        }
    }

    start() {
        // 发布
        this.hooks.arch.callAsync('may', function () {
            console.log('end');
        })
        // 另一种发布
        // this.hooks.arch.promise('may').then(function () {
        //         console.log('end');
        //     }
        // )
    }

    tap() {   //  注册监听函数,订阅
        this.hooks.arch.tapAsync('node',  (name, callback) => {
            setTimeout(() => {
                console.log('node', name)
                // callback(null, 'result')
                callback('error', 'result')   // 如果放error, 会跳过直接后面的勾子，直接走到最终的

            }, 1000)
        })
        this.hooks.arch.tapAsync('react',  (name, callback) => {
            setTimeout(() => {
                console.log('react', name)
                callback()
            }, 1000)
        })
        // 另一种订阅
        // this.hooks.arch.tapPromise('node', (name) => {
        //     return new Promise((resolve, reject) => {
        //         setTimeout(() => {
        //             console.log('node', name)
        //             resolve()
        //         }, 1000)
        //     })
        // })
        // this.hooks.arch.tapPromise('react', (name) => {
        //     return new Promise((resolve, reject) => {
        //         setTimeout(() => {
        //             console.log('react', name)
        //             resolve()
        //         }, 1000)
        //     })
        // })
    }
}


let l = new Lesson()

l.tap();  //注册两个函数
l.start(); // 启动勾子

```

`7.theory.js`

```
class AsyncSeriesWaterfallHook {  //
    constructor(args) {  // args => ['name']
        this.tasks = []
    }

    tapAsync(name, task) {
        this.tasks.push(task)
    }

    tapPromise(name, task) {
        this.tasks.push(task)
    }
    callAsync(...args) {
        let finalCallback = args.pop()
        let index = 0;
        let next = (err, data) => {
            let task = this.tasks[index]
            if(!task) return finalCallback();
            if (index === 0) {
                // 执行的第一个
                task(...args, next)
            } else {
                task(data, next)
            }
            index ++
        }
        next();
    }

    promise(...args) {
        // 将promise串联起来
        let [first, ...other] = this.tasks
        return other.reduce((p, n) => {
             return p.then((data) => n(data))
        }, first(...args))
    }
}

let hook = new AsyncSeriesWaterfallHook(['name'])


// hook.tapAsync('react', function (name, callback) {
//     setTimeout(() => {
//         console.log('react', name);
//         callback(null, '结果1')
//     }, 1000)
// })
//
// hook.tapAsync('node', function (name, callback) {
//     setTimeout(() => {
//         console.log('node', name);
//         callback(null, '结果2')
//     }, 1000)
// })
//
// hook.tapAsync('webpack', function (name, callback) {
//     setTimeout(() => {
//         console.log('webpack', name);
//         callback()
//     }, 1000)
// })

//
hook.tapPromise('react', function (name, callback) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('react', name);
            resolve('result')
        }, 1000)
    })
})

hook.tapPromise('node', function (name, callback) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('node', name);
            resolve()
        }, 1000)
    })
})


//
//
// hook.callAsync('jw', function () {
//     console.log('end');
// })


hook.promise('jw').then(function () {
    console.log('end');
})

```

