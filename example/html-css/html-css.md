#### html

1. h5 新特性

标签：新增语义化标签（aside / figure / section / header / footer / nav 等），增加多媒体标签 video 和 audio，使得样式和结构更加分离
属性：增强表单，主要是增强了 input 的 type 属性；meta 增加 charset 以设置字符集；script 增加 async 以异步加载脚本
存储：增加 localStorage、sessionStorage 和 indexedDB，引入了 application cache 对 web 和应用进行缓存
API：增加拖放 API、地理定位、SVG 绘图、canvas 绘图、Web Worker、WebSocket

2. 前端存储及特点

「cookies」：HTML5 之前本地储存的主要方式，大小只有 4k，HTTP 请求头会自动带上 cookie，兼容性好
「localStorage」：HTML5 新特性，持久性存储，即使页面关闭也不会被清除，以键值对的方式存储，大小为 5M
「sessionStorage」：HTML5 新特性，操作及大小同 localStorage，和 localStorage 的区别在于 sessionStorage 在选项卡(页面)被关闭时即清除，且不同选项卡之间的 sessionStorage 不互通
「IndexedDB」：NoSQL 型数据库，类比 MongoDB，使用键值对进行储存，异步操作数据库，支持事务，储存空间可以在 250MB 以上，但是 IndexedDB 受同源策略限制
「Web SQL」：是在浏览器上模拟的关系型数据库，开发者可以通过 SQL 语句来操作 Web SQL，是 HTML5 以外一套独立的规范，兼容性差

3. href 和 src 的区别

**href**（hyperReference）即超文本引用：当浏览器遇到 href 时，会并行的地下载资源，不会阻塞页面解析，例如我们使用<link>引入 CSS，浏览器会并行地下载 CSS 而不阻塞页面解析. 因此我们在引入 CSS 时建议使用<link>而不是@import
`<link href="style.css" rel="stylesheet" />`

**src**（resource）即资源，当浏览器遇到 src 时，会暂停页面解析，直到该资源下载或执行完毕，这也是 script 标签之所以放底部的原因
`<script src="script.js"></script>`

4. meta 有哪些属性，作用是什么

meta 标签用于描述网页的元信息，如网站作者、描述、关键词，meta 通过 name=xxx 和 content=xxx 的形式来定义信息，常用设置如下

**charset**：定义 HTML 文档的字符集
`<meta charset="UTF-8" >`
**http-equiv**：可用于模拟 http 请求头，可设置过期时间、缓存、刷新
`＜meta http-equiv="expires" content="Wed, 20 Jun 2019 22:33:00 GMT"＞`

**viewport**：视口，用于控制页面宽高及缩放比例
`<meta name="viewport" content="width=device-width, initial-scale=1">`

#### css

1. 清除浮动

清除浮动是为了解决子元素浮动而导致父元素高度塌陷的问题

「1.添加新元素」

```
<div class="parent">
  <div class="child"></div>
  <!-- 添加一个空元素，利用css提供的clear:both清除浮动 -->
  <div style="clear: both"></div>
</div>
```

「2.使用伪元素」

```
/* 对父元素添加伪元素 */
.parent::after{
  content: "";
  display: block;
  height: 0;
  clear:both;
}
```

「3.触发父元素 BFC」

```
/* 触发父元素BFC */
.parent {
  overflow: hidden;
  /* float: left; */
  /* position: absolute; */
  /* display: inline-block */
  /* 以上属性均可触发BFC */
}
```

2. BFC

BFC 是页面上的一个隔离的独立容器，不受外界干扰或干扰外界
触发:
float 不为 none
overflow 的值不为 visible
position 为 absolute 或 fixed
display 的值为 inline-block 或 table-cell 或 table-caption 或 grid

应用:
「清除浮动」：BFC 内部的浮动元素会参与高度计算，因此可用于清除浮动，防止高度塌陷
「避免某元素被浮动元素覆盖」：BFC 的区域不会与浮动元素的区域重叠
「阻止外边距重叠」：属于同一个 BFC 的两个相邻 Box 的 margin 会发生折叠，不同 BFC 不会发生折叠

3. 水平居中

**flex 布局**

```
.parent {
    display: flex;
    justify-content:center;
    align-items:center;
}
```

**绝对定位**
父元素相对定位, 子元素绝对定位

```
.parent { position: relative }

//1
.child {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%,-50%)
}

//2
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  margin-top: -50px; /* 自身 height 的一半 */
  margin-left: -50px; /* 自身 width 的一半 */
}

//3
.child {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
}
```
