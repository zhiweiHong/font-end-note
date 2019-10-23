#### 概念

 本质上，*`webpack`* 是一个现代 JavaScript 应用程序的*静态模块打包器(module bundler)*。当 `webpack` 处理应用程序时，它会递归地构建一个*依赖关系图(dependency graph)*，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 *bundle*。 

#### 核心概念

##### `entry` 

- 入口文件

##### `output`

- 打包后存放相关配置

##### `loader`

- 为了让`webpack`将非js模块也视为有效模块

##### `plugins` 

- 增强`webpack`功能

#### 安装

##### 全局安装

``` shell
cnpm install webpack -g
```

##### 私有安装

一般选用私有安装，因为每个项目的`webpack`版本可能不一致。

```shell
cnpm install webpack@3.6.0 -S
```

#### 配置

##### 默认配置

- `webpack`默认的配置文件是 `webpack.config.js`
- 默认使用`CommonJs`语法
- 默认loader加载顺序，是从右到左

```js
const path = require('path');
 
module.exports = {
    entry: "./src/main.js",
    output: {
      // 发布文件名
      filename: "bundle.js",
      path: path.resolve(__dirname, 'dist')
    },
  }
```

##### `css-Loader`  `style-loader`

- 一般`style-loader`与`css-loader`结合使用
- 一般用于解析`css`文件

```js
module: {
      rules: [
        {
          test: /\.css$/,
          use: ["style-loader","css-loader"]
        }
      ]
    }
```

##### `url-loader` `file-loader`

- 都是用于处理文件
-  `url-loader` 功能类似于 `file-loader`，但是在文件大小（单位 byte）低于指定的限制时，可以返回一个 `DataURL`（图片的话就是`base64`）。 

```js
const path = require('path');
 
module.exports = {
    entry: "./src/main.js",
    output: {
      filename: "bundle.js",
      path: path.resolve(__dirname, 'dist'),
      // 设置前缀路径，用于找到对应的图片文件
      publicPath:"dist/",
    },
    
    module: {
      rules: [
        {
          test: /\.(png|jpg|gif)$/,
          use: [
            {
              loader: 'url-loader',
              options: {
                limit: 8192,
                // 重命名
                name:"img/[name].[hash:8].[ext]"
              }
            }
          ]
        }
      ]
    }
  }
```

##### `ES6`语法处理

```js
cnpm install babel-loader@7 babel-core babel-preset-es2015 -S
```

```js
{
          test: /\.js$/,
          // 排除引用lib包
          exclude: /(node_modules|bower_components)/,
          use: {
            loader: 'babel-loader',
            options: {
              // 设置规则
              presets: ['es2015']
            }
          }
        }
```

#### 集成`vue`

- `vue` 默认发行两个版本

  - runtime-only。
    - 如果未对`vue`进行配置，默认使用`import vue from Vue`使用的就是runtime-only这个版本。
    - 此版本中代码不可以有任何的template。
    - 如果`Vue`实例中只配置了`el`，默认会将整个`div`认为是一个`template`
    - 更性能，更快捷
    - 构建方式
      - render -> vm -> ui
  - runtime-compile 
    - 可以用`template`，可以进行编译
    - 构建较为复杂
      - template -> ast -> render -> vm -> ui

- 解决`You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.`

  - 不使用`import Vue from 'vue'`转使用`import Vue from 'vue/dist/vue.js'`

  - 在`webpack.config.js`中进行配置`resolve`

    ```js
    resolve:{
        alias:{
            'vue$':'vue/dist/vue.esm.js'
        }
    },
    ```

#### 插件使用

##### `BannerPlugin`

- 版权插件

  ```js
  const path = require('path');
  const webpack = require("webpack")
  module.exports = {
    entry: "./src/main.js",
    output: {
      filename: "bundle.js",
      path: path.resolve(__dirname, 'dist'),
      publicPath: "E:\Work\Project\learnVue\learnWebpack\dist/",
    },
    plugins: [
      // 默认插件
      new webpack.BannerPlugin("版权归hzhiwei所有")
    ]
  }
  ```

##### `HtmlWebpackPlugin`

- 生成发布的`html`文件

  - 对于此文件而言，将无需再写入script标签，因为插件会自动生成script标签

- 这对于在文件名中包含每次会随着编译而发生变化哈希的 `webpack bundle` 尤其有用

  ```shell
  cnpm install --save-dev html-webpack-plugin
  ```

  

  ```js
  const path = require('path');
  const htmlWebpackPlugin = require("html-webpack-plugin");
  module.exports = {
    entry: "./src/main.js",
    output: {
      filename: "bundle.js",
      path: path.resolve(__dirname, 'dist'),
    },
    plugins: [
      new htmlWebpackPlugin({
        // 指定生成模板
        template:"src/main.html"
      })
    ]
  }
  ```

##### `UglifyjsWebpackPlugin`

- 主要作用是对`js`等文件进行压缩处理

- `webpack3.6.0`匹配版本是`uglifyjs1.0`

  ```shell
  cnpm install --save-dev uglifyjs-webpack-plugin
  ```

#### `webpack-dev-server`

- 用于提供web服务

- devServer

  - contentBase 服务监听文件
  - compress  是否压缩
  - port 开放端口
  - inline 是否实时监听

  ```shell
  cnpm install webpack-dev-server -S
  ```

  ```js
  const path = require('path');
  
  require("html-webpack-plugin");
  module.exports = {
    entry: "./src/main.js",
    output: {
      filename: "bundle.js",
      path: path.resolve(__dirname, 'dist'),
      publicPath: '/',
    },
  
    devServer: {
      contentBase: './dist',
      
    }
  }
  ```

  

