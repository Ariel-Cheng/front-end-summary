# 前端知识总结 -- 面经汇总  

🎉问题 + 答案
🎉欢迎提问


## 基础知识

### JavaScript

1. 同源策略及跨域请求的几种方法和原理。
2. 什么是`CORS`，有什么作用？
3. 介绍一下事件委托及其使用场景。
4. 前端模块化的几种标准及其比较。
5. 模块加载器的实现原理(`SeaJS`)
6. 什么是`Session`和 `Cookie`，它们的作用是什么？
7. 如何用`JavaScript`操作`Cookie`？
8. 什么情况下会导致客户端`JavaScript`读取`Cookie`失败？
9. 前端实现动画效果的几种方法？
10. `this`的概念，基本用法。
11. `apply`和`call`的作用以及区别。
12. `bind`的作用，如何用`apply`或`call`模拟`bind`？
13. `JavaScript`原型链及`JavaScript`如何实现继承、类？
14. 闭包的概念，作用，使用场景以及可能造成的不良后果。
15. 常见数据结构(栈，队列，链表，二叉树，图等)和算法(排序算法)的概念以及`JavaScript`的实现。
16. 事件冒泡和事件捕获的区别。
17. 如何进行浏览器兼容性检测(客户端检测)？
18. 如何进行`JavaScript`代码的测试？
19. 什么是变量自举(变量名提升、函数名提升)？
20. `"use strict"`的作用？
21. `AJAX`请求的细节和原理？
22. `AJAX`、`JSON`技术的优缺点。
23. 函数式编程是什么？
24. 什么是函数柯里化(Currying)？
25. `jQuery`链式调用的原理
26. 解决异步回调地狱(`callback hell`)有哪些方法？
27. `Promise`的原理。
28. 介绍一下什么是`generator`，如何利用`generator`实现异步流程控制？
29. 说几个`ES6`的新特性。
30. 客户端存储及他们的异同(例如：Cookie, SessionStorage和localStorage、webstorage等)
31. `DOM1` `DOM2` `DOM3`都有什么不同。
32. 什么是`XSS`，如何在实际项目中防范？
33. 什么是`XSRF`， 如何在实际项目中防范？
34. `Object` ，`Array`和`String`对象的常用方法有哪些？
35. 数组如何实现去重、数组判断、求交集、求并集、求差集和洗牌(`shuffle`)？
36. `JavaScript`的垃圾回收机制
37. 常见的`JavaScript`设计模式
38. 客户端如何与服务器时间同步？
39. 什么是`JavaScript`中的类数组对象(`arguments`对象)？
40. 在浏览器地址栏按回车、F5、Ctrl+F5刷新网页的区别
41. 如何判断两个对象是否相等？
42. 什么是`IIFE`(Immediately Invoked Function Expression，立即执行函数表达式)？有什么作用？解释下为什么接下来这段代码不是 IIFE(立即调用的函数表达式)：function foo(){ }();要做哪些改动使它变成 IIFE?
43. 变量的`null`，`undefined`和`undeclared`的区别是什么？如何检测它们？
44. 原生对象(native object)和宿主对象(host object)有什么区别？
45. 浏览器多个标签间如何通信(阿里)？
46. `attribute`和 `property`的区别是什么(核心)？
47. `document load event` 和 `document DOMContentLoaded event`之间的区别是什么？
48. `== `与` ===`的区别。
49. 解释`JavaScript`异步实现的原理。
50. `escape()`, `decodeURIComponent()`, `decodeURI()`之间的区别是什么？
51. NodeJS如何利用CPU的多核，增强性能？
52. `ExpressJS `中间件原理。
53. script的三种加载方式。
54. typeof和instanceof区别。

### CSS

1. 圣杯布局的原理和实现。
2. 盒子模型。
3. 如何通过`CSS`改变盒子模型的布局？
4. `CSS`几种定位方式的概念和区别。
5. `CSS`实现动画有几种方式？
6. `CSS`不同选择器的权重。
7. ·`flexbox`布局的基本原理和用法。
8. 块级元素和行内元素的区别？
9. 浮动的原理。
10. 清除浮动有哪几种方式以及它们的优缺点？
11. 如何利用`CSS`实现垂直居中？
12. `CSS`预处理器(`sass`， `less`， `stylus`)的优缺点。
13. `base64`的原理及优缺点
14. `reset.css`和`normalize.css`的区别。
15. `html`标签`<link href=""/>`和`css`文件中的`@import`指令的区别。
16. `CSS`字体单位`rem`、 `em` 和`px` 之间的区别和联系？
17. `CSS3`新特性有哪些？
18. 列出你所知道可以改变页面布局的属性。

### HTML

1.  `<script>`，`<script defer = "defer">`，`<script async = "async">`它们之间加载脚本的区别是什么？
2.  `doctype`的作用。
3.  HTML中标准模式和怪异模式有什么不同？
4.  为什么要少用`iframe`？
5.  对`HTML`语义化的理解？
6.  盒模型在浏览器兼容方面的异同。
7.  `HTML5`的新特性。
8.  `HTML5`中引进`data-`有什么作用。
9.  `canvas`的性能优化。
10.  浏览器重绘（repaint）和重排（reflow）

### HTTP

1. `HTTPS`的原理。
2. `HTTP`和`HTTPS`的区别。
3. `TCP/IP`四层模型。
4. `TCP`，`IP`， `HTTP`的相关概念。
5. `TCP`三次握手的过程。
6. `TCP`和`UDP`的区别。
7. `TCP`的拥塞控制。
8. `HTTP`常见的状状态码。
9. `HTTP`头部包含的信息及作用。
10. `HTTP`缓存控制的原理。
11. `SEO`是什么，有什么最佳实践？
12. `POST`和`GET`的异同

### 框架

1. `AngularJS`， `ReactJS`，`VueJS`各有什么优缺点，适用什么样的开发场景？
2. `React`组件的生命周期。
3. `React` 渲染网页的流程。
4. `React`的 `DOM-DIFF`算法的原理
5. `Redux`是干什么的？
6. `Redux`的三大原是什么？

### 自动化工具

1. `gulp` 和`grunt`的区别
2. `webpack`打包工具的运行原理
3. `npm script`。

## 综合性问题

1. 一个页面从输入 URL 到页面加载完的过程中都发生了什么事情？
2. `html`页面的渲染过程。
3. 你是如何了解到并且学习一门技术的？
4. 你平时是如何学习前端的？
5. 你未来三年的计划。
6. 讲一下你读过的和正在读或者研究的关于前端技术的书或者技术。
7. 你对前端的理解？你为什么学前端？
8. “渐进增强”和“优雅降级”指的是什么？
9. 什么是“FOUC”及如何避免。
10. 什么是面向对象编程及面向过程编程，它们的异同和优缺点？
11. 面向对象的三大原则是什么？
12. 你在平时的学习和工作中有遇到过什么问题，你是如何解决的？
13. 对于现在前端发展日新月异，各种框架层出不穷，你是怎么看待的？

   提示：围绕基础最重要，不要盲目更新，适合项目需求才是最好的。
