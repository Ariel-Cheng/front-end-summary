### HTML

1.  `<script>`，`<script defer = "defer">`，`<script async = "async">`它们之间加载脚本的区别是什么？

  答案：javascript脚本有三种加载方式，分别为`<script>`，`<script defer = "defer">`，`<script async = "async">`。

  ![三种脚本加载的异同](../images/三种加载的异同.png)
  * `<script>`: 脚本的获取和执行是同步的。此过程中页面被阻塞，停止解析。
  * `<script defer = "defer">`：脚本的获取是异步的，执行是同步的。脚本加载不阻塞页面的解析，脚本在获取完后并不立即执行，而是等到DOMready之后才开始执行。
  * `<script async = "async">`: 脚本的获取是异步的，执行是同步的。但是和`<script defer = "defer">`的不同点在于脚本获取后会立刻执行，这就会造成脚本的执行顺序和页面上脚本的排放顺序不一致，可能造成脚本依赖的问题。

  参考：

  - [图解script的三种加载方式](http://natumsol.github.io/2015/11/11/loading-script/)

2.  `doctype`的作用。

  答案：DOCTYPE是用来声明文档类型和DTD规范的，一个主要的用途便是文件的合法性验证。告诉浏览器它使用了什么文档类型。它指出阅读程序应该用什么 规则集来解释文档中的标记。 如果文件代码不合法，那么浏览器解析时便会出一些差错。浏览器也会根据不同的DOCTYPE选择不同的渲染方法。DTD（document type definition，文档类型定义）是一系列的语法规则， 用来定义XML或(X)HTML的文件类型。浏览器会使用它来判断文档类型， 决定使用何种协议来解析，以及切换浏览器模式。XHTML中有三种文档类型，包括过度型、严格型、框架型。随着XML的流行，HTML推出了XHTML标准，其中严格模式严格遵守了XML的规范，例如属性必须有值、标签必须闭合等，同时也抛弃了一些不推荐的标签。而XHTML过度版本，则稍微比严格模式松散些，一些不推荐的标签依然可用外。当页面有框架时，则应该使用框架型。再就是HTML5的版本。使用HTML5的Doctype会默认触发标准模式。

  ```HTML
  /*HTML 5:会默认触发标准模式*/
  <!DOCTYPE html>
  /*HTML 4.01 Strict:严格模式:该 DTD 包含所有 HTML 元素和属性，但不包括一些不推荐的标签（比如 font）。不允许框架集（Framesets）。*/
  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
  /*HTML 4.01 Transitional:过度版本:该 DTD 包含所有 HTML 元素和属性，包括一些不推荐的标签（比如 font）。不允许框架集（Framesets）。*/
  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  ```
  如今只有老的浏览器需要一个特定的doctype（文档类型）。浏览器如果不知道doctype，就会很简单的以标准模式对包含的标签进行渲染。

  参考：

    - [DOCTYPE的作用：文档类型与浏览器模式](http://harttle.com/2016/01/22/doctype.html)

3.  HTML中标准模式（standards mode）和怪异模式（quirks mode）（也称怪异模式、兼容模式等）有什么不同？

  答案：quirks mode和strict mode是浏览器解析css的两种模式，或者可以称之为解析方法。目前正在使用的浏览器这两种模式都支持。**标准模式渲染**：浏览器根据标准规约来渲染页面。**混杂模式渲染**：浏览器采用更加宽松的、向后兼容的方式来渲染页面。会模仿旧浏览器的行为,兼容新的标准特性,可能会很怪异。

  由于历史的原因，各个浏览器在对页面的渲染上存在差异，甚至同一浏览器在不同版本中，对页面的渲染也不同。在W3C标准出台以前，浏览器在对页面的渲染上没有统一规范，产生了差异(Quirks mode或者称为Compatibility Mode)；由于W3C标准的推出，浏览器渲染页面有了统一的标准(CSScompat或称为Strict mode也有叫做Standars mode)，这就是二者最简单的区别。quirks mode和strict mode最大的不同就是提现在对盒模式的解释上 ，这也是我们在js里要注意的地方.区别就是产生在width属性上：

  * IE6 IE7 在怪异模式下 盒模型是一模一样的   即width=width
  * IE6 IE7 在标准模式下 盒模型也是一模一样的 即width=width+padding+border

  W3C标准推出以后，浏览器都开始采纳新标准，但存在一个问题就是如何保证旧的网页还能继续浏览，在标准出来以前，很多页面都是根据旧的渲染方法编写的，如果用的标准来渲染，将导致页面显示异常。为保持浏览器渲染的兼容性，使以前的页面能够正常浏览，浏览器都保留了旧的渲染方法（如：微软的IE）。这样浏览器渲染上就产生了Quircks mode和Standars mode，两种渲染方法共存在一个浏览器上。火狐一直工作在标准模式下，但IE（6，7，8）标准模式与怪异模式差别很大，主要体现在对盒子模型的解释上，这个很重要。 那么浏览器究竟该采用哪种模式渲染呢？这就引出的DTD，既是网页的头部声明，浏览器会通过识别DTD而采用相对应的渲染模式。

  1. 浏览器要使老旧的网页正常工作，但这部分网页是没有doctype声明的，所以浏览器对没有doctype声明的网页采用quirks mode解析。
  2. 对于拥有doctype声明的网页，什么浏览器采用何种模式解析，这里有一张详细列表可参考：[点击这里](https://hsivonen.fi/doctype/)。
  3. 对于拥有doctype声明的网页，这里有几条简单的规则可用于判断：对于那些浏览器不能识别的doctype声明，浏览器采用strict mode解析。
  4. 在doctype声明中，没有使用DTD声明或者使用HTML4以下（不包括HTML4）的DTD声明时，基本所有的浏览器都是使用quirks mode呈现，其他的则使用strict mode解析。
  5. 可以这么说，在现有有doctype声明的网页，绝大多数是采用strict mode进行解析的。
  6. 在ie6中，如果在doctype声明前有一个xml声明(比如:<?xml version=”1.0″ encoding=”iso-8859-1″?>)，则采用quirks mode解析。这条规则在ie7中已经移除了。

  参考：

    - [浏览器的两种模式quirks mode 和strict mode](https://my.oschina.net/dongqianlin/blog/73991?p={{currentPage-1}})

4.  为什么要少用`iframe`？

  答案：
  1. iframe的创建比其它包括scripts和css的 DOM 元素的创建慢了 1-2 个数量级。
  2. iframe会阻塞主页面的Onload事件:window 的 onload 事件需要在所有 iframe 加载完毕后(包含里面的元素)才会触发。在 Safari 和 Chrome 里，通过 JavaScript 动态设置 iframe 的 SRC 可以避免这种阻塞情况。
  3. iframe和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载:有人可能希望 iframe 会有自己独立的连接池，但不是这样的。绝大部分浏览器，主页面和其中的 iframe 是共享这些连接的。这意味着 iframe 在加载资源时可能用光了所有的可用连接，从而阻塞了主页面资源的加载。如果 iframe 中的内容比主页面的内容更重要，这当然是很好的。但通常情况下，iframe 里的内容是没有主页面的内容重要的。这时 iframe 中用光了可用的连接就是不值得的了。一种解决办法是，在主页面上重要的元素加载完毕后，再动态设置 iframe 的 SRC。

  参考：

    - [为什么要少用 Iframe ?](http://itindex.net/blog/2012/06/27/1340767816866.html)

5.  对`HTML`语义化的理解？

  答案：用正确的标签表达正确的内容，可以增强网页的易用性（如障碍人士访问等）和搜索引擎的爬取和检索。便于开发者阅读和写出更优雅的代码。HTML5新增的语义化标签如header\section\article\footer等。

  1. 为了在没有CSS的情况下，页面也能呈现出很好地内容结构、代码结构:为了裸奔时好看；
  2. 用户体验：例如title、alt用于解释名词或解释图片信息、label标签的活用；
  3. 有利于SEO：和搜索引擎建立良好沟通，有助于爬虫抓取更多的有效信息：爬虫依赖于标签来确定上下文和各个关键字的权重；
  4. 方便其他设备解析（如屏幕阅读器、盲人阅读器、移动设备）以意义的方式来渲染网页；
  5. 便于团队开发和维护，语义化更具可读性，是下一步吧网页的重要动向，遵循W3C标准的团队都遵循这个标准，可以减少差异化。

  HTML语义化标签
  1. header 元素代表“网页”或“section”的头部部分(页眉)。通常包含h1-h6元素或hgroup，作为整个页面或者一个内容块的标题。也可以包裹一节的目录部分，一个搜索框，一个nav，或者任何相关logo。整个页面没有限制header元素的个数，可以拥有多个，可以为每个内容块增加一个header元素。
  2. footer元素代表“网页”或“section”的页脚，通常含有该节的一些基本信息，譬如：作者，相关文档链接，版权资料。如果footer元素包含了整个节，那么它们就代表附录，索引，提拔，许可协议，标签，类别等一些其他类似信息。没有个数限制，除了包裹的内容不一样，其他跟header类似.
  3. hgroup元素代表“网页”或“section”的标题，当元素有多个层级时，该元素可以将h1到h6元素放在其内，譬如文章的主标题和副标题的组合.
  4. nav元素代表页面的导航链接区域。用于定义页面的主要导航部分。
  5. aside元素被包含在article元素中作为主要内容的附属信息部分，其中的内容可以是与当前文章有关的相关资料、标签、名次解释等。（特殊的section）在article元素之外使用作为页面或站点全局的附属信息部分。最典型的是侧边栏，其中的内容可以是日志串连，其他组的导航，甚至广告，这些内容相关的页面。
  6. section元素代表文档中的“节”或“段”，“段”可以是指一篇文章里按照主题的分段；“节”可以是指一个页面里的分组。section通常还带标题，虽然html5中section会自动给标题h1-h6降级，但是最好手动给他们降级.article、nav、aside可以理解为特殊的section，所以如果可以用article、nav、aside就不要用section，没实际意义的就用div
  7. article元素最容易跟section和div容易混淆，其实article代表一个在文档，页面或者网站中自成一体的内容，其目的是为了让开发者独立开发或重用。譬如论坛的帖子，博客上的文章，一篇用户的评论，一个互动的widget小工具。（特殊的section）除了它的内容，article会有一个标题（通常会在header里），会有一个footer页脚。
  8. address代表区块容器，必须是作为联系信息出现，邮编地址、邮件地址等等,一般出现在footer。
  9. h1-h6因为hgroup，section和article的出现，h1-h6定义也发生了变化，允许一张页面出现多个h1。
  10. time元素也是文本标签，因为是全新的标签，所以我们单独来介绍。time元素用来标记一篇文章的发布时间。
  ```html
  <header>
      <hgroup>
          <h1>网站标题</h1>
          <h1>网站副标题</h1>
      </hgroup>
  </header>
  <nav>
      <ul>
          <li>HTML 5</li>
          <li>CSS3</li>
          <li>JavaScript</li>
      </ul>
  </nav>  
  <section>
      <h1>section是啥？</h1>
      <article>
          <h1>一篇文章</h1>
          <p>内容</p>
          <aside>
              <h1>作者简介</h1>
              <p>小北，前端一枚</p>
          </aside>
          <footer>
              <p><small>版权：html5jscss网所属，作者：小北</small></p>
          </footer>
      </article>
  </section>
  ```

6.  盒模型在浏览器兼容方面的异同。

  答案：浏览器的盒子模型指的是它的盒子宽度需要包括内容区宽度、内外边距和边框大小，高度类似。一般设置宽度默认是对应内容区宽度。

  兼容性方面：在IE7以前的版本中设置宽度是包括：内边距+边框+内容区的。IE7之后跟其它浏览器一样，都是只对应内容区的宽度。可以通过修改box-sizing:border-box来修改。让设置宽度等于内容区和边框和内边距之和。

7.  `HTML5`的新特性。

  答案：
  * HTML5新增的特性与API 语义化标签：提升Web的可用性，利于SEO和屏幕阅读器；一般有header、footer、nav、article、section等。
  * 新的音视频：HTML中包含audio和video标签，可以播放视频和音评，不过格式有限制。
  * 新的Doctype、`<figure>`新的图形元素、`<small>`重新定义：小字、`<script>``<link>`无需指定type、`<ul contenteditable="true">`内容可编辑、`<input id="email" name="email" type="email" />`内置表单验证 email、占位符(Placeholders)、本地存储(Local Storage)、`<video preload controls>`显示控制条、mark元素高亮、data自定义属性、
  * Form API：增强了form表单，比如增加了input的type类型，number/tel/range等;

  *不属于html5新特新*
  * Geolocation：提供地理位置的API，获取用户的地理位置信息。
  * WebSocket：提供Websock的API，使得web可以实时的接受服务器响应。
  * Communication：HTML5中提供了CORS，可以实现跨域资源共享；还有实现了跨文档消息传输，postMessage。
  * Webworkers：Webworkers使得在浏览器端也可以实现多线程的应用。
  * WebStorage：Webstorage是为了减少http请求，在用户客户端实现缓存数据，包括localStorage和sessionStorage，还有indexDB等。
  * OffineWeb：浏览器借用WebStorage可以实现一些简单的离线应用，比如读写邮件、编辑文档、创建todo等

  参考：

    - [你必须知道的28个HTML5特征、窍门和技术](http://www.zhangxinxu.com/wordpress/2010/08/%E7%BF%BB%E8%AF%91-%E4%BD%A0%E5%BF%85%E9%A1%BB%E7%9F%A5%E9%81%93%E7%9A%8428%E4%B8%AAhtml5%E7%89%B9%E5%BE%81%E3%80%81%E7%AA%8D%E9%97%A8%E5%92%8C%E6%8A%80%E6%9C%AF/)

8.  `HTML5`中引进`data-`有什么作用。

  答案：data-是HTML5新增的一个自定义属性。用以方便开发者在HTML中自定义一些属性来存储和操作数据，同时又不违背HTML的规范，不会带来一些副作用。data-自定义属性非常简单，如下例：
  ```html
  <article  id="electriccars"  data-columns="3"  data-index-number="12314"  data-parent="cars">...</article>。
  ```
  通过JS去获取自定义属性非常简单，可以通过 getAttribute()传递全部名称来获取，也可以使用 dataset 属性集来获取。如：
  ```js
  var article = document.querySelector('#electriccars');
  var attr = article.getAttribute('data-index-number');
  article.dataset.columns; // "3"  
  article.dataset.indexNumber ;// "12314"  
  article.dataset.parent ;//
  ```
  参考：

    - [CSS content内容生成技术以及应用](http://www.zhangxinxu.com/wordpress/2010/04/css-content%E5%86%85%E5%AE%B9%E7%94%9F%E6%88%90%E6%8A%80%E6%9C%AF%E4%BB%A5%E5%8F%8A%E5%BA%94%E7%94%A8/)

9.  `canvas`的性能优化。

  答案：主要有使用缓存、使用requestAnimationFrame而不是setTimeout或者setInterval做动画的最佳循环、避免浮点运算、尽量减少canvas API的调用、硬件加速。

  参考：

    - [canvas的性能优化](http://www.cnblogs.com/rubylouvre/p/3570636.html)

10.  浏览器重绘（repaint）和重排（reflow）

  答案：重绘是一个元素外观的改变所触发的浏览器行为，例如改变visibility、outline、背景色等属性。浏览器会根据元素的新属性重新绘制，使元素呈现新的外观。重绘不会带来重新布局，并不一定伴随重排。重排是更明显的一种改变，可以理解为渲染树需要重新计算。

  通过CSS与JS设置元素例如颜色，背景图等不会影响布局的属性，但会引起重绘，此时不影响布局，只是局部元素的重新渲染，对性能影响不大。当改变了布局或者改变元素宽高，display等属性时，会引起重排，此时可能会改变DOM树的局部结构，甚至改变整个DOM树，因此浏览器（内部程序）会执行大量的重排工作，很耗性能。

  重排会因DOM树中元素的定位（排版）或结构被改变而触发，具体有：

  * 通过JS增加，删除，修改DOM节点；
  * 改变元素的display属性；
  * 改变窗口大小，滚动屏幕；
  * 移动DOM元素；
  * 修改默认字体，大小，行高；
  * 获取offsetTop，scrollTop，clientTop等距离值；
  * 获取元素样式；

  优化：

  1. `display:none;`的元素不加入DOM树，因此对于需要一次性改动大量DOM元素时，可以先将要增加，删除，修改元素的父级display设为none，在DOM树外执行增加，删除，修改操作后再一次性加回DOM树（display:none;），此过程只进行2次重排；
  2. 减少改变样式的次数，即将对同一元素的样式设置尽量合并，减少操作；
  3. 将要进行多次重排的元素属性设为absolute或fixed，这样该元素便脱离了普通流，所以改变定位时不影响DOM结构；
  4. 缓存经常使用的与重排相关的属性值；例如：`for(i = 0; i<aLi.length; i++){}`可改写为：`l = aLi.length；for(i = l-1 ; i>=0 ; i--){} //这时就不用每进行一次循环就reflow一次去计算aLi.length了`
  5. 尽量减少table布局。过去为了对齐等原因，大部分的网页都用table布局，但是性能都非常差。table布局的重排和重绘，它可能需要多次计算才能确定好其在渲染树中节点的属性，通常要花3倍于同等元素的时间。而且随便一个cell的高度宽度的修改都会影响到整个表格重排，因此性能非常差。所以，尽量避免使用table布局。
