**webpack执行流程**

递归构建依赖关系图

1. 参数解析
2. 找到入口文件
3. 调用 Loader 编译文件
4. 遍历 AST，收集依赖
5. 生成 Chunk
6. 输出文件

**loader**
将不同类型的文件转换为 webpack 可识别的模块
工作原理:
webpack 只能直接处理 javascript 格式的代码。任何非 js 文件都必须被预先处理转换为 js 代码，才可以参与打包。loader（加载器）就是这样一个代码转换器。它由 webpack 的 loader runner 执行调用，接收原始资源数据作为参数（当多个加载器联合使用时，上一个loader的结果会传入下一个loader），最终输出 javascript 代码（和可选的 source map）给 webpack 做进一步编译。

如何开发一个loader:
当只有一个 loader 应用于资源文件时，它接收源码作为参数，输出转换后的 js 代码。
```
// loaders/simple-loader.js
module.exports = function loader (source) {
    console.log('simple-loader is working');
    return source;
}
```
https://champyin.com/2020/01/28/%E6%8F%AD%E7%A7%98webpack-loader/



**plugin**
扩展和开发webpack的编译过程, 加入自定义的构建行为，可以让webpack执行更广泛的任务
原理:
站在代码逻辑的角度就是：webpack 在编译过代码程中，会触发一系列 Tapable 钩子事件，插件所做的，就是找到相应的钩子，往上面挂上自己的任务，也就是注册事件，这样，当 webpack 构建的时候，插件注册的事件就会随着钩子的触发而执行了。

如何开发一个plugin:

1. 一个命名的 Javascript 方法或者 JavaScript 类。
2. 它的原型上需要定义一个叫做 apply 的方法。
3. 注册一个事件钩子。
4. 操作webpack内部实例特定数据。
5. 功能完成后，调用webpack提供的回调。 

```
// plugins/MyPlugin.js
class MyPlugin {
  apply(compiler) {
    compiler.hooks.done.tap('My Plugin', (stats) => {
      console.log('Bravo!');
    });
  }
}

module.exports = MyPlugin;
```
https://champyin.com/2020/01/12/%E6%8F%AD%E7%A7%98webpack-plugin/


**hmr**
https://juejin.cn/post/6844904008432222215#comment
https://zhuanlan.zhihu.com/p/30669007


**webpack优化**
加快编译速度

1. 更新webpack，node, yarn/npm版本， 有更好的解析速度
2. cache缓存配置，加快二次构建速度
3. 减少非必要的loader,plugin使用
4. 优化resolve配置, alias别名，exclude/include指定范围,extensions后缀指定
5. 多线程thread-loader, 把一些耗时的loader放到其他线程中
6. 区分环境

减少包体积

1. terser-webpack-plugin 压缩js
2.   css-minimizer-webpack-plugin 压缩css
3. splitChunks分离代码，抽取公共库的代码，不再重复引入
4. mini-css-extract-plugin 分离css,且能做到按需加载
5. 最小化entry chunk, 为运行时代码创建一个额外的chunk
6. Tree Shaking 按需引入
7. 静态资源放到CDN中

加快加载速度

1. 按需加载，使用import()语法动态导入
2. 浏览器缓存，减少非必要的文件hash变动
3. CDN


webpack和vite的区别

webpack:
从一个entry.js入口文件开始,将其依赖的所有js或者其他assets通过loader打包成一个文件,随后这个打包后的文件将被从server传递到客户端浏览器运行。
因为这样的处理原则,⽂件更改后需要整个⽂件链路重新编译,这就是为什么更改可能需要长达 10 秒才能反映在浏览器中,更新速度会随着应用体积增长而直线下降

vite
在开发阶段基于浏览器原⽣ESM的⽀持实现了nobundle服务,另⼀⽅⾯借助esbuild超快的编译速度来做第三⽅库构建和TS/JSX语法编译,从⽽能够有效提⾼开发效率。文件修改后,只需要让浏览器重新获取这个ESM⽂件即可,这种方式称为no-bundle

vite对标webpack就是为了简化、增速，继⽽达到提效的⽬的。