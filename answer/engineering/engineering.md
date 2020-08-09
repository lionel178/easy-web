# 工程化 面试题

## 目录

- [1. 谈谈你对 webpack 的看法](#1)
- [2. webpack 与 grunt、gulp、rollup 的不同](#2)
- [3. 在什么情况下选择 webpack？在什么情况下选择 rollup？](#3)
- [4. webpack 有什么优劣势](#4)
- [5. webpack 有哪些常见的 Loader？你用过哪些 Loader？](#5)
- [6. webpack 有哪些常见的 Plugin？你用过哪些 Plugin？](#6)
- [7. 那你再说一说 Loader 和 Plugin 的区别？](#7)
- [8. webpack 构建流程简单说一下](#8)
- [9. webpack 如何解析代码路径的](#9)
- [10. 使用 webpack 开发时，你用过哪些可以提高效率的插件？](#10)
- [11. source map 是什么？生产环境怎么用？](#11)
- [12. 模块打包原理知道吗？](#12)
- [13. 文件监听原理呢？](#13)
- [14. 说一下 webpack 的热更新原理吧](#14)
- [15. 如何对 bundle 体积进行监控和分析？](#15)
- [16. 文件指纹是什么？怎么用？](#16)
- [17. 在实际工程中，配置文件上百行乃是常事，如何保证各个 loader 按照预想方式工作？](#17)
- [18. 代码分割的本质是什么？有什么意义？你是如何拆分的？](#18)
- [19. 是否写过 Loader？简单描述一下编写 loader 的思路？](#19)
- [20. 是否写过 Plugin？简单描述一下编写 Plugin 的思路？](#20)
- [21. 聊一聊 Babel 原理吧](#21)
- [22. 在项目中，如何优化 Webpack](#22)

## 题解

#### <a href="#1" id="1">1. 谈谈你对 webpack 的看法</a>

webpack 是一个 模块打包工具，可以使用 webpack 管理模块依赖，并编译输出模块们所需的静态文件。它能 很好地管理、打包 Web 开发中所用到的 HTML、JS、CSS 以及各种静态文件（图片、字体等），让开发过程更加高效。对于不同类型的资源， webpack 有对应的模块加载器。 webpack 模块打包器会分析模块间的依赖关系，最后生成了优化且合并后的静态资源。

特点：

1. 对 CommonJS、AMD、ES6 的语法做了兼容
2. 对 js、css、图片等资源文件都支持打包
3. 串联模块加载器以及插件机制，让其具有更好的灵活性和扩展性，例如对 ES6 的支持
4. 可以将代码切割成不同的 chunk，实现按需加载，降低了初始化时间
5. 支持 sourcemap，易于调试
6. 具有强大的 plugin 接口，大多是内部插件，使用起来比较灵活

<br/>
<br/>

#### <a href="#2" id="2">2. webpack 有什么优劣势</a>

优点：

1. 专注于模块化项目
2. plugins 能做很多事情
3. 社区活跃

缺点：

1. 上手难度较高
2. plugins 繁多，需要不断学习才能灵活掌握，还经常出现老文章里推荐的 plugin 已经停止维护
3. 对于初学者，调试很难定位问题
4. 构建速度较慢，这也是后起之秀主要针对的点

<br/>
<br/>

#### <a href="#3" id="3">3. 在什么情况下选择 webpack？在什么情况下选择 rollup？</a>

- webpack 适用于大型复杂的前端站点构建
- rollup 适用于基础库的打包，如：vue、react

另外一个角度：
如果你需要进行代码分割，或者你有很多的静态资源，再或者你做的东西深度依赖 CommonJS，选择 Webpack。  
如果你的代码基于 ES2015 模块编写，并且做的东西是准备给他人使用的，可以考虑 rollup。

<br/>
<br/>

#### <a href="#4" id="4">4. webpack 与 grunt、gulp、rollup 的不同</a>

grunt 和 gulp 是基于任务和流（Task、Stream）的，找到一个（或一类）文件，对齐做一些列链式操作，更新流上的数据，整条链式操作构成了一个任务，多个任务就构成了整个 web 的构建流程

rollup 与 webpack 类似，但专注于 ES6 的模块打包。它最大的亮点是利用 ES6 模块设计，生成更简洁、更简单的代码。

webpack 是模块化管理工具和打包工具，它是基于入口的。 webpack 会自动递归解析入口所需要加载的所有资源文件，然后用不同 Loader 来处理不同的文件，用 Plugin 来扩展 webpack 功能。

虽然现在 webpack 相对比较主流，但一些轻量化的任务还是会用 gulp 来处理，比如单独打包 CSS 文件；另外一些类库的代码使用 rollup 打包。

<br/>
<br/>

#### <a href="#5" id="5">5. webpack 有哪些常见的 Loader？你用过哪些 Loader？</a>

我经常使用的有：

**文件：**

`raw-loader`： 加载文件原始内容

`file-loader`：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件（处理图片和字体）

`url-loader`：与 file-loader 类似，区别是用户可以设置一个阈值，大于阈值会交给 file-loader 处理，小于时返回文件的 base64 形式编码（处理图片和字体）

**转换编译：**

`babel-loader`： 把 ES6 转换成 ES5

`ts-loader`： 将 ts 转换成 js

`awesome-typescript-loader`： 将 ts 转换成 js ，性能比 ts-loader 好点

**样式：**

`css-loader`： 加载 CSS， 支持模块化、压缩、文件导入等特性  
less-loader：将 less 代码转换成 css

`postcss-loader`：扩展 CSS 语法，使用下一代 CSS，可以配合 autoprefixer 插件自动补齐 CSS3 前缀

`style-loader`：把 CSS 代码注入到 JS 中，通过 DOM 操作去加载 CSS

**其他：**

`image-loader`：加载并压缩图片文件

`eslint-loader`： 通过 ESLint 检查 js 代码

`tslint-loader`： 通过 TSLint 检查 ts 代码

`cache-loader`：可以在一些性能开销较大的 Loader 前添加，目的是将结果缓存到磁盘中

`thread-loader`：开启多线程打包

参考 / 来源：  
[官方文档](https://www.webpackjs.com/loaders/)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#6" id="6">6. webpack 有哪些常见的 Plugin？你用过哪些 Plugin？</a>

`ignore-plugin`： 从 bundle 中排出某些模块

`html-webpack-plugin`：简单创建 HTML 文件

`terser-webpack-plugin`：js 代码压缩，支持压缩 ES6

`extract-text-webpack-plugin`：分离样式文件，从 bundle 中提取 css 到单独的文件

`clean-webpack-plugin`： 目录清理

`webpack-bundle-analyzer`： 可视化 Webpack 输出文件的体积 (业务组件、依赖第三方模块)

`compression-webpack-plugin`：开启 gzip 压缩

`hard-source-webpack-plugin`：开发阶段使用，为模块提供中间缓存步骤，加快二次编译速度

`progress-bar-webpack-plugin`： 项目启动或者打包进度条插件

`friendly-errors-webpack-plugin`：开发友好的错误提示插件

参考 / 来源：  
[官方文档](https://www.webpackjs.com/plugin/)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#7" id="7">7. 那你再说一说 Loader 和 Plugin 的区别？</a>

**从功能角度区分**：

`Loader` 用于加载待打包的资源，`Plugin` 用于扩展 webpack 功能。

`Loader` 本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果，主要用于加载某些资源文件。因为 webpack 只认识 js，这就需要对应的 loader 将资源转化，加载进来。

`Plugin` 用于扩展 webpack 的功能（loader 也是扩展功能，但只专注于转化文件这一领域），在 webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 webpack 提供的 API 改变输出结果。

**从运行时机角度区分**：

`Loader` 运行在打包文件之前（loader 为在模块加载时的预处理文件）  
`Plugin` 在整个编译周期都起作用。

**从使用角度区分**：

`Loader` 在 rules 中配置，类型为数组。每一项都是一个 Object ， 内部包含了 test（类型文件）、loader、options（参数）等属性。

`Plugin` 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。

参考 / 来源：  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)  
[前端面试题：wepack 中 loader 和 plugin 的区别](https://blog.csdn.net/jiang7701037/article/details/98887179)  
[webpack 的 loader 和 plugin](https://blog.csdn.net/wp270280522/article/details/51496436)

<br/>
<br/>

#### <a href="#8" id="8">8. webpack 构建流程简单说一下</a>

Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

- `初始化参数`：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数

- `开始编译`：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译

- `确定入口`：根据配置中的 entry 找出所有的入口文件

- `编译模块`：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出改模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理

- `完成模块编译`：在经过第 4 步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系

- `输出资源`：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk， 再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会

- `输出完成`：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

在以上过程中，Webpack 会在特定的事件点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。

简单地说：

- `初始化`：启动构建，读取与合并配置参数，加载 Plugin，实例化 Compiler

- `编译`：从 Entry 出发，针对每个 Module 串行调用对应的 Loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归地进行编译处理。

- `输出`：将编译后的 Module 组合成 Chunk，将 Chunk 转换成文件，输出到文件系统中

参考 / 来源：  
[webpack 编译流程](https://juejin.im/post/5d70b186f265da03e1689864)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#9" id="9">9. webpack 如何解析代码路径的</a>

webpack 依赖 enhanced-resolve 来解析代码模块的路径，这个模块像 Node.js 那一套模块路径解析的增强版本，有很多可以自定义的解析配置。

模块解析规则分三种：

- 解析相对路径

  1. 查找相对当前模块的路径下是否有对应文件或文件夹，是`文件`则直接加载
  2. 如果是文件夹则找到对应文件夹下是否有 `package.json` 文件
  3. 有的话就按照文件中的 `main` 字段的文件名来查找文件
  4. 没有 package.json 或 main，则查找 `index.js` 文件

- 解析绝对路径
  直接查找对应路径的文件，不建议使用，因为不同的机器用绝对路径会找不到

- 解析模块名
  查找当前文件目录，父级直至根目录下的 node_modules 文件夹，看是否有对应名称的模块

另外：通过设置 resolve.alias 、 resolve.extensions 、 resolve.modules 、 resolve.mainFields 、 resolve.resolveLoader 等选项来优化路径查找速度。

参考 / 来源：  
[[webpack]--webpack 如何解析代码模块路径](https://www.cnblogs.com/ifannie/p/9208808.html)

<br/>
<br/>

#### <a href="#10" id="10">10. 使用 webpack 开发时，你用过哪些可以提高效率的插件？</a>

- `webpack-merge`：提取公共配置，减少重复配置代码

- `HotModuleReplacementPlugin`：模块热替换

- `thread-loader`: 并行打包，加快启动速度

- `babel-plugin-transform-remove-console`: 自动删除 `console.log`

<br/>
<br/>

#### <a href="#11" id="11">11. source map 是什么？生产环境怎么用？</a>

`source map` 是将编译、打包、压缩后的代码映射回源码的过程。打包压缩后的代码不具备良好的可读性，想要调试源码就需要 source map，出错的时候，浏览器控制台将直接显示原始代码出错的位置。

map 文件只要不打开开发者工具，浏览器是不会加载的。

线上环境一般有三种处理方案：

- `source-map`：map 文件包含完整的原始代码，但是打包会很慢。打包后的 js 最后一行是 map 文件地址的注释。通过 nginx 设置将 .map 文件只对白名单开放（公司内网）

- `hideen-source-map`：与 sourceMap 相同，也生成 map 文件，但是打包后的 js 最后没有 map 文件地址的引用。浏览器不会主动去请求 map 文件，一般用于网站错误分析，需要让错误分析工具按名称匹配到 map 文件。 或者借助第三方错误监控平台 Sentry 使用

- `nosources-source-map`：只会显示具体行数以及查看源码的错误栈。安全性比 sourcemap 高

注意：避免在生产中使用 `inline-` 和 `eval-`，因为它们会增加 bundle 体积大小，并降低整体性能

参考 / 来源：  
[【webpack】你所不知道的 sourceMap](https://juejin.im/post/5e099ee3f265da33910a547d)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#12" id="12">12. 模块打包原理知道吗？</a>

webpack 根据 webpack.config.js 中的入口文件，在入口文件里识别模块依赖，不管这里的模块依赖是用 CommonJS 写的，还是 ES6 Module 规范写的，webpack 会自动进行分析，并通过转换、编译代码，打包成最终的文件。**最终文件中的模块实现是基于 webpack 自己实现的 webpack_require（es5 代码）**，所以打包后的文件可以跑在浏览器上。

同时以上意味着在 webapck 环境下，你可以只使用 ES6 模块语法书写代码（通常我们都是这么做的），也可以使用 CommonJS 模块语法，甚至可以两者混合使用。因为从 webpack2 开始，内置了对 ES6、CommonJS、AMD 模块化语句的支持，**webpack 会对各种模块进行语法分析，并做转换编译**。

另外，针对异步模块：**webpack 实现模块的异步加载有点像 jsonp 的流程**。

遇到异步模块时，使用`__webpack_require__.e`函数去把异步代码加载进来。该函数会在 html 的 head 中动态增加 script 标签，src 指向指定的异步模块存放的文件。

加载的异步模块文件会执行`webpackJsonpCallback`函数，把异步模块加载到主文件中。

所以后续可以像同步模块一样,直接使用`__webpack_require__("./src/async.js")`加载异步模块。

参考 / 来源：  
[Webpack 模块打包原理](https://lq782655835.github.io/blogs/project/webpack4-1.module.html)

<br/>
<br/>

#### <a href="#13" id="13">13. 文件监听原理呢？</a>

在发现源码发生变化时，自动重新构建出新的输出文件。

缺点：每次需要手动刷新浏览器

原理：轮询判断文件的最后编辑时间是否变化，初次构建时把文件的修改时间储存起来，下次有修改时会和上次修改时间比对，发现不一致的时候，并不会立刻告诉监听者，而是先缓存起来，等 `aggregateTimeout` 后，把变化列表一起构建，并生成到 bundle 文件夹。

```js
module.export = {
  // 默认 false，也就是不开启
  watch: true,
  watchOptions: {
    // 默认为空，不监听的文件夹或者文件，支持正则匹配
    ignore: /node_modules/,
    // 监听到变化发生后会等 300ms 再去执行，默认 300ms
    aggregateTimeout: 300,
    // 判断文件是否发生变化是通过不停询问系统指定文件有没有变化实现的，默认每秒询问 1000 次
    poll: 1000,
  },
};
```

参考 / 来源：  
[webpack--文件监听的原理](https://www.cnblogs.com/luckyXcc/p/11333642.html)

<br/>
<br/>

#### <a href="#14" id="14">14. 说一下 webpack 的热更新原理吧</a>

`Webpack` 的热更新又称为热替换（`Hot Module Replacement`），缩写为 `HMR`。这个机制可以做到不用刷新浏览器而将变更的模块替换掉旧的模块。

相对于手动刷新页面，HMR 的优点在于可以保存应用的状态，提高开发效率。

1. webpack 构建的项目，分为 server 端和 client 端（也就是浏览器），项目启动时，双方会保持一个 socket 连接，用于通话。

2. 当本地资源发生变化时，server 向浏览器发送新资源的 hash 值，浏览器调用 reloadApp 方法，检查是否有变化，有差异是会向 server 发起 `Ajax`获取更改内容（文件列表、hash），这样浏览器继续借助这些信息向 server 端发起请求，通过 `jsonp` 的方式获取 chunk 的增量更新。

后续的部分（拿到增量更新之后如何处理？哪些状态该保留？哪些又需要更新？）由 `HotModulePlugin` 来完成，提供了相关 API 以供开发者针对自身场景进行处理，像 `react-hot-loader` 和 `vue-loader` 都是借助这些 API 实现 HMR。

参考 / 来源：  
[为了前端的深度-webpack 热更新](https://juejin.im/post/5c3ca02ce51d45451850250a)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#15" id="15">15. 如何对 bundle 体积进行监控和分析？</a>

1. `VSCode` 中有一个插件 `Import Cost` 可以帮助我们对引入模块的大小进行实时监测

2. `webpack-bundle-analyzer` 生成 `bundle` 的模块组成图，显示所占体积

3. `bundlesize` 工具包可以进行自动化资源体积监控，集成到 CI 中，就可以在应用过大的时候收到提醒。

参考 / 来源：  
[【译】使用 webpack 进行 web 性能优化：监控和分析应用](https://zhuanlan.zhihu.com/p/44828564)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#16" id="16">16. 文件指纹是什么？怎么用？</a>

文件指纹是打包后输出的文件名的后缀。

- `Hash`：和整个项目的构建相关，只要项目文件有修改，整个项目构建的 hash 值就会更改

- `Chunkhash`： 和 Webpack 打包的 chunk 有关，不同的 entry 会生成不同的 chunkhash

- `Contenthash`：根据文件内容来定义 hash，文件内容不变，则 contenthash 不变。

js 的文件指纹设置：设置 output 的 filename，用 chunkhash

```js
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js',
  },
  output: {
    filename: '[name][chunkhash:8].js',
    path: __dirname + '/dist',
  },
};
```

css 的文件指纹设置：

- 设置 MiniCssExtractPlugin 的 filename，使用 contenthash
- 设置 ExtractTextPlugin 的 filename

```js
module.exports = {
  // ...
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name][contenthash:8].css',
    })
    new ExtractTextPlugin('[name][contenthash].css'),
  ]
}
```

图片的文件指纹设置
设置 file-loader 或 url-loader 的 name， 使用 hash。

参考 / 来源：  
[webpack 文件指纹配置](https://blog.csdn.net/m0_37938910/article/details/91375338)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#17" id="17">17. 在实际工程中，配置文件上百行乃是常事，如何保证各个 loader 按照预想方式工作？</a>

webpack 配置中，通过 module.rules 中的 enforce 字段，将 loader 分为 `preLoader` 、 `postLoader` 和 `loader` 三种，执行顺序为 `pre -> loader -> inline -> post`

`pre` 代表在所有正常 loader 之前执行， `post` 是所有 loader 之后执行。（inline 官方不推荐使用）

参考 / 来源：  
[【webpack 进阶】你真的掌握了 loader 么？- loader 十问](https://www.jianshu.com/p/6a08e1b1f2fd)  
[Webpack Loader 种类以及执行顺序](https://www.cnblogs.com/hanshuai/p/11287231.html)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#18" id="18">18. 代码分割的本质是什么？有什么意义？你是如何拆分的？</a>

代码分割的本质其实是在 `源代码直接上线` 和 `打包成唯一脚本 main.bundle.js` 这两种极端方案之间的一种更适合实际场景的中间状态。

**用可接受的服务器性能压力增加来换取更好的用户体验。**

源代码直接上线：虽然过程可控，但是 http 请求多，性能开销大。

打包成唯一脚本：

- 服务器压力小，但是页面空白期长，用户体验不好
- 大体积文件会增加编译时间，影响开发效率
- 多页应用，独立访问单个页面时，需要下载大量不相干的资源

代码分割（splitChunk）的意义：

- 复用的代码抽离到公共模块中，解决代码冗余
- 公共模块再按照使用的页面多少（或其他思虑）进一步拆分，用来减小文件体积，顺便优化首屏加载速度

拆分原则：  
如何拆分因项目而异，普遍适应的拆分原则：

1. 业务代码和第三方库分离打包，实现代码分割
2. 业务代码中的公共业务模块提取打包到一个模块
3. 首屏相关模块单独打包

参考 / 来源：  
[浅谈 webpack 优化之代码分割与公共代码提取详解](http://www.duanlonglong.com/qdjy/546.html)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#19" id="19">19. 是否写过 Loader？简单描述一下编写 loader 的思路？</a>

所谓 loader 只是一个导出为函数的 JavaScript 模块。 webpack 中的 `loader runner` 会调用这个函数，然后把上一个 loader 产生的结果或者资源文件(resource file)传入进去。函数的 this 上下文将由 webpack 填充，并且 webpack 的 `loader runner` 具有一些有用方法，可以使 loader 改变为异步调用方式，或者获取 query 参数。

编写 Loader 时要遵循单一 z 职责，每个 Loader 只做一种"转义"工作。 每个 Loader 的拿到的是源文件内容（source），可以通过返回值的方式将处理后的内容输出，也可以调用 this.callback()方法，将内容返回给 webpack。 还可以通过 this.async()生成一个 callback 函数，再用这个 callback 将处理后的内容输出出去。 此外 webpack 还为开发者准备了开发 loader 的工具函数集——loader-utils。

- Loader 运行在 Node.js 中，我们可以调用任意 Node.js 自带的 API 或者安装第三方模块进行调用
- Webpack 传给 Loader 的源内容默认都是 UTF-8 格式编码的字符串，当某些场景下 Loader 处理二进制文件时，需要通过 exports.raw = true 告诉 Webpack 该 Loader 是否需要二进制数据
- 尽可能的异步化 Loader，如果计算量很小，同步也可以
- Loader 是无状态的，我们不应该在 Loader 中保留状态
- 使用 loader-utils 和 schema-utils 为我们提供的实用工具
- 加载本地 Loader 方法
  - Npm link
  - ResolveLoader

参考 / 来源：

[官网](https://www.webpackjs.com/api/loaders/)  
[关于 webpack 的面试题](https://www.cnblogs.com/gaoht/p/11310365.html)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#20" id="20">20. 是否写过 Plugin？简单描述一下编写 Plugin 的思路？</a>

webpack 在运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 webpack 提供的 API 改变输出结果。

- compiler 暴露了和 Webpack 整个生命周期相关的钩子
- compilation 暴露了与模块和依赖有关的粒度更小的事件钩子
- 插件需要在其原型上绑定 apply 方法，才能访问 compiler 实例传给每个插件的 compiler 和 compilation 对象都是同一个引用，若在一个插件中修改了它们身上的属性，会影响后面的插件
- 找出合适的事件点去完成想要的功能
  - emit 事件发生时，可以读取到最终输出的资源、代码块、模块及其依赖，并进行修改(emit 事件是修改 Webpack 输出资源的最后时机)
  - watch-run 当依赖的文件发生变化时会触发
- 异步的事件需要在插件处理完任务时调用回调函数通知 Webpack 进入下一个流程，不然会卡住

[官网](https://www.webpackjs.com/api/plugins/)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#21" id="21">21. 聊一聊 Babel 原理吧</a>

大多数 JavaScript Parser 遵循 `estree` 规范，Babel 最初基于 `acorn` 项目(轻量级现代 JavaScript 解析器)
Babel 大概分为三大部分：

- 解析：将代码（其实就是字符串）转换成 AST

- 转换：访问 AST 的节点进行变换操作生产新的 AST

- 生成：以新的 AST 为基础生成代码

详细一点：  
 ES6、7 代码输⼊ -> babylon 进⾏解析 -> 得到 AST （抽象语法树）->
plugin ⽤ babel-traverse 对 AST 树进⾏遍历转译 -> 得到新的 AST 树 -> ⽤ babel-generator 通过 AST 树⽣成 ES5 代码

[面试官: 聊一聊 Babel](https://www.cnblogs.com/duxinyi/p/11576583.html)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)

<br/>
<br/>

#### <a href="#22" id="22">22. 在项目中，如何优化 Webpack</a>

**优化开发体验**

##### 优化构建速度

首先说下衡量手段：`speed-measure-webpack-plugin`，监控构建过程中，loader 和 plugin 的执行时长

1. 使用 `高版本` 的 webpack 和 node.js
2. 缩小文件搜索范围

   - 配置 loader 时，通过 `include` 和 `exclude` 缩小命中范围
   - 优化 `resolve.modules` 配置，指明存放第三方模块的绝对路径，减少寻找时间。
   - 优化 `resolve.mainFields` 配置： 只采用 main 字段作为入口文件描述字段 (减少搜索步骤，需要考虑到所有运行时依赖的第三方模块的入口文件描述字段)
   - 优化 `resolve.alias` 配置
   - 优化 `resolve.extensions` 配置
   - 优化 `module.noParse` 配置： 对完全不需要解析的库进行忽略 (不去解析但仍会打包到 bundle 中，注意被忽略掉的文件里不应该包含 import、require、define 等模块化语句)

3. 使用 `DLLPlugin`
   - 使用 DllPlugin 进行分包，使用 DllReferencePlugin(索引链接) 对 manifest.json 引用，让一些基本不会改动的代码先打包成静态资源，避免反复编译浪费时间。
   - HashedModuleIdsPlugin 可以解决模块数字 id 问题
4. 使用 HappyPack（不维护了）、`thread-loader`
5. 使用 `ParallelUglifyPlugin`
6. `充分利用缓存提升二次构建速度`
   - babel-loader 开启缓存
   - terser-webpack-plugin 开启缓存
   - 使用 cache-loader 或者 hard-source-webpack-plugin

##### 优化使用体验

1. 开启自动刷新
2. 开启模块热替换（HMR）
3. 展示构建进度：`progress-bar-webpack-plugin`
4. 优化 webpack 原始的构建输出：`webpack-dashboard`

**优化输出质量**

##### 减少用户能感知到的加载时机，也就是首屏加载速度

1. 区分环境，不同环境使用的配置不相同
2. 压缩代码:

   - 多进程并行压缩

     - webpack-paralle-uglify-plugin
     - uglifyjs-webpack-plugin 开启 parallel 参数 (不支持 ES6)
     - `terser-webpack-plugin` 开启 parallel 参数

   - 通过 mini-css-extract-plugin 提取 Chunk 中的 CSS 代码到单独文件，通过 css-loader 的 minimize 选项开启 cssnano 压缩 CSS。

3. Tree Shaking：删除没用到的代码


    - 打包过程中检测工程中没有引用过的模块并进行标记，在资源压缩时将它们从最终的 bundle 中去掉(只能对 ES6 Modlue 生效) 开发中尽可能使用 ES6 Module 的模块，提高 tree shaking 效率
    - 禁用 babel-loader 的模块依赖解析，否则 Webpack 接收到的就都是转换过的 CommonJS 形式的模块，无法进行 tree-shaking
    - 使用 PurifyCSS(不在维护) 或者 uncss 去除无用 CSS 代码

      - purgecss-webpack-plugin 和 mini-css-extract-plugin 配合使用(建议)

4. 提取公共代码

   - 基础包分离：

     - 使用 html-webpack-externals-plugin，将基础包通过 CDN 引入，不打入 bundle 中
     - 使用 SplitChunksPlugin 进行(公共脚本、基础包、页面公共文件)分离(Webpack4 内置) ，替代了 CommonsChunkPlugin 插件

5. 按需加载

##### 提升流畅度，也就是提升代码性能

1. prepack
2. Scope Hoisting

   - 构建后的代码会存在大量闭包，造成体积增大，运行代码时创建的函数作用域变多，内存开销变大。Scope hoisting 将所有模块的代码按照引用顺序放在一个函数作用域里，然后适当的重命名一些变量以防止变量名冲突
   - 必须是 ES6 的语法，因为有很多第三方库仍采用 CommonJS 语法，为了充分发挥 Scope hoisting 的作用，需要配置 mainFields 对第三方模块优先采用 jsnext:main 中指向的 ES6 模块化语法

3. 图片压缩： imagemin、image-webpack-loader

参考 / 来源：  
[官网](https://www.webpackjs.com/guides/build-performance/)
[Webpack 优化](https://www.jianshu.com/p/4f58b179c626)  
[「吐血整理」再来一打 Webpack 面试题](https://juejin.im/post/5e6f4b4e6fb9a07cd443d4a5)  
[Webpack 优化——将你的构建效率提速翻倍](https://juejin.im/post/5d614dc96fb9a06ae3726b3e)

<br/>
<br/>
