# babel
是一个javascript的编译器
用于将es2015+代码向后兼容

工作原理: parse(解析) transform(转换) generate(生成)

@babel/preset-env: 将新语法转换成旧语法,比如class,const,let,箭头函数

@babel/polyfill: 抹平新api, 比如Promise, Array.includes方法等

@babel/plugin-transform-runtime: 对转译过程中产生的辅助函数的复用, 解决转译api层出现的全局污染


配置文件 babel.config.js

```
module.exports = {
  presets: [["@babel/preset-env"]],
  plugins: [
    [
      "@babel/plugin-transform-runtime",
      {
        corejs: { version: 3 },
      },
    ],
  ],
};
```

参考 
https://www.zoo.team/article/babel
https://juejin.cn/post/6844903956905197576#comment