## Vue 源码的获取
- 项目地址：https://github.com/vuejs/vue
- Fork 一份到自己仓库，克隆到本地，可以自己写注释提交到 github
- 为什么分析 Vue 2.6
 - 到目前为止 Vue 3.0 的正式版还没有发布
 - 新版本发布后，现有项目不会升级到 3.0，2.x 还有很长的一段过渡期
 - 3.0 项目地址：https://github.com/vuejs/vue-next
 ## 源码目录结构
 src
 ├─compiler 编译相关 
 ├─core Vue 核心库 
 ├─platforms 平台相关代码 
 ├─server SSR，服务端渲染 
 ├─sfc .vue 文件编译为 js 对象 
 └─shared 公共的代码
## 调试设置 打包
- 打包工具 Rollup
 - Vue.js 源码的打包工具使用的是 Rollup，比 Webpack 轻量
 - Webpack 把所有文件当做模块，Rollup 只处理 js 文件更适合在 Vue.js 这样的库中使用
 - Rollup 打包不会生成冗余的代码
- 安装依赖
```js
npm i
```
- 设置 sourcemap
 - package.json 文件中的 dev 脚本中添加参数 --sourcemap
```js
"dev": "rollup -w -c scripts/config.js --sourcemap --environment TARGET:web- full-dev"
```
- 执行 dev 
 - npm run dev 执行打包，用的是 rollup，-w 参数是监听文件的变化，文件变化自动重新打
包
## 调试
- examples 的示例中引入的 vue.min.js 改为 vue.js
- 打开 Chrome 的调试工具中的 source
## Vue 的不同构建版本
- npm run build 重新打包所有文件

- 官方文档 - 对不同构建版本的解释

- dist\README.md

  |                          | UMD                | CommonJS              | ES Module          |
  | ------------------------ | ------------------ | --------------------- | ------------------ |
  | Full                     | vue.js             | vue.common.js         | vue.esm.js         |
  | Runtime-only             | vue.runtime.js     | vue.runtime.common.js | vue.runtime.esm.js |
  | Full (production)        | vue.min.js         |                       |                    |
  | Runtime-only(production) | vue.runtime.min.js |                       |                    |
### 术语
- 完整版：同时包含编译器和运行时的版本。
- 编译器：用来将模板字符串编译成为 JavaScript 渲染函数的代码，体积大、效率低。
- 运行时：用来创建 Vue 实例、渲染并处理虚拟 DOM 等的代码，体积小、效率高。基本上就是除去编译器的代码
- UMD：UMD 版本通用的模块版本，支持多种模块方式。 vue.js 默认文件就是运行时 + 编译器的UMD 版本
- CommonJS(cjs)：CommonJS 版本用来配合老的打包工具比如 Browserify 或 webpack 1。
- ES Module：从 2.6 开始 Vue 会提供两个 ES Modules (ESM) 构建文件，为现代打包工具提供的版本。
- ESM 格式被设计为可以被静态分析，所以打包工具可以利用这一点来进行“tree-shaking”并将用不到的代码排除出最终的包。
- ES6 模块与 CommonJS 模块的差异
## Runtime + Compiler vs. Runtime-only
```js
// Compiler 
// 需要编译器，把 template 转换成 render 函数 
// const vm = new Vue({ 
    // el: '#app', 
    // template: '<h1>{{ msg }}</h1>', 
    // data: { 
        // msg: 'Hello Vue' 
        // } 
    // }) 
// Runtime 
// 不需要编译器 
const vm = new Vue({ 
    el: '#app', 
    render (h) { 
        return h('h1', this.msg) 
    },
    data: { 
        msg: 'Hello Vue' 
        } 
    })
```
- 推荐使用运行时版本，因为运行时版本相比完整版体积要小大约 30%
- 基于 Vue-CLI 创建的项目默认使用的是 vue.runtime.esm.js
- 通过查看 webpack 的配置文件
```js
vue inspect > output.js
```
- 注意： *.vue 文件中的模板是在构建时预编译的，最终打包后的结果不需要编译器，只需要运行时版本即可

### 阅读源码记录
- el不能是body或者html记录
- 如果没有render,把template转换成render函数
- 如果有render方法,直接调用mount挂载DOM
