#### js

1. 防抖节流

「防抖 debounce」,在事件被触发 n 秒后再执行回调，如果在这 n 秒内又被触发，则重新计时
场景: 文本编辑器实时保存, 登录,输入框等按钮避免发送多次请求

「节流 throttle」，规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效。
场景: scroll 滚动事件, resize 事件

2. 数组去重

使用 Set
[...new Set(arr)]

filter

```
function unique(array) {
  var res = array.filter(function (item, index, array) {
    return array.indexOf(item) === index;
  });
  return res;
}
```

3. 柯里化  
   把接受「多个参数」的函数变换成接受一个「单一参数」的函数，并且返回接受「余下参数」返回结果的一种应用

```
let currying = (fn, ...args) => {
  return fn.length > args.length
    ? (...args) => currying(fn, ...args, ...arguments)
    : fn(...args);
};

let addSum = (a, b, c) => a + b + c;
let add = currying(addSum);
add(1)(2)(3);
add(1,2)(3)
```

4. 深拷贝
   实现深拷贝的几个要点
   递归遍历
   环检查(循环引用),使用 WeakMap 存储拷贝过的对象,每次拷贝前像 WeakMap 询问,如果有就直接拿
   需要忽略原型
   考虑正则,Date 等特殊类型

5. 判断数据类型
   Object.prototype.toString.call(obj)

6. this 的指向问题

7. new 实现原理

- 创建一个新对象，这个对象的**proto**要指向构造函数的原型对象
- 执行构造函数
- 返回值为 object 类型则作为 new 方法的返回值返回，否则返回上述全新对象

```
function _new(fn, ...arg) {
    const obj = Object.create(fn.prototype);
    const ret = fn.apply(obj, arg);
    return ret instanceof Object ? ret : obj;
}
```

8. instanceof
   用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上。
   obj instanceof constructor
   object 某个实例对象
   construtor 某个构造函数

9. sleep 函数

```
4方式
//Promise
const sleep = time => {
  return new Promise(resolve => setTimeout(resolve,time))
}
sleep(1000).then(()=>{
  console.log(1)
})

//Generator
function* sleepGenerator(time) {
  yield new Promise(function(resolve,reject){
    setTimeout(resolve,time);
  })
}
sleepGenerator(1000).next().value.then(()=>{console.log(1)})

//async
function sleep(time) {
  return new Promise(resolve => setTimeout(resolve,time))
}
async function output() {
  let out = await sleep(1000);
  console.log(1);
  return out;
}
output();

//ES5
function sleep(callback,time) {
  if(typeof callback === 'function')
    setTimeout(callback,time)
}

function output(){
  console.log(1);
}
sleep(output,1000);
```

10. es5 的继承和 es6 的继承区别
    继承机制不同: 子类 this 的生成顺序不同, es6 要先调用父类的构造函数完成塑造,再通过子类自己添加实例属性和方法,所以要先 super(), es5 先创造子类的实例对象,再通过原型链继承父类的属性方法
    **proto**的指向不同: es6 子类的**proto**指向父类, es5 指向 Function.prototype

11. 事件循环

js 单线程，如果一直同步执行的话容易造成阻塞，需要异步任务来处理阻塞(http 请求，I/O 操作)
**同步异步**
同步任务: 常规的 js 代码, 在主线程中执行
异步任务: setTimeout, Promise, async/await 等代码，会放入事件队列中
调度规则: js 执行主线程中的同步代码，执行完成后，js 引擎空闲了，会去读取任务队列中的事件，放到主线程中执行。 这个过程不断重复, 形成了 Event Loop
**宏微任务**
宏任务: setTimeout, setInerval,
微任务: Promise, async/await, process.nextTick
调度规则: 执行主线程中的代码，相当于开始一个宏任务, 执行完成后, 看任务队列是否有微任务(Promise.prototype.then)，如果有则执行, 此时一轮循环结束。setTimeout 注册的事件是一个宏任务，将在下一轮循环开始

#### 浏览器

1. 渲染机制

HTML 和 CSS 经过各自解析，生成 DOM 树和 CSSOM 树
合并成为渲染树
根据渲染树进行布局
最后调用 GPU 进行绘制，显示在屏幕上

**回流(重排)**
当元素的尺寸或者位置发生了变化，就需要重新计算渲染树，这就是回流
DOM 元素的几何属性(width/height/padding/margin/border)发生变化时会触发回流
DOM 元素移动或增加会触发回流
读写 offset/scroll/client 等属性时会触发回流
调用 window.getComputedStyle 会触发回流

**重绘**
DOM 样式发生了变化，但没有影响 DOM 的几何属性时，会触发重绘

**减少回流**
使用 class 替代 style，减少 style 的使用
使用 resize、scroll 时进行防抖和节流处理，这两者会直接导致回流
使用 visibility 替换 display: none，因为前者只会引起重绘，后者会引发回流
批量修改元素时，可以先让元素脱离文档流，等修改完毕后，再放入文档流
避免触发同步布局事件，我们在获取 offsetWidth 这类属性的值时，可以使用变量将查询结果存起来，避免多次查询，每次对 offset/scroll/client 等属性进行查询时都会触发回流
对于复杂动画效果,使用绝对定位让其脱离文档流，复杂的动画效果会频繁地触发回流重绘，我们可以将动画元素设置绝对定位从而脱离文档流避免反复回流重绘。

