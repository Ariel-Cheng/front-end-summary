# 前端知识总结


## 基础知识

### JavaScript

1. 同源策略及跨域请求的几种方法和原理。

   答案：同源策略是客户端脚本（尤其是Javascript）的重要的安全度量标准。它最早出自Netscape Navigator2.0，其目的是防止某个文档或脚本从多个不同源装载。这里的同源指的是：同协议，同域名和同端口。这里说的js跨域是指通过js在不同的域之间进行数据传输或通信，比如用ajax向一个不同的域请求数据，或者通过js获取页面中不同域的框架中(iframe)的数据。只要协议、域名、端口有任何一个不同，都被当作是不同的域。

   浏览器的同源策略，其限制之一就是第一种方法中我们说的不能通过ajax的方法去请求不同源中的文档。 它的第二个限制是浏览器中不同域的框架之间是不能进行js的交互操作的。有一点需要说明，不同的框架之间（父子或同辈），是能够获取到彼此的window对象的，但头疼的是你却不能使用获取到的window对象的属性和方法(html5中的postMessage方法是一个例外，还有些浏览器比如ie6也可以使用top、parent等少数几个属性)，总之，你可以当做是只能获取到一个几乎无用的window对象。

   **document.domain**：只要将同一域下不同子域的document.domain设置为共同的父域，则这个时候我们就可以访问对应window的各种属性和方法了。例如：www.example.com父域下的www.lib.example.com和www.hr.example.com两个子域，将对应页面的document.domain设为example.com即可。**缺点**：只能在一级域名相同时才能运用。且只是用与不同子域的框架间交互。不能用ajax直接请求不同子域的页面数据。  

   **JSONP**：(JSON with Padding)是一种跨域请求方式。原理是动态添加一个script标签，而script标签的src属性是没有跨域的限制的。jquery中还提供了一个$.getJSON(url,arg,callback(data))用来进行jsonp访问。**缺点**：只支持get不支持post；只支持http请求这种情况，不能解决不同域两个页面之间如何进行JavaScript调用的问题；JSONP请求失败时不返回http状态码。

   **CORS（跨域资源共享）**：HTML5引入的新的跨域的方法，不过需要在请求头(orign)和响应头(Access-Control-Allow-)设置相应的属性。**缺点**：兼容性问题，适合一些新的浏览器。

   参考:  
   - [说说JSON和JSONP](http://www.cnblogs.com/dowinning/archive/2012/04/19/json-jsonp-jquery.html)  
   - [js中几种实用的跨域方法原理详解](http://www.cnblogs.com/2050/p/3191744.html)  
   - [The Same Origin Policy: JSONP vs The document.domain Property](http://adam.kahtava.com/journal/2010/03/18/the-same-origin-policy-jsonp-vs-the-documentdomain-property/)  
   - [HTTP访问控制(CORS)](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)

2. 什么是`CORS`，有什么作用？

   答案：**CORS** 全称：“跨域资源共享”(Cross-origin resource sharing).它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服AJAX只能同源使用的限制，从不同的域访问其资源。简言之，CORS就是为了让AJAX可以实现可控的跨域访问而生的。CORS需要浏览器和服务器同时支持，定义了一种浏览器和服务器交互的方式来确定是否允许跨域请求。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。非简单请求会多出一次“预检”请求。
   JSONP只支持GET请求，CORS支持所有类型的HTTP请求。JSONP的优势在于支持老式浏览器，以及可以向不支持CORS的网站请求数据。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。

   参考:  
   - [跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)   
   - [HTTP访问控制(CORS)](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)

3. 介绍一下事件委托及其使用场景。

   答案：**事件委托**：允许我们不必为某些特定的节点添加事件监听器，而是将事件监听器添加到（这些节点的）某个父节点上，分析冒泡事件，找到匹配的子节点元素，然后做出相应的事件响应。基于两点：1、事件的冒泡，所以才可以在父元素来监听子元素触发的事件。2、DOM的遍历，一个父级元素包含的子元素过多，那么当一个事件被触发时，同样触发了某一种类型的元素。
   ```js
   //jquery 中对事件委托的支持有：delegate函数、on函数等、试用当前以及未来。
   <script type="text/javascript">  
    $(function() {  
                //让box1 处理来自 子元素P的click事件委托  
        $("#box1").delegate("p", "click", function(event) {  
            alert(event.target.id + " is clicked.");  
        });  
    })  
   </script>
   ```
   **应用场景**

   * 需要为子元素用一个事件处理函数 处理相同的动作；
   * 简化不同动作间的结构

   **优点**

   * 减少事件绑定，减少事件即函数即对象堆内存的占用，减少事件绑定需要的指定dom元素的查找次数，进而节省了内存。
   * 简化了dom节点更新时，相应事件的更新。避免了因ajax的出现，局部刷新的盛行，导致每次加载完，都要重新绑定事件；部分浏览器移除元素时，绑定的事件并没有被及时移除，导致的内存泄漏，严重影响性能等情况。
   * Allows to use innerHTML without additional processing.

   **缺点**

   * 要求事件必须冒泡. 大多数的事件会冒泡，但是并不是所有的。如blur、focus、load、unload等。
   * 理论上委托会导致浏览器额外的加载，因为在容器内的任意一个地方事件的发生，都会运行事件处理函数，所以多数情况下事件处理函数都是在空循环（没有意义的动作），通常不是什么大不了的事儿。

   参考:  
   - [JavaScript 事件委托](http://blog.csdn.net/luanlouis/article/details/24009177)  
   - [What is DOM Event delegation?](https://github.com/simongong/js-stackoverflow-highest-votes/blob/master/questions21-30/event-delegation.md)  
   - [深入理解-事件委托](http://www.zhangyunling.com/?p=564)  

4. 前端模块化的几种标准及其比较。

   答案：前端模块化，使得开发体验大大增强，摆脱了很多需要人力去做切容易出错的点，使得代码管理更加清晰规范。文件按需加载，依赖自动管理，使得更多的精力去关注模块代码本身，开发时不需要在页面上写一大堆`<script>`标签，一个`require`初始化模块就搞定。不需要每增加一个文件，还要到HTML或者其他地方添加一个`<script>`标签或文件声明。减少命名冲突，消除全局变量；一个模块一个文件，组织更清晰；依赖自动加载，按需加载。

   **模块化标准**

   1. 一切源于**CommonJS**,是服务器模块的规范，**Node.js** 采用了这个规范。文件即模块，每一个模块都是一个单独的作用域，在一个文件定义的变量（还包括函数）都是私有的，对其他文件是不可见的。使用require引用和加载模块，exports定义和导出模块，module标识模块，使用require时需要去读取并执行该文件，然后返回exports导出的内容。CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作。由于Node.js主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，因此CommonJS规范比较适用。但是，如果是浏览器环境，要从服务器端加载模块，这种同步操作很耗时，阻塞后续代码的运行，并不适用。这时就必须采用非同步模式，从而在浏览器端由CommonJS衍生出两大分支：AMD（异步模块定义）和CMD（通用模块定义）。

   2. **AMD & RequireJS**：通过define(模块名，依赖模块数组，回调函数)来定义模块，使用**提前**异步加载依赖模块的方式，模块加载完毕后执行回调函数。等到所有前置依赖加载并执行完毕，再回调主要的代码逻辑。通过参数传入依赖模块，浏览器提前下载这些模块，破坏了*就近声明*原则。

   3. **CMD & SeaJS**：SeaJS是采用的就近依赖的方式来加载模块，一般define(function(require, exports, module){})，在factory里就近加载依赖模块；本质上也是异步的加载模块，只是和RequireJS相比加载和执行的时机不一样。

   4. **UMD (通用模块规范)**：对AMD和CommonJS规范的整合，实现对JS模块化的跨平台。先判断是否支持Node.js模块格式(exports是否存在),存在则使用Node.js模块格式；再判断是否支持AMD(define是否存在), 存在则使用AMD方式加载模块；前两个都不存在，则将模块公开到全局(window或global)。

   5. **ES6的规范化**：ES6作为JavaScript新的标准，自带了模块化的buff，通过import和export导入导出模块；尽量并不是UMD, 但它还是尽量在兼容现有的AMD和CommonJS模块, 支持导入这两者的模块。 它要更优雅一些, 它有所超越, 原生的语法和关键字也更方便。
   前端模块管理工具：npm + webpack

   SeaJS对模块的态度是懒执行,SeaJS只会在真正需要使用(依赖)模块时才执行该模块；而RequireJS对模块的态度是预执行；会先尽早地执行(依赖)模块, 相当于所有的require都被提前了, 而且模块执行的顺序也不一定

   参考:  
   - [JS模块化](http://tinkgu.github.io/2015/11/12/modularity/)  
   - [前端模块化开发方案小对比](http://div.io/topic/1078)  
   - [前端各种模块化规范常回顾](http://www.imooc.com/article/15094?block_id=tuijian_wz)  

5. 模块加载器的实现原理(`SeaJS`)

   答案：模块化开发之sea.js实现原理简言之就是要解决三个问题，分别为：

   1. 模块加载（插入script标签来加载模块。你在页面看不到标签是因为模块被加载完后删除了对应的script标签）；
   2. 模块依赖（按依赖顺序依赖）；
   3. 命名冲突（封装一层define，所有的都成为了局部变量，并通过exports暴漏出去）。

  简单的模块化，可以用闭包来做，但是闭包只能解决模块加载中第一个问题，变量名冲突的问题，但是一个成熟的模块加载系统是需要两个部分。

   1. 创建隔离的代码块，不会出现变量名冲突的问题
   2. 解决模块与模块之间的依赖问题

  在seajs中模块化并不是通过闭包来实现的，而是通过js对象来实现的，一个模块就是一个对象，这个对象拥有一个id，一般用url的相对路径来作为它的id，但是你也可以创建命名的模块。模块下载下来之后，并不会立刻执行你写的整块代码，因为你写的整块代码，都会被一个define函数包裹，首先执行define函数，你所写的代码都会以参数的形式传入define函数，define函数会处理这些代码，第一步正则匹配，找出你代码里的依赖，如果有依赖，就会去处理这些依赖，等依赖加载完了之后，才会去执行全部的代码，最后暴露相应的接口，整个模块就已经准备好了。

   入口`js`文件`<script src="sea.js"></script>`写在页面的尾部，整个页面已经加载完成之后seajs再加载其他的模块，并不会阻塞页面。从入口 js 往里检查依赖。正则匹配，分析依赖关系，建立脚本加载队列、递归加载。

   1. **动态创建脚本** createElement(‘script’)和appendChild(script) 动态创建脚本，添加到head元素中。
   2. **分析依赖建立** 每个模块可能会依赖不同的模块，我们需要理清楚这些模块之间的依赖关系，然后分别将它们加载进来。为了分析依赖关系，我们使用toString的方法，将模块转化为一个string，然后去其中寻找依赖。`fn.toString().match(/.require((“|’)[^)]*(“|’))/g)` 将模块转换为字符串，然后通过正则表达式，匹配每个模块中的的依赖文件。
   3. 建立脚本加载队列、递归加载。

   参考:
  - [模块化开发之sea.js实现原理总结](http://www.lxway.com/85146452.htm)  
   - [模块加载器](http://web.jobbole.com/85622/)   
   - [如何实现一个 CMD 模块加载器](http://annn.me/how-to-realize-cmd-loader/)  

6. 什么是`Session`和 `Cookie`，它们的作用是什么？

   答案：会话：进行某活动连续的一段时间内，服务器与客户端之间会话的状态(保持用户状态，登陆失败成功等等).用来跟踪用户，比如某段时间内，用户多次访问某网站，那么该网站可以确定该用户的身份(登陆了，刷新之后登陆状态依然要保持)。所以就得在服务器与用户之间有一个一一对应的关系。
   一个用户的所有请求都应该属于同一会话，会话与会话间相互独立。网站服务一般使用http协议传输数据(无状态协议)，一旦数据交换完成，客户端与服务器端的连接就会关闭，再次交换需要再次创建新连接。也就是说，服务器无法从链接上跟踪会话，所以要引入一种机制，来弥补http协议无状态的不足。
   一般通过`Cookie`和`Session`来实现。
   `Cookie`通过在客户端记录信息，确定用户的身份；
   `Session`通过服务器端记录信息，确定用户身份。
   1. **Cookie**

     在`Session`没有出现以前，基本所有网站都用`Cookie`。`Cookie` 是访问过的网站创建的文件，用于存储浏览信息，例如个人资料信息。这些信息存放在客户端的计算机中，用于客户端计算机与服务器之间传递信息。
     对于`Cookie`的实现，每一次http请求都会带给服务器当前域下的`Cookie`值，服务器通过解析不同的`Cookie`值，或加密后的`Cookie`值，来辨识当前用户信息，这些`Cookie`数据都是存储到客户端，也就是浏览器的键值对，可以删除、更改、设置过期等等，也有一些最大的容量(4kb,20条等)这些限制。浏览器会将这些信息存放在一个统一的位置，对于Windows操作系统而言，我们可以从： [系统盘]:\Documents and Settings\[用户名]\`Cookie目录中找到存储的`Cookie`。有两个http头部是专门负责设置以及发送`Cookie`的,它们分别是Set-`Cookie`以及`Cookie`。当服务器返回给客户端一个http响应信息时，其中如果包含Set-`Cookie`这个头部时，意思就是指示客户端建立一个`Cookie`，并且在后续的http请求中自动发送这个`Cookie`到服务器端，直到这个`Cookie`过期。
     ![Cookie](/images/cookie.png)

   2. **Session**

    当程序要为某个客户端的请求创建一个`Session`的时候，服务器首先检查这个客户端的请求里面是否包含了一个`Session`的标识，一般我们称之为`Session`Id。如果已经包含了一个`Session`Id，就说明已经为当前这个客户端建立过`Session`，那个服务器就会根据这个`Session`Id，把这个`Session`找出来就可以了。如果客户端请求里没有这个`Session`Id，那就要为这个客户端创建一个`Session`，并且生成一个跟这个`Session`关联的一个`Session`Id，然后这个`Session`Id的值，应该是一个不会重复，而且不容易被找到规律的字符串，这样以防止被伪造，那么这个`Session`Id也会在当次的这个响应中返回给客户端保存起来。`Session`内容只会保存在服务器中，发到客户端的只有`Session` id；当客户端再次发送请求的时候，会将这个`Session` id带上，服务器接受到请求之后就会依据`Session` id找到相应的`Session`，从而再次使用之。正是这样一个过程，用户的状态也就得以保持了。
   ![Session](/images/Session.png)

    保存这个`Session`Id的方式可以采用`Cookie`，这样呢，在交互的过程中，浏览器可以自动的按照规则，把标识发送给服务器，一般来说，`Cookie`的名字都是类似于`Session`Id，比如依赖于connect的一个express，它的默认的`Cookie`值就是connect.`Session`Id,一般情况系下，`Session`都是存储到内存里，到我们的开发情况下，当服务器进程被停止了，或者重启了，内存里的`Session`也会被清空，如果设置了`Session`持久化的特性，比如服务器吧`Session`保存包硬盘上，那么当服务器进程重启后，这些信息就能再次被使用。

    那么对于`Session`的持久化，有几种常见的方式，比如基于`Cookie`/基于内存/基于redis/基于mongoDB.
    * 基于`Cookie`，把数据直接存储到`Cookie`里，解析后就能获得
    * 基于内存，使用内存当容器来存储数据
    * 基于redis，把数据存入到redis数据库中，这样不同进程都能获取到`Session`数据，`Cookie`解析后获取到的`Session`Id，通过`Session`Id访问内存里面，再来获取相应的数据。
    * 基于mongoDB 同上。

    ![持久化](/images/持久化.png)

   参考:
   - [理解Cookie和Session机制](http://www.admin10000.com/document/7097.html)

7. 如何用`JavaScript`操作`Cookie`？

   答案：在 JavaScript 中通过 document.cookie属性，你可以创建、维护和删除 `Cookie`。创建 `Cookie` 时该属性等同于 Set-`Cookie` 消息头，而在读取 `Cookie` 时则等同于 `Cookie` 消息头。在创建一个 `Cookie` 时，你需要使用和 Set-`Cookie` 期望格式相同的字符串：
   ```js
   document.cookie="name=Nicholas;domain=nczonline.net;path=/";
   ```
   设置 document.cookie属性的值并不会删除存储在页面中的所有 `Cookie`。它只简单的创建或修改字符串中指定的 `Cookie`。下次发送一个请求到服务器时，通过 document.cookie设置的 `Cookie` 会和其它通过 Set-`Cookie` 消息头设置的 `Cookie` 一并发送至服务器。这些 `Cookie` 并没有什么明确的不同之处。要使用 JavaScript 提取 `Cookie` 的值，只需要从 document.cookie 中读取即可。返回的字符串与 `Cookie` 消息头中的字符串格式相同，所以多个 `Cookie` 会被分号和字符串分割。例如：`name1=Greg; name2=Nicholas`
   鉴于此，你需要手工解析这个 `Cookie` 字符串来提取真实的 `Cookie` 数据。当前已有许多描述如何利用 JavaScript 来解析 `Cookie` 的资料，包括我的书，Professional JavaScript，所以在这我就不再说明。通常利用已存在的 JavaScript 库操作 `Cookie` 会更简单，如使用 YUI `Cookie` utility 来处理 `Cookie`，而不要手工重新创建这些算法。
   通过访问 document.cookie返回的 `Cookie` 遵循发向服务器的 `Cookie` 一样的访问规则。要通过 JavaScript 访问 `Cookie`，该页面和 `Cookie` 必须在相同的域中，有相同的 path，有相同的安全级别。

   *注意：一旦 `Cookie` 通过 JavaScript 设置后便不能提取它的选项，所以你将不能知道 domain，path，expires 日期或 secure 标记。*

   从JavaScript的角度看，`Cookie` 就是一些 **字符串String类型** 信息。`Cookie`里面包含的信息并没有一个标准的格式，各个网站服务器的规范都可能不同，但一般会包括：所访问网站的域名（domain name），访问开始的时间，访问者的IP地址等客户端信息，访问者关于这个网站的一些设置等等。`Cookie`一般包含一下几个属性：name(名字)，value(值)，domain(域)，path(路径)，expires(过期时间)；其中，name和value是必须的，其他的会有默认值。domain的默认值是当前页面的域名，path的默认值是当前页的URL，expires的默认值是`Session`(会话结束时清除`Cookie`)。
   1. 写入`Cookie`

    写入`Cookie`主要设置五个字段，内容、有效期、域名、路径、是否安全传输。`Cookie`使用类似键值对的方式进行存储，比如“username=zhangshan”，只需将这个`Cookie`赋值给document.`Cookie`即可。名、值都要是标准的标识符，所以可以在使用前，用escape函数包裹，以免出错。**document.cookie** 有读和写两种形式，写形式代表添加`Cookie`，且一次只能添加一条`Cookice`，要添加多条则需要调用多次。处于安全性的考虑，`Cookie`是具有不可跨域性的,只有与创建 `Cookie` 的页面在同一个目录或子目录下的网页才可以访问.把`Cookie`设置为secure，那么它与服务器之间就通过HTTPS或者其它安全协议传递数据。c只保证 `Cookie` 与服务器之间的数据传输过程加密，而保存在本地的 `Cookie`文件并不加密。如果想让本地`Cookie`也加密，得自己加密数据。
   ```js
   document.cookie= "name=zhangsan;expires="+date.toGMTString();
   //例如 "www.qq.com" 与 "sports.qq.com" 公用一个关联的域名"qq.com"，我们如果想让 "sports.qq.com" 下的`Cookie`被 "www.qq.com" 访问，我们就需要用到 `Cookie` 的domain属性，并且需要把path属性设置为 "/"。
   document.cookie= "name=zhangsan;domain=qq.com;secure";
   ```
   2. 读取`Cookie`

    读取`Cookie`使用到document.`Cookie`的读模式，返回的就是所有的`Cookie`，中间用分号隔开。
   ```js
   document.cookie= "name=zhangsan";  //写
   document.cookie= "age=10";  //写
   console.log(document.`Cookie`);  //输出 name=zhangsan; age=10
   ```
   3. 删除、修改`Cookie`

    `Cookie`并不提供删除、修改的方法，如果想修改某项`Cookie`，只需添加一个同名`Cookie`，新的值将覆盖旧的值。要删除`Cookie`，只需将该`Cookie`有效期设置到当前时间以前即可。
   ```js
   document.cookie= "name=zhangsan";
   document.cookie= "name=lisi";  //name被修改为lisi
   var date = new Date();
   //设置为前一毫秒（多前都可以）
   date.setTime(date.getTime() - 1);
   //删除name
   document.cookie= "name=lisi;expires=" + date.toGMTString();
   ```
   4. 封装操作`Cookie`的方法

    使用原生方法对`Cookie`操作是有些麻烦的，我们可以将其封装起来，name代表键名，value代表值，不填则为读取名为name的值，option代表设置值如有效期等。其中有效期单位为天。

   参考:
   - [JavaScript 操作 `Cookie`](http://www.cnblogs.com/shoupifeng/archive/2011/11/25/2263892.html)
   - [javascript中的`Cookie`问题](https://segmentfault.com/a/1190000004189877)

8. 什么情况下会导致客户端`JavaScript`读取`Cookie`失败？

   答案：1.服务器对其设置了 HttpOnly。服务端设置了http-only属性的`Cookie`，客户端JS无法读取，更别说更改了。(HttpOnly是指仅在HTTP层面上传输的`Cookie`,当设置了HttpOnly标志后，客户端脚本就无法读写该`Cookie`，这样能有效的防御XSS获取`Cookie`)。

   2.域名限制。跨域的`Cookie`会存取失败（跨二级域名不包括在内）。

   3.客户端可以自由禁止。如果浏览器设置了阻止网站设置任何数据， 客户端无法接收`Cookie`，当然JS对`Cookie`的操作会失败。

   4.大小限制。`Cookie`的数量超过最大限制，之前的`Cookie`被自动删除，JS无法读取到。

   5.`Cookie`过期被浏览器自动删除了。

   参考:
   - [HTTP `Cookie`s 详解](http://bubkoo.com/2014/04/21/http-`Cookie`s-explained/)
   - [在什么情况下 JavaScript 写入 `Cookie` 的操作会失败](https://www.zhihu.com/question/20332255)

9. 前端实现动画效果的几种方法？

   答案：1.**css的transition**:抓住了所设置变化属性的起始态和完成态，通过设定的速度曲线来完成动画。可以涉及到各种变化的css属性，默认为all，则所有变化的属性都会在出发时，以动画的形式展现出来。这种动画方式是css3的，因此ie9以下是不支持的，其他的浏览器需要加前缀，并且只有两态，不支持自定义中间的状态。
   语法：transition: property duration timing-function delay;
   property:填写需要变化的css属性如：width,line-height,font-size,color等;
   duration:完成过渡效果需要的时间（2s 或者2000ms）
   timing-function:完成效果的速度曲线（linear匀速,ease从慢到快再到慢,ease-in慢慢变快,ease-out慢慢变慢等等）
   timing-delay:动画效果的延迟触发时间（2s 或者2000ms）。
   *tips:* transform是一种变化属性，该属性允许我们对元素进行旋转、缩放、移动或倾斜。可以作为transition中需要变化的属性。
   ```js
   transition:width 2s;
   -moz-transition:width 2s; /* Firefox 4 */
   -webkit-transition:width 2s; /* Safari and Chrome */
   -o-transition:width 2s; /* Opera */
   transform:rotate(9deg);
   ```
   2.css3的animation属性:使用animation属性制作动画可以更加灵活的设置动画帧，通过不同keyframe（动画帧）的设置，实现很多优雅的效果，keyframe中的百分数是动画完成总时间的比例。animation是设置总的动画效果，而keyframe中设置上相应的动画名字，然后在keyframe中设置具体的动画效果。当然由于是css3的属性，仍然需要注意它的兼容性，加上必须的前缀。
   语法：animation: name duration timing-function delay iteration-count direction;
   name:keyframe的名称，也就是定义了关键帧的动画的名称,这个名称用来区别不同的动画。
   duration:完成动画所需要的时间（2s 或者 2000ms）
   timing-function:完成动画的速度曲线
   delay：动画开始之前的延迟
   iteration-count：动画播放次数
   direction：是否轮流反向播放动画（normal:正常顺序播放，alternate下一次反向播放）如果把动画设置为只播放一次，则该属性没有效果。
   ```js
   <style>
    div
      {
        animation:mymove 5s infinite;
        -webkit-animation:mymove 5s infinite; /*Safari and Chrome*/
      }     
    @keyframes mymove
    {
      1% {left:0px;}
      20%{left:200px;}
      50% {left:300px;}
      100%{left:200px;}
    }
    @-webkit-keyframes mymove /*Safari and Chrome*/
    {
      1% {left:0px;}
      20%{left:200px;}
      50% {left:300px;}
      100%{left:200px;}
    }
    </style>
   ```
   3.Jquery的animate函数:该方法通过CSS样式将元素从一个状态改变为另一个状态。CSS属性值是逐渐改变的，这样就可以创建动画效果。只有数字值可创建动画（比如 "margin:30px"）。字符串值无法创建动画（比如 "background-color:red"）。可见通过jquery的animation生成动画的过程中可同时使用多个属性，也可以定义相对值（该值相对于元素的当前值）。需要在值的前面加上 += 或 -=，如（height:'+=150px'），还可以使用队列机制进行步骤式的动画,动画就会按照顺序一步一步实现，并且不用考虑兼容性，因为几乎都兼容。但是，animate函数只能够实现一些数值属性，能够实现的变化非常有限制，而且使用这个函数时还要配合stop来使用，在达到某条件时终止动画，设置比较复杂。
   语法：$(selector).animate(styles,options)
   styles:产生动画的css样式和值；
   options={
     speed:动画的速度（可选参数：slow,normal,fase）
     easing：动画的速度函数（可选参数：swing，linear）
     callback：动画完成之后要执行的函数；
     queue：是否放置在效果队列中，是布尔值，为false则立即开始
     specialEasing:styles参数的一个或多个属性映射及对应的easing函数。}
   ```js
   div.animate({height:'300px',opacity:'0.4'},"slow");
   div.animate({width:'300px',opacity:'0.8'},"slow");
   ```
   4.原生js动画:原生js动画利用js代码，将动画一步以函数的方式写出来，可以实现多种动画样式，而且可以自己做兼容性处理，自己设立每一步的动画效果，并且能够完成比较复杂的效果，但是代码量很大。setTimeOut\setTimeInterval。
   5.插件：网上可以搜到很多封装好的动画插件，这些插件可以直接引入到页面中，通过初试话和配置的方式进行设定，直接在页面中展示动画。如：waves，textillate.js等等。
   6.使用canvas制作动画：使用canvas在浏览器上画图，并且利用其api，制作动画。canvas使用的地方非常多，尤其是在制作h5小游戏上。同样都是使用编码的方式由前端开发工程师完成动画效果，canvas要比原生js效率高的多，流畅的多。通过画笔的方式，能够轻松的实现更多的动画效果。
   7.引用gif图片：如果在需求特别紧急，而且动画又特别复杂的情况下，自己没有把握按时实现效果，或者代价太大，上gif图片吧，用户的体验是没有影响的~所以，别纠结。

   参考:
   - [前端制作动画的几种方式（css3，js）](http://www.cnblogs.com/zhangwenjiajessy/p/6154703.html)
   - [html5 canvas常用api总结(一)(二)](http://www.cnblogs.com/zhangwenjiajessy/p/6108090.html)

10. `this`的概念，基本用法。

   答案： this是Javascript语言的一个关键字。this对象是在运行时基于函数的执行环境绑定的。
   它代表函数运行时，自动生成的一个内部对象，只能在函数内部使用.随着函数使用场合的不同，this的值会发生变化。但是有一个 **总的原则**，那就是this指的是，调用函数的那个对象. 谁调用它，this就指向谁.

  *注意* ：当我们使用严格模式（strict mode）的时候，this 在全局函数中和匿名函数中的值是未定义的（undefined），不指向任何一个对象。一个函数执行的时候，它就获得了 this 这个属性。

   1.全局作用域中使用this：在全局作用域中，当代码在浏览器中执行的时候，所有的全局变量和函数都被定义在 window 对象上。因此，当我们在全局函数中使用 this 的时候，它会指向全局 window 对象并且拥有它的值（除非在严格模式下）。
   2.我们应该发现当上下文发生变化的时候，换句话说就是当我们在别的对象中调用了本对象内定义的方法的时候，this 关键字就不再指向定义 this 时的那个对象了，而是指向了调用了那个 this 所在方法的对象。
   3.在匿名函数内部的 this 不能获得外层函数 this 的值，所以当没有使用严格模式的时候，它就被绑定在了全局 window 对象上了。在内层函数中维持 this 的值的方法：在常见的单体模式中，通常我们会使用_this , that , self 等保存 this，这样我们可以在改变了上下文之后继续引用到它 。

   基本用法：
   1.纯粹的函数调用:这是函数的最通常用法，属于全局性调用，因此this就代表全局对象Global.
   ```js
   var x = 1;
　　function test(){
　　　　this.x = 0;
　　}
　　test();
　　alert(x); //0
   ```
   2.作为对象方法的调用:函数还可以作为某个对象的方法调用，这时this就指这个上级对象
   ```js
   function test(){
　　　　alert(this.x);
　　}
　　var o = {};
　　o.x = 1;
　　o.m = test;
　　o.m(); // 1
   ```
   3.作为构造函数调用:所谓构造函数，就是通过这个函数生成一个新对象（object）。这时，this就指这个新对象。
   ```js
   function test(){
   　　　　this.x = 1;
   　　}
   　　var o = new test();
   　　alert(o.x); // 1
   ```
   4.apply调用:apply()是函数对象的一个方法，它的作用是改变函数的调用对象，它的第一个参数就表示改变后的调用这个函数的对象。因此，this指的就是这第一个参数。apply()的参数为空时，默认调用全局对象。因此，这时的运行结果为0，证明this指的是全局对象
   ```js
      var x = 0;
   　　function test(){
   　　　　alert(this.x);
   　　}
   　　var o={};
   　　o.x = 1;
   　　o.m = test;
   　　o.m.apply(); //0
       o.m.apply(o); //1
   ```

   参考:
   - [理解并掌握 JavaScript 中 this 的用法](https://webcache.googleusercontent.com/search?q=cache:j_SnUmA5VtgJ:https://code.mforever78.com/translation/2015/05/19/understand-javascripts-this-with-clarity-and-master-it/+&cd=1&hl=zh-CN&ct=clnk&gl=cn)
   - [Javascript的this用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)
   - [JS 中 this上下文对象的使用方式](http://www.cnblogs.com/imwtr/p/4766543.html)

11. `apply`和`call`的作用以及区别。

   答案：JavaScript 的一大特点是，函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。当一个 object 没有某个方法，但是其他的有，我们可以借助call或apply用其它对象的方法来操作。
   作用：call 和 apply 都是为了改变某个函数运行时的上下文（context）而存在的，换句话说，就是为了改变函数体内部 this 的指向。
   区别：apply和call功能一样，作用是绑定this指针，设定函数中this的上下文环境。第一个参数都是要传入给当前对象的对象，你想指定的上下文，可以是任何一个 JavaScript 对象(JavaScript 中一切皆对象)。但对第二个参数：call()接受的是一个参数列表，而 apply()方法接受的是一个包含多个参数的数组（或类数组对象arg）。

   ```js
     //   其中 this 是你想指定的上下文，他可以是任何一个 JavaScript 对象(JavaScript 中一切皆对象)，call 需要把参数按顺序传递进去，而 apply 则是把参数放在数组里。当参数数量不确定时，函数内部也可以通过 arguments 这个伪数组来遍历所有的参数。
     func.call(this, arg1, arg2);
     func.apply(this, [arg1, arg2])
     //number 本身没有 max 方法，但是 Math 有，我们就可以借助 call 或者 apply 使用其方法。
     var  numbers = [5, 458 , 120 , -215 ];
     var maxInNumbers = Math.max.apply(Math, numbers),   //458
         maxInNumbers = Math.max.call(Math,5, 458 , 120 , -215); //458
   ```

   参考:

   - [深入浅出 妙用Javascript中apply、call、bind](http://www.cnblogs.com/coco1s/p/4833199.html)

12. `bind`的作用，如何用`apply`或`call`模拟`bind`？

   答案：bind() 方法与 apply 和 call 很相似，也是可以改变函数体内 this 的指向。bind()方法会创建一个新函数，称为绑定函数，当调用这个绑定函数时，绑定函数会以创建它时传入 bind()方法的第一个参数作为 this，传入 bind() 方法的第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数。
   在Javascript中，多次 bind() 是无效的。更深层次的原因， bind() 的实现，相当于使用函数在内部包了一个 call / apply ，第二次 bind() 相当于再包住第一次 bind() ,故第二次以后的 bind 是无法生效的。
   区别是，当你希望改变上下文环境之后并非立即执行，而是回调执行的时候，使用 bind() 方法。而 apply/call 则会立即执行函数。但IE9（包括IE9)以上的才支持bind
   ```js
   var obj = {
       x: 81,
   };
   var foo = {
       getX: function() {
           return this.x;
       }
   }
   console.log(foo.getX.bind(obj)());  //81
   console.log(foo.getX.call(obj));    //81
   console.log(foo.getX.apply(obj));   //81
   ```
   bind、call、apply三者的区别：
   1.apply 、 call 、bind 三者都是用来改变函数的this对象的指向的；
   2.apply 、 call 、bind 三者第一个参数都是this要指向的对象，也就是想指定的上下文；
   3.apply 、 call 、bind 三者都可以利用后续参数传参，apply 传递的参数必须是用数组进行包装的，而 call 和 bind 则是将要传递的参数一一列出；
   4.bind 是返回对应函数，便于稍后调用；apply 、call 则是立即调用 。

   用`apply`或`call`模拟`bind`
   ```js
   Function.prototype.Bind = function(context){
       var self = this,
            // 获取到bind第二个参数（中的所有参数），bind参数是一一列出的。
           args = Array.prototype.slice.call(arguments,1);
           // 返回一个新的函数
       return function(){
           // 将相关参数赋给这个bind所在方法，并将执环境赋给context
           return self.apply(context,args);
       };
   };
   ```
   参考:

   - [JS 的 call apply bind 方法](http://www.cnblogs.com/imwtr/p/4765278.html)

13. `JavaScript`原型链及`JavaScript`如何实现继承、类？

   答案：**原型链**：每个构造函数都有一个原型对象，原型对象都包含一个执行构造函数的指针，而实例都包含一个执行原型对象的内部指针。而原型对象有可能等于另一个类型的实例，此时原型对象将包含一个指向另一个原型的指针，相应地，另一个原型中也包含一个只想另一个构造函数的指针。如果另一个原型又是另一个类型的实例，那么上述关系依然成立，如此层层递进，构成了实例与原型的链条。这就是所谓原型链的基本概念。
   1、可以通过对象实例访问保存在原型中的值，但不能通过对象实例重写原型中的值。只会屏蔽原型对象中的同名属性。
   2、原型链的动态性：对原型对象做的任何更改都能里立即从实例中反映出来，即使是先创建实例后再修改原型也一样。
   3、实例中的指针仅只想原型，而不指向构造函数。如果重写整个原型对象，就会切断现有实例、构造函数与新原型对象之间的联系。所以不能使用对象字面量来重写原型。
   4、每创建一个函数，就会同时创建它的prototype对象，这个对象也会自动获取它的construct属性，使用字面量，本质上重写了默认的protype对象，所以construct就会断掉，指向object。
   整个过程如图所示：
   ！[原型链](/images/原型链.png)

   **继承**：实现的本质是重写原型对象，而不使用默认提供的原型对象，代之以一个新类型的实例。利用原型链，继承别的对象的方法。
   ```js
      function superType()
      {
	       this.name = true;
       }//超类构造函数
       superType.prototype.getSuperName = function(){
	        return this.name;
        }//超类原型
      function subType(){
	      this.name = false;
      }//子类构造函数
      subType.prototype = new superType(); //将子类的原型指向超类的实例，实现继承
      subType.prototype.getSubName = function(){
         return this.name;
       }
      var instance = new subType();//实例化超类对象
      alert(instance.getSuperName())//true
   ```
   **类**：在面向对象编程中，类（class）是对象（object）的模板，定义了同一组对象（又称"实例"）共有的属性和方法。Javascipt语法不支持"类"（class），导致传统的面向对象编程方法无法直接使用，但是可以用一些变通的方法，模拟出"类"。

   ###### 1.构造函数法:
   首字母大写，new操作符，还可以定义在构造函数的prototype对象之上。
   ```js
      function Person(name,age,job){
        this.name = name;
        this.age = age;
        this.job = job;
        this.asyName = function(){
          alert(this.name);
        }
      }
      Cat.prototype.makeSound = function(){
　　　　alert("喵喵喵");
　　 }
      var person1 = new Person("Greg",27,"Doctor");
   ```
   ###### 2.Object.create()法：
   不能实现私有属性和私有方法，实例对象之间也不能共享数据，对"类"的模拟不够全面。
   ```js
   var Cat = {
　　　　name: "大毛",
　　　　makeSound: function(){ alert("喵喵喵"); }
　　};
  var cat1 = Object.create(Cat);
　　alert(cat1.name); // 大毛
　　cat1.makeSound(); // 喵喵喵
  ```
  ###### 3.工厂模式
  3.1 封装：
  一个对象里面，定义一个构造函数createNew()，用来生成实例。

  3.2 继承：
  让一个类继承另一个类，实现起来很方便。只要在前者的createNew()方法中，调用后者的createNew()方法即可。

  3.3 私有属性和私有方法：
  在createNew()方法中，只要不是定义在cat对象上的方法和属性，都是私有的。

  3.4 数据共享:
  有时候，我们需要所有实例对象，能够读写同一项内部数据。这个时候，只要把这个内部数据，封装在类对象的里面、createNew()方法的外面即可。如果有一个实例对象，修改了共享的数据，另一个实例对象也会受到影响。
  ```js
  var Animal = {
  　　　　createNew: function(){
  　　　　　　var animal = {};
  　　　　　　animal.sleep = function(){ alert("睡懒觉"); };
  　　　　　　return animal;
  　　　　}
  　　};
  var Cat = {
    sound : "喵喵喵",
　　　　createNew: function(){
　　　　　　var cat = Animal.createNew();
　　　　　　cat.name = "大毛";
　　　　　　cat.makeSound = function(){ alert("喵喵喵"); };
　　　　　　return cat;
　　　　}
　　};
  var cat1 = Cat.createNew();
　　cat1.sleep(); // 睡懒觉
  alert(cat1.name); // undefined
  cat1.makeSound(); // 喵喵喵
 ```
   参考:
   - [JavaScript基于原型链的继承原理](http://natumsol.github.io/2014/07/08/Javascript-inheritance/)
   - [Javascript定义类（class）的三种方法](http://www.ruanyifeng.com/blog/2012/07/three_ways_to_define_a_javascript_class.html)

14. 闭包的概念，作用，使用场景以及可能造成的不良后果。

   答案：当在函数内部定义了其他函数时，就创建了闭包（closures）。闭包有权访问函数内部的所有变量，**原理** 如下：

	* 当某个函数被调用时，会创建一个执行环境及相应的作用域链，然后，使用arguments和其他命名参数的值来初始化函数的活动对象。
	* 在后台执行环境中，闭包的作用域链包含着他自己的作用域、包含函数的作用域和全局作用域。
	* 通常，函数的作用域及其所有变量都会在函数执行结束后被销毁
	* 但是，当函数返回一个闭包时，这个函数的作用域将会一直在内存中保存直到闭包不存在为止,即当函数返回闭包后，其执行环境的作用域链会被销毁，但他的活动对象仍会留在内存中，知道匿名函数被销毁后，函数的活动对象才会被销毁。
  * 闭包只能取得包含函数中任何变量的最后一个值。

  **使用场景** ：

  1、使用闭包可以在JavaScript中模仿块级作用域（JavaScript本身没有块级作用域的概念）要点如下：
	* 创建并立即调用一个函数，这样既可以执行其中的代码，又不会在内存中留下对该函数的引用；
	* 结果就是函数内部的所有变量都会立即被销毁--除非将某些变量赋值给包含作用域（即外部作用域）中的变量；
	* 调用函数的方式，实在函数名称后面加一堆圆括号，因为JavaScript将function关键字当作一个函数声明的开始，而函数声明后面不能跟圆括号，然而函数表达式的后面可以跟圆括号；
	* 将函数声明包含在一对圆括号中，他实际上表示一个函数表达式~用作块级作用域，私有作用域的匿名函数。后面可以加上一对圆括号~表示立即执行这个函数。

  2、闭包还可以用于在对象中创建私有变量，利用私有和特权，隐藏那些不应该被直接更改的属性。要点如下：
	* 任何在函数中定义的变量，都可以认为是私有变量，因为不能再函数的外部访问这些变量，私有变量包括函数的参数、局部变量和在函数内部定义的其他函数；
	* 即使JavaScript中没有正式的私有对象属性的概念，但是可以在函数内部创建一个闭包，那么闭包通过自己的作用域也可以访问这些变量，从而撞见访问私有变量的公有方法；
	* 有权访问私有变量的公有方法叫做特权方法；
	* 在构造函数中定义特权方法的缺点：必须使用构造函数模式来实现，构造函数模式的缺点就是针对每个实例都会创建同一组心方法，无法体现方法的复用。
  ```js
  for (var i=1; i<10; i++) {
    setTimeout( function timer(){
      console.log( i );
    },1000 );
  }//输出10个10；
  ```
  闭包会携带包含它的函数的作用域，这种作用域链的配置引出一个值得注意的副作用：闭包只能取得包含函数中任何变量的最后一个值。因为闭包保存的是整个变量对象，而不是某个特殊的变量。
  ```js
  for (var i=1; i<10; i++) {
      (function(j){
          setTimeout( function timer(){
              console.log( j );
          }, 1000 );
      })( i );
  }//输出1~9
  ```
  按值传递参数，立即执行，而不是直接引用外部作用于中的i。

  **不良后果：**

  好处：能够实现封装和缓存等；坏处：就是消耗内存、不正当使用会造成内存溢出的问题。
  1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
  2）闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

  参考:
  - [学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
  - [图解Javascript上下文与作用域](http://blog.rainy.im/2015/07/04/scope-chain-and-prototype-chain-in-js/)

15. 常见数据结构(栈，队列，链表，二叉树，图等)和算法(排序算法)的概念以及`JavaScript`的实现。

   答案：js数组、《数据结构与算法JavaScript描述》
   参考:  
    - [常用排序算法之JavaScript实现](http://www.cnblogs.com/ywang1724/p/3946339.html#3037096)
    - [排序算法](http://javascript.ruanyifeng.com/library/sorting.html)
    - [快速排序（Quicksort）的Javascript实现](http://www.ruanyifeng.com/blog/2011/04/quicksort_in_javascript.html)

16. 事件冒泡和事件捕获的区别。

   答案：W3C中定义任何事件的发生都会经历三个阶段：
   * 捕获阶段（capturing）从document对象开始进行事件捕获，沿着文档逐层向下捕获，直到事件达了事件源元素(target目标)，从最不精确的对象开始触发，然后到最精确；
   * 目标阶段（targeting）事件在目标节点上触发；
   * 冒泡阶段（bubbling）事件从发生的目标（event.srcElement||event.target）开始，沿着文档逐层向上冒泡，到document为止,事件按照从最特定的事件目标到最不特定的事件目标(document对象)的顺序触发；

   事件捕获和事件冒泡是DOM中的两种事件流，区别是从顶层元素到目标元素或者从目标元素到顶层元素。两种事件处理顺序刚好相反。IE只支持事件冒泡。

   **传统绑定事件方式：DOM0级事件处理程序**

   将一个函数赋值给一个事件处理程序属性，每个DOM元素都有自己的时间处理程序属性，这些属性通常全部小写。DOM0级事件被认为是元素的方法。以这样的方式添加的事件处理程序会在事件流的冒泡阶段被处理。
    ```js
    var btn = document.getElementById('myBtn');
    btn.onclick = function(){
      alert("Cilcked!");
    }
    ```
    **DOM2级事件处理程序：**    

    程序员可以自己选择绑定事件时采用事件捕获还是事件冒泡，方法就是绑定事件时通过addEventListener函数，它有三个参数，第三个参数若是true，则表示采用事件捕获，若是false，则表示采用事件冒泡。
    ```js
    ele.addEventListener('click',doSomething2,true)
    //true=捕获   false=冒泡
    ```
    **IE浏览器**

    如上面所说，IE只支持事件冒泡，不支持事件捕获，它也不支持addEventListener函数，不会用第三个参数来表示是冒泡还是捕获，它提供了另一个函数attachEvent。
    ```js
    ele.attachEvent("onclick", doSomething2);
    ```
    **事件的传播是可以阻止的：**

    * 在W3c中，使用e.stopPropagation（）方法来阻止冒泡，在捕获的过程中stopPropagation（）后，后面的冒泡过程也不会发生了；
    * 在IE下设置window.event.cancelBubble = true；
    * 不是所有的事件都能冒泡。以下事件不冒泡：blur、focus、load、unload。
    * 阻止冒泡并不能阻止对象默认行为；有一些html元素默认的行为，比如说a标签，点击后有跳转动作；form表单中的submit类型的input有一个默认提交跳转事件；reset类型的input有重置表单行为，这种行为无须我们写程序定制；
    * 在W3c中，使用preventDefault（）方法；
    * 在IE下设置window.event.returnValue = false;

   参考:
   - [JavaScript事件冒泡、事件捕获和阻止默认事件](http://www.codeceo.com/article/javascript-event-type.html)
   - [事件的传播](http://javascript.ruanyifeng.com/dom/event.html#toc9)

17. 如何进行浏览器兼容性检测(客户端检测)？

   答案：浏览器提供商虽然在实现公共接口方面投入了很多精力，但结果仍然是每一种浏览器都有各自的长处，也都有各自的缺点。即使是那些跨平台的浏览器，虽然从技术上看版本相同，也照样存在不一致性问题。面对普遍存在的不一致性问题，开发人员要么采取迁就各方的“最小公分母”策略，要么就得利用各种客户端检测方法，来突破或者规避种种局限性。只要能够找到更通用的方法，就应该优先采用更通用的方法。一言而蔽之，先设计最通用的方案，然后再使用特定于浏览器的技术增强该方案。

   客户端检测一共分为三种，分别为：能力检测、怪癖检测和用户代理检测，通过这三种检测方案，我们可以充分的了解当前浏览器所处系统、所支持的语法、所具有的特殊性能。

   **1.能力检测**

   最常用也是最为人们广泛接受的客户端检测形式是能力检测（又称特性检测）。能力检测的目标不是识别特定的浏览器，而是识别浏览器的能力。采用这种方式不必顾忌特定的浏览器如何，只要确定浏览器支持特定的能力，就可以给出解决方案。在实际开发中，应该将能力检测作为确定下一步解决方案的依据，而不是用它来判断用户使用的是什么浏览器。能力检测的基本模式如下：
   ```js
    if (object.propertyInQuestion) {
    //使用object.propertyInQuestion
    }
   ```
   两个重要的概念：
   第一个概念是先检测达成目的的最常用的特性。先检测最常用的特性，可以保证代码最优化，因为在多数情况下都可以避免测试多个条件。

   第二个概念是必须测试实际要用到的特性。一个特性存在，不一定意味着就是某一类浏览器，不意味着另一个特性也存在。

   检测某个或某几个特性并不能够确定浏览器。实际上，根据浏览器不同(考虑兼容性)将能力组合起来是更可取的方式。
   ```js
   //这里其实有两块地方使用了能力检测，第一个就是是否支持 innerWidth 的检测，第二个就是是否是标准模式的检测，这两个都是能力检测。
   var width = window.innerWidth; 　　　　//如果是非 IE 浏览器
   if (typeof width != 'number') { 　　//如果是 IE，就使用 document
        if (document.compatMode == 'CSS1Compat') {
            width = document.documentElement.clientWidth;
        } else {
            width = document.body.clientWidth; //非标准模式使用 body
        }
    }
    alert(width);
   ```
   如果知道自己的应用程序需要使用某些特定的浏览器特性，那么最好是一次性检测所有相关特性，而不要分别检测。
    ```js
    //确定浏览器是否支持 Netscape 风格的插件
    var hasNSPlugins = !!(navigator.plugins && navigator.plugins.length );
    //确定浏览器是否具有 DOM1 级规定的能力
    var hasDOM1 = !!(document.getElementById && document.createElement
                    && document.getElementByTagName);
    ```
    以上两个例子，一个检测是否支持`Netscape`风格的产检；另一个检测浏览器是否具备`DOM1`级所规定的能力。所得到的布尔值可以在以后继续使用，从而节省重新检测能力的时间。

    当然,更靠谱的检测时使用typeof来检测其特性的类型,而非仅仅通过类型转换来判断是否有这个属性.

    **2.怪癖检测**

    怪癖检测（quirks detection）的目标是识别浏览器的特殊行为。但与能力检测确认浏览器支持什么能力不同，怪癖检测是想要知道浏览器存在什么缺陷（“怪癖”也就是bug）。这个通常要运行一小段代码，以确定某一特性不能正常工作。(IE中存在的一个`bug`，即如果某个实例属性与标记为`[[DontEnum]]`的某个原型属性同名，那么该实例属性将不会出现在`fon-in`循环当中。)

    一般来说，“怪癖”都是个别浏览器所独有的，而且通常被归为 bug。在相关浏览器的版本中，这些问题可能会也可能不会被修复。由于检测“怪癖”涉及运行代码，因此建议仅检测那些对你有直接影响的“怪癖”，而且最好在脚本一开始就执行此类检测，以便尽早解决问题。

    **3.用户代理检测**

    通过检测用户代理字符串来确定实际使用的浏览器。在服务器端广为接受，但是在客户端，是一种万不得已才使用的做法，，其优先级排在能力检测和怪癖检测之后。JS中`navigator`对象的`userAgent`属性保存着客户端的相关信息。在每一次HTTP请求过程中，用户代理字符串是作为响应首部发送的，而该字符串可以通过`JavaScript`的`navigator.userAgent`属性访问。
    1）检测五大呈现引擎： Opera、WebKit、KHTML 和Gecko、IE;
    2）识别浏览器：IE、Firefox、Opera、Chrome、Safari、Konq;
    3）识别平台：(系统)windows、mac、xll；(移动设备)iphone、ipod、winMobile；(游戏设备)wii、ps等。

    参考:
    - [客户端检测](http://www.cnblogs.com/luwei2/archive/2013/03/17/CheckBrowser_clientTest.html)

18. 如何进行`JavaScript`代码的测试？

    答案：平时在测试方面做的比较少，一般用JSlint检查一些常见的错误。对于功能性的可能会使用基于karma+Jasmine测试框架来做。

    测试的大概流程是：在代码正式投入到生产环境之前，通过写一些测试用例，然后通过借助于测试框架，用来跑这些测试案例，如果测试通过了，就证明代码没有问题；如果测试通不过，就证明有问题，然后再修改，然后在跑测试用例，这样一个迭代，直到没有错误发生就可以了。之所以要借助测试框架，就是为了实现一个自动化的流程，如果是人工手动做做的话，需要手动去触发，比较麻烦。测试对整个软件的质量把控是很重要的。

19. 什么是变量自举(变量名提升、函数名提升)？

    答案：作用域，也就是我们常说的词法作用域，词法作用域永远都是在函数被声明的时候确定下来的.说简单点就是你的程序存放变量、变量值和函数的地方。  

    **1.块级作用域**

    简单说来就是，花括号{}括起来的代码共享一块作用域，里面的变量都对内或者内部级联的块级作用域可见。严格的说，在JavaScript也存在块级作用域。如下面几种情况:
      * with`with (obj) { a = 5; b = 5; c = 5; }`均作用于obj上 ;
      * let:是ES6新增的定义变量的方法`let bar = foo * 2`，其定义的变量仅存在于最近的{}之内;
      * const:与let一样，仅存在于最近的{}之内;唯一不同的是const定义的变量值不能修改;

    **2.基于函数的作用域**

    在JavaScript中，作用域是基于函数来界定的。也就是说属于一个函数内部的代码，函数内部以及内部嵌套的代码都可以访问函数的变量。
    ```js
    function foo(a){
      var b = a*2;
      function bar(c){
        console.log(a,b,c);
      }
      bar(b*3);
    }
    foo(2);//2,4,12
    ```
    这里顺便讲讲常见的两种error,ReferenceError和TypeError。如上图，如果在bar里使用了d，那么经过查询③、②、①都没查到，那么就会报一个ReferenceError；如果bar里使用了b，但是没有正确引用，如b.abc(),这会导致TypeError。

    **3.变量名提升**

    JS代码的执行过程分为编译过程和执行；在编译阶段，编译器会将函数里所有的声明都提前到函数体内的上部，而真正赋值的操作留在原来的位置上。需要注意的是，变量名提升是以函数为界的，嵌套函数内声明的变量不会提升到外部函数体的上部。
    ```js
    //变量名提升，初始化值的位置不变
    var foo = 1;
    function bar() {
      //相当于 var foo;if(!foo){foo = 10;}alert(foo);
	     if (!foo) {
		       var foo = 10;
	        }
	     alert(foo);
    }        
    bar();//输出10；

   //函数名提升，整个函数的定义提升
    var a = 1;
    function b() {
      //相当于 function a(){};a=10;return;
    	a = 10;
      alert(a);//输出10；
    	return;
    	function a() {}
    }
    b();
    alert(a);//输出1；作用域的问题。
    ```
    参考:
      - [JavaScript Scoping and Hoisting](http://www.adequatelygood.com/JavaScript-Scoping-and-Hoisting.html)
      - [聊一下JS中的作用域scope和闭包closure](http://www.cnblogs.com/front-Thinking/p/4317020.html)

20. `"use strict"`的作用？

    答案：作用是为了规范js代码，消除一些不合理、不严谨的地方；保证代码运行的安全；提高编译器效率，增加运行速度；为以后新版本js做铺垫。
    如何调用：
      1. 针对整个脚本文件`<script>`，将"use strict"放在脚本文件的第一行，则整个脚本都将以"严格模式"运行；
      2. 针对单个函数,将"use strict"放在函数体的第一行，则整个函数以"严格模式"运行；
      3. 因为第一种调用方法不利于文件合并，所以更好的做法是，借用第二种方法，将整个脚本文件放在一个立即执行的匿名函数之中。

    主要限制：
      1. 全局变量显式声明(var)；
      2. 禁止使用with;
      3. Javascript语言有两种变量作用域（scope）：全局作用域和函数作用域。严格模式创设了第三种作用域：eval作用域(eval语句本身就是一个作用域，它所生成的变量只能用于eval内部。)；
      4. 增强安全措施，比如禁止this关键字指向全局对象(严格模式下，this的值为undefined)；
      5. 禁止删除变量；
      5. 显式报错；比如对只读属性赋值，对一个使用getter方法读取属性赋值，对禁止扩展的对象添加新属性；
      6. 重名错误，对象不能有重名的属性，函数不能有重名的参数；
      7. 禁止八进制表示法；
      8. argument对象的限制；比如禁止使用arguments.callee,这意味着，你无法在匿名函数内部调用自身了；
      9. 函数必须声明在顶层,不允许在非函数的代码块内声明函数；
      10. 新增保留字：implements, interface, let, package, private, protected, public, static, yield

    参考:
      - [Javascript 严格模式详解](http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html)

21. `AJAX`请求的细节和原理？

    答案：Ajax并不算是一种新的技术，全称是asychronous javascript and xml，可以说是已有技术的组合。

    主要用来实现客户端与服务器端的异步通信效果，使用HTTP脚本来按需加载数据，实现页面的局部刷新。使用ajax原生方式发送请求主要通过 XMLHttpRequest(标准浏览器)、ActiveXObject(IE浏览器)对象实现异步通信效果。

    Ajax的工作原理相当于在用户和服务器之间加了—个中间层，使用户操作与服务器响应异步化;这样把以前的一些服务器负担的工作转嫁到客户端，利于客户端闲置的处理能力来处理;减轻服务器和带宽的负担，从而达到节约 ISP 的空间及带宽租用成本的目的;简单来说通过 XmlHttpRequest 对象来向服务器发异步请求;从服务器获得数据;然后用javascript来操作DOM而更新页面.这其中最关键的一步就是从服务器获得请求数据。要清楚这个过程和原理，我们必须对 XMLHttpRequest有所了解。

    基本步骤：
    * 创建 XMLHttpRequest 对象,也就是创建一个异步调用对象
    * 创建一个新的 HTTP 请求,并指定该HTTP请求的方法、URL 及验证信息
    * 设置响应 HTTP 请求状态变化的函数
    * 发送 HTTP 请求
    * 获取异步调用返回的数据
    * 使用 JavaScript 和 DOM 实现局部刷新
    ```js
    var xhr = null;//创建对象
    if (window.XMLHttpRequest) {
      xhr = new XMLHttpRequest();
    } else {
      xhr = new ActiveXObject("Microsoft.XMLHTTP");
    }
    xhr.open("方式","地址","标志位");//初始化请求
    xhr.setRequestHeader("","");//设置http头信息
    xhr.onreadystatechange = function() {}//指定回调函数
    xhr.send();//发送请求
    ```
    js框架（jQuery/EXTJS等）提供的 ajax API对原生的ajax进行了封装，熟悉了基础理论.

    参考：
    - [题库-Ajax](https://hongqinma.github.io/2015/08/03/e005-%E9%9D%A2%E8%AF%95%E9%A2%98%E5%BA%93-Ajax/)
    - [Javascript权威指南JQuery Ajax 552-562](http://www.w3school.com.cn/jquery/ajax_ajax.asp)

22. `AJAX`、`JSON`技术的优缺点。

    答案：Ajax是异步JavaScript和XML，用于在Web页面中实现异步数据交互。

    **优点：**

    * 可以使得页面不重载全部内容的情况下加载局部内容，降低数据传输量
    * 避免用户不断刷新或者跳转页面，提高用户体验

    **缺点：**

    * 对搜索引擎的支持比较弱(*在了解这个问题之前，我们要知道类似谷歌百度这类搜索引擎的工作原理。谷歌百度的工作原理大概可以概括为：爬取页面的HTML（单纯的HTML，并不会加载执行JS），分析并提取里面的关键信息并存入数据库。所谓SEO就是优化HTML的结构，使我们的网站HTML内容更易于爬虫去爬取，这样能提升网站在搜索引擎里的排名（rank）。但是，类似AngularJS、React JS这类MV*框架，因为他们的HTML文件只有一个，而且HTML里面什么信息都没有，所有的信息都在JS里，这样搜索引擎爬取过来的页面就是一个空空如也的页面，很不利于SEO（搜索引擎优化）。但是随着前端技术的发展，React现在已经支持服务端渲染了，对于搜索引擎，我们可以专门提供一个入口，这样就可以解决上面出现的问题。*)
    * 不支持浏览器back按钮,要实现ajax下的前后退功能成本较大
    * 可能造成请求数的增加
    * 跨域问题限制
    JSON是一种轻量级的数据交换格式，ECMA的一个子集

    **优点**：轻量级、易于人的阅读和编写，便于机器（JavaScript）解析，支持复合数据类型（数组、对象、字符串、数字）

23. 函数式编程是什么？

    答案："函数式编程"是一种"编程范式"（programming paradigm），也就是如何编写程序的方法论。它属于"结构化编程"的一种，主要思想是把运算过程尽量写成一系列嵌套的函数调用。
    ```js
    //数学表达式
    (1 + 2) * 3 - 4
    //传统过程式编程
    var a = 1 + 2;
　　var b = a * 3;
　　var c = b - 4;
    //函数式编程：要求使用函数，我们可以把运算过程定义为不同的函数
    var result = subtract(multiply(add(1,2), 3), 4);
    ```
    **特点：**
    1. 函数是"第一等公民"：函数与其他数据类型一样，处于平等地位，可以赋值给其他变量，也可以作为参数，传入另一个函数，或者作为别的函数的返回值
    2. 只用"表达式"，不用"语句"："表达式"（expression）是一个单纯的运算过程，总是有返回值；"语句"（statement）是执行某种操作，没有返回值。函数式编程只要求保持计算过程的单纯性，每一步都是单纯的运算，而且都有返回值。
    3. 没有"副作用"：函数要保持独立，所有功能就是返回一个新的值，没有其他行为，尤其是不得修改外部变量的值，不修改系统变量。
    4. 不修改状态：不修改变量，函数式编程使用参数保存状态，它演示了不同的参数如何决定了运算所处的"状态"。
    5. 引用透明：函数的运行不依赖于外部变量或"状态"，只依赖于输入的参数，任何时候只要参数相同，引用函数所得到的返回值总是相同的。

    **意义：**

    1. 代码简洁，开发快速：大量使用函数，减少了代码的重复；
    2. 接近自然语言，易于理解：函数式编程的自由度很高，可以写出很接近自然语言的代码；
    3. 更方便的代码管理：函数式编程不依赖、也不会改变外界的状态，只要给定输入参数，返回的结果必定相同。因此，每一个函数都可以被看做独立单元，很有利于进行单元测试（unit testing）和除错（debugging），以及模块化组合。
    4. 易于"并发编程"：函数式编程不需要考虑"死锁"（deadlock），因为它不修改变量，所以根本不存在"锁"线程的问题。不必担心一个线程的数据，被另一个线程修改，所以可以很放心地把工作分摊到多个线程，部署"并发编程"（concurrency）。
    5. 代码的热升级：只要保证接口不变，内部实现是外部无关的。所以，可以在运行状态下直接升级代码，不需要重启，也不需要停机。

    参考:
    [函数式编程初探](http://www.ruanyifeng.com/blog/2012/04/functional_programming.html)

24. 什么是函数柯里化(Currying)？

    答案：柯里化（Currying），是把接受多个参数的函数变换为接受单一参数（最初函数的第一个参数）的函数，并且返回 **接受余下的参数而且返回结果** 的新函数的技术。柯里化，参数部分使用。外部函数处理部分应用，剩下的由外部函数的返回函数处理。
    ```js
    //currying 只接受“苹果”参数，其他的参数，由fn函数接收并处理，返回结果
    //外部参数"苹果"；
    var currying = function(fn) {
        var args = [].slice.call(arguments, 1);
        return function() {
            var newArgs = args.concat([].slice.call(arguments));
            return fn.apply(null, newArgs);
        };
    };

    var getFruits = currying(function() {
        var allWife = [].slice.call(arguments);
        console.log(allWife.join(";"));
    }, "苹果");

    // 获得其他6个水果
    getFruits("橘子","山竹","草莓","榴莲","葡萄","西瓜");
    //输出"苹果"，"橘子","山竹","草莓","榴莲","葡萄","西瓜"
    getFruits("柚子","牛油果","香蕉","梨子","桃子","车厘子");
    //输出"苹果"，"柚子","牛油果","香蕉","梨子","桃子","车厘子"
    ```
    作用:
    1. 参数复用；在柯里化的外围函数中添加复用的参数即可("苹果")。
    2. 提前返回；参看“参考”中绑定事件的例子。
    3. 可以实现延迟计算/运行；其实Function.prototype.bind的方法中延迟计算就是运用的柯里化。

    参考:
    [函数式编程初探](http://www.ruanyifeng.com/blog/2012/04/functional_programming.html)

25. `jQuery`链式调用的原理
26. 解决异步回调地狱(`callback hell`)有哪些方法？
27. `Promise`的原理。
28. 介绍一下什么是`generator`，如何利用`generator`实现异步流程控制？
29. 说几个`ES6`的新特性。
30. 客户端存储及他们的异同(例如：`Cookie`, `Session`Storage和localStorage、webstorge等)
    参考:
    [使用JavaScript操作`Cookie`](http://www.fscwz.com/2015/11/10/JavaScript-`Cookie`/)
    [`Session`Storage localStorage 和 `Cookie` 之间的区别](https://zhidao.baidu.com/question/753078779171743764.html)
31. `DOM0``DOM1` `DOM2` `DOM3`都有什么不同。
32. 什么是`XSS`，如何在实际项目中防范？
33. 什么是`XSRF`， 如何在实际项目中防范？
34. `Object` ，`Array`和`String`对象的常用方法有哪些？
35. 数组如何实现去重、求交集、求并集和洗牌(`shuffle`)？
36. `JavaScript`的垃圾回收机制
37. 常见的`JavaScript`设计模式
38. 客户端如何与服务器时间同步？
39. 什么是`JavaScript`中的类数组对象(`arguments`对象)？
40. 在浏览器地址栏按回车、F5、Ctrl+F5刷新网页的区别
41. 如何判断两个对象是否相等？
42. 什么是`IIFE`(Immediately Invoked Function Expression，立即执行函数表达式)？有什么作用？
43. 变量的`null`，`undefined`和`undeclared`的区别是什么？如何检测它们？
44. 原生对象(native object)和宿主对象(host object)有什么区别？
45. 浏览器多个标签间如何通信？
46. `attribute`和 `property`的却别是什么？
47. `document load event` 和 `document DOMContentLoaded event`之间的区别是什么？
48. `== `与` ===`的区别。
49. 解释`JavaScript`异步实现的原理。
50. `escape()`, `decodeURIComponent()`, `decodeURI()`之间的区别是什么？
51. NodeJS如何利用CPU的多核，增强性能？
52. `ExpressJS `中间件原理。
53. script的三种加载方式。
54. js中switch语句中，用来判断的表达式可以是任意类型，而不仅限与整型。js中函数的参数传递方式都是按值传递，没有按引用传递的参数。但js中有保存引用的对象，比如数组。
变量作用域：值一个变量在程序中的那些地方可以被访问。js中的变量作用域被定义为函数作用域。这是值变量的值在定义该变量的函数内是可见的，并且定义在该函数内的嵌套函数中也可以访问该变量。在主程序中，如果在函数外定义一个变量，那么这个变量拥有全局作用域，这是值可以在包括函数体内的程序的任何部分访问该变量。递归并不是异步回调，还是同步执行。                                                                                  
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
3. `npm script`


## 综合性问题
1. 一个页面从输入 URL 到页面加载完的过程中都发生了什么事情？

2. `html`页面的渲染过程

3. 你是如何了解到并且学习一门技术的？

4. 你平时是如何学习前端的？

   前端技术发展很快, 工具层出不穷, 它们往往以低廉的代价专注于某个问题域给出了当时合理的解决方案, 在短时间内成为主流, 但往往由于标准的推进, 前端问题域的变化, 落后于更好的解决方案, 迅速过时.
   这启发我对于学习前端, 始终坚持这样一种策略:

   对新生工具和技术, 防御性地跟进了解, 作为技术储备
   对主流的解决方案, 分析调研目前的需求, 快速使用, 作为开发技能
   对过时的流行方案, 观察当时出现的背景和对应的问题域, 研究其核心思想, 作为分析参考

5. 你未来三年的计划

6. 讲一下你读过的和正在读或者研究的关于前端技术的书或者技术。

7. 你对前端的理解？你为什么学前端？

8. “渐进增强”和“优雅降级”指的是什么？

9. 什么是“FOUC”及如何避免

10. 什么是面向对象编程及面向过程编程，它们的异同和优缺点？

11. 面向对象的三大原则是什么？

12. 你在平时的学习和工作中有遇到过什么问题，你是如何解决的？

13. 对于现在前端发展日新月异，各种框架层出不穷，你是怎么看待的？

   提示：围绕基础最重要，不要盲目更新，适合项目需求才是最好的。