**GPU 加速**
「优点」：使用 transform、opacity、filters 等属性时，会直接在 GPU 中完成处理，这些属性的变化不会引起回流重绘
「缺点」：GPU 渲染字体会导致字体模糊，过多的 GPU 处理会导致内存问题

2. 加快首屏渲染速度

「优化文件大小」：HTML 和 CSS 的加载和解析都会阻塞渲染树的生成，从而影响首屏展示速度，因此我们可以通过优化文件大小、减少 CSS 文件层级的方法来加快首屏速度
「避免资源下载阻塞文档解析」：浏览器解析到<script>标签时，会阻塞文档解析，直到脚本执行完成，因此我们通常把<script>标签放在底部，或者加上 defer、async 来进行异步下载

3. 浏览器缓存策略

强缓存(不要向服务器询问的缓存)
「Expires」
即过期时间，例如「Expires: Thu, 26 Dec 2019 10:30:42 GMT」表示缓存会在这个时间后失效，这个过期日期是绝对日期，如果修改了本地日期，或者本地日期与服务器日期不一致，那么将导致缓存过期时间错误。

「Cache-Control」
HTTP/1.1 新增字段，Cache-Control 可以通过 max-age 字段来设置过期时间，例如「Cache-Control:max-age=3600」除此之外 Cache-Control 还能设置 private/no-cache 等多种字段

协商缓存(需要向服务器询问缓存是否已经过期)
「Last-Modified」
即最后修改时间，浏览器第一次请求资源时，服务器会在响应头上加上 Last-Modified ，当浏览器再次请求该资源时，浏览器会在请求头中带上 If-Modified-Since 字段，字段的值就是之前服务器返回的最后修改时间，服务器对比这两个时间，若相同则返回 304，否则返回新资源，并更新 Last-Modified

「ETag」
HTTP/1.1 新增字段，表示文件唯一标识，只要文件内容改动，ETag 就会重新计算。缓存流程和 Last-Modified 一样：服务器发送 ETag 字段 -> 浏览器再次请求时发送 If-None-Match -> 如果 ETag 值不匹配，说明文件已经改变，返回新资源并更新 ETag，若匹配则返回 304
ETag 比 Last-Modified 更准确：如果我们打开文件但并没有修改，Last-Modified 也会改变，并且 Last-Modified 的单位时间为一秒，如果一秒内修改完了文件，那么还是会命中缓存


#### 前端安全

**csrf:**
是一种挟制用户在当前已登录的 Web 应用程序上执行非本意的操作的攻击方法。
假如黑客在自己的站点上放置了其他网站的外链，例如"www.weibo.com/api，默认情况下，浏览器会带着weibo.com的cookie访问这个网址，如果用户已登录过该网站且网站没有对CSRF攻击进行防御，那么服务器就会认为是用户本人在调用此接口并执行相关操作，致使账号被劫持。
防御方法:
验证 Token：浏览器请求服务器时，服务器返回一个 token，每个请求都需要同时带上 token 和 cookie 才会被认为是合法请求
验证 Referer：通过验证请求头的 Referer 来验证来源站点，但请求头很容易伪造
设置 SameSite：设置 cookie 的 SameSite，可以让 cookie 不随跨域请求发出，但浏览器兼容不一

**xss:**
指的是通过利用网页开发时留下的漏洞，注入恶意指令代码到网页，使用户加载并执行攻击者恶意制造的网页程序。常见的例如在评论区植入 JS 代码，用户进入评论页时代码被执行，造成页面被植入广告、账号信息被窃取
「存储型」：即攻击被存储在服务端，常见的是在评论区插入攻击脚本，如果脚本被储存到服务端，那么所有看见对应评论的用户都会受到攻击。
「反射型」：攻击者将脚本混在 URL 里，服务端接收到 URL 将恶意代码当做参数取出并拼接在 HTML 里返回，浏览器解析此 HTML 后即执行恶意代码
「DOM 型」：将攻击脚本写在 URL 中，诱导用户点击该 URL，如果 URL 被解析，那么攻击脚本就会被运行。和前两者的差别主要在于 DOM 型攻击不经过服务端
防御方法:
「输入检查」：对输入内容中的<script><iframe>等标签进行转义或者过滤
「设置 httpOnly」：很多 XSS 攻击目标都是窃取用户 cookie 伪造身份认证，设置此属性可防止 JS 获取 cookie
「开启 CSP」，即开启白名单，可阻止白名单以外的资源加载和运行

#### 算法

排序算法

**冒泡**

```
function bubbleSort(arr){
  for(let i = 0; i < arr.length; i++) {
    for(let j = 0; j < arr.length - i - 1; j++) {
      if(arr[j] > arr[j+1]) {
        let temp = arr[j]
        arr[j] = arr[j+1]
        arr[j+1] = temp
      }
    }
  }
  return arr
}
```

**快速排序**
选取基准元素
比基准元素小的元素放到左边，大的放右边
在左右子数组中重复步骤一二，直到数组只剩下一个元素
向上逐级合并数组

**归并排序**
归并排序和快排的思路类似，都是递归分治，区别在于快排边分区边排序，而归并在分区完成后才会排序

**堆排序**
堆是一棵特殊的树, 只要满足这棵树是完全二叉树和堆中每一个节点的值都大于或小于其左右孩子节点这两个条件, 那么就是一个堆, 根据堆中每一个节点的值都大于或小于其左右孩子节点, 又分为大根堆和小根堆

