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

    在默认情况下，HTTPS连接的cookie会自动加上secure这个参数。

    **HttpOnly Cookie机制**

    HttpOnly是指仅在HTTP层面上传输的Cookie,当设置了HttpOnly标志后，客户端脚本就无法读写该Cookie，这样能有效的防御XSS获取Cookie

    **cookie和session的区别：**

    1. Session保留在服务器中，cookie保留在客户端或浏览器中
    2. 在Session机制中，session是通过标示符(session id)来识别用户身份，维持会话的,所以session依赖于session id, 后者的保存方式采用cookie,在交互的过程中浏览器自动的按照规则把标识符发送给服务器，当cookie被用户禁止时，也可以采用URL重写的技术，即把session id附加在URL路径后面，附加的方式有两种，一种是作为URL路径的附加信息，一种是作为查询字符串附加在URL后面。
   参考:
   - [理解Cookie和Session机制](http://www.admin10000.com/document/7097.html)
   - [使用JavaScript操作Cookie](http://www.fscwz.com/2015/11/10/JavaScript-cookie/)

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

   **继承**：实现的本质是重写原型对象，而不使用默认提供的原型对象，代之以一个新类型的实例。利用原型链，继承别的对象的方法。类式继承和原型式继承
   ```js
   //类式继承
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

      /* -- 原型式继承 -- */
　　//clone()函数用来创建新的类Person对象
　　var clone =function(obj){
      var _f =function(){};
         　　//这句是原型式继承最核心的地方，函数的原型对象为对象字面量
         　　_f.prototype = obj;
         　　returnnew _f;
    　　}
    //先声明一个对象字面量
    var Person = {
       　　name:'Darren',
       　　getName:function(){
          　　returnthis.name;
       　　}
    }
    //不需要定义一个Person的子类，只要执行一次克隆即可
    var Programmer = clone(Person);
    //可以直接获得Person提供的默认值，也可以添加或者修改属性和方法
    alert(Programmer.getName())
    Programmer.name ='Darren2'
    alert(Programmer.getName())
    //声明子类,执行一次克隆即可
    var Someone = clone(Programmer);
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

  3.2 继承：类式继承和原型式继承
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
  alert(cat1.name); // 大毛
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
    - [函数式编程初探](http://www.ruanyifeng.com/blog/2012/04/functional_programming.html)

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
    - [JS中的柯里化(currying)](http://www.zhangxinxu.com/wordpress/2013/02/js-currying/)

25. `jQuery`链式调用的原理

    答案：jquery高效的原因之一就是链式调用。原理：通过对象上每个方法的最后返回本对象`return this`。因为返回的是同一对象，所以链式操作可以持续下去。类似`$("#id").css('color','red').show(200);`简单实现一个如下：
    ```js
      //定义一个JS构造函数
      function Demo() {}
      //扩展它的prototype
      Demo.prototype ={
          setName:function (name) {
              this.name = name;
              return this;
          },
          getName:function () {
              return this.name;
          },
          setAge:function (age) {
              this.age = age;
              return this;
          }
      };
      ////工厂函数
      function D() {
          return new Demo();
      }
      //去实现可链式的调用
      D().setName("CJ").setAge(18).getName();
    ```
    链式调用的好处：
    * 节省代码量，代码看起来更优雅；
    * 返回的都是同一个对象，可以提高代码的效率；（不用反复查找同一dom元素）
    * 另外还有一种就是让代码流程更清晰；
    * 因为Javascript是无阻塞语言，通过事件来驱动，异步来完成一些本需要阻塞进程的操作。异步编程，编写代码时也是分离的，这就使代码可读性变差。而链式操作，“选其对象，对其操作”的思路,代码流程清晰，改善了异步体验。

    回溯：有时你需要中间值而中断链式调用，或者链式调用中某个方法返回的this改变了，那么这就需要回溯了。回溯帮你维持this的正确指向。在jQuery中，回溯通过`end()`来实现.
    ```js
    var box=$('#box');
    box.find('p.info').text('This is a message.')...//find函数改变了this，this从box变为p.info；
    //如果你希望继续对box进行操作，那么就要用到回溯。
    box.find('p.info').text('This is a message.').end().find('p.result').text('ok');
    //end()能返回之前的对象，即这里的box，也即回溯。
    ```
    参考:
    - [链式调用与回溯](http://www.cnblogs.com/justany/archive/2013/01/17/2863101.html)

26. 解决异步回调地狱(`callback hell`)有哪些方法？

    答案：回调函数:意指先在系统的某个地方对函数进行注册，让系统知道这个函数的存在，然后在以后，当某个事件发生时，再调用这个函数对事件进行响应。回调函数使得我们可以轻易的写出异步执行的代码.

    **回调地狱:**

    回调地狱也称回调金字塔（指函数左边沿的缩进），指的是因为回掉函数的嵌套太多、过多的回调层级而造成的 **代码结构混乱** ,造成阅读和维护上的困难。

    其造成的第一个缺点是不匹配我们大脑的思维模式，因为大脑是单线程的，我们在思考问题的时候，会倾向于用同步的思维去理解这些异步的代码，会去判断哪个事件先执行，哪一个稍后执行，而代码结构的混乱，使得我们很难判断回调函数执行的顺序，也使得代码很难维护和更新（大括号太多变得无从下手）。所以我们要做的就是找到一些解决方案，使得我们可以流畅的进行代码阅读。

    **嵌套的回掉函数，是由其调用者来调用的。**
    ```js
    //获取文章的第一评论作者的信息
    function getUserInfoByArticleFirstCommentId(id){
      $.get("getArticle",{id:id},function(article){
        $.get("getComment",{id:getFirstCommentId(article)},function(comment){
          $.get("getUserInfo",{id:comment.author},function(user){
            //TODO
          });
        });
      });
    }
    ```
    **解决方案：**

    1. **具名函数**：使用具名函数并保持代码层级不要太深；
    ```js
    function fun3(err, c) {
    // ...do something with c in function 3
    }
    function fun2(err, b) {
        // ...do something with b in function 2
        asyncFun3(fun3);
    }
    function fun1(err, a) {
        //...do something with a in function 1
        asyncFun2(fun2);
    }
    asyncFun1(fun1);
    ```
    2. **Promise**：进阶一级的使用Promise或者链式Promise，但是还是需要不少的回调，虽然没有了嵌套；
    ```js
    asyncFun1().then(function(a) {
        // do something with a in function 1
        asyncFun2();
    }).then(function(b) {
        // do something with b in function 2
        asyncFun3();
    }).then(function(c) {
        // do somethin with c in function 3
    });
    ```
    3. **Async**：利用任务队列控制异步流程，使用async等辅助库，代价是需要引入额外的库，而且代码上也不够直观；
    ```js
    async.series([
        function(callback) {
            // do some stuff ...
            callback(null, 'one');
        },
        function(callback) {
            // do some more stuff ...
            callback(null, 'two');
        }
    ],
    // optional callback
    function(err, results) {
        // results is now equal to ['one', 'two']
    });
    ```

    4. **Generator**：ES6带来了新一代解决回调地狱的神器——Generator，本意上应该是一种方便按照某种规则生成元素的迭代器，不过鉴于其特殊的语法和运行原理(利用Generaotr可以暂停代码执行的特性，我们通过将异步操作用yield关键字进行修饰，每当执行异步操作的时候，代码便在此暂停执行了。异步操作结束后，通过在回调函数里利用next(data)来控制Generator的执行流程，并顺便将异步操作的结果data回传给Generator，执行下一步。到此，整个异步流程得到了完美的控制。)，可以通过某种神奇的方式写出同步化的异步代码，从而避免回调，使代码更易阅读。可以看到，经过Generator重写后，代码形式上和我们熟悉的同步代码没什么二样了。😁
    ```js
    //利用Generator获取文章的第一评论作者的信息
    function *getUserInfoByArticleFirstCommentId(id){
      var article = yield $.get("getArticle",{id:id});
      var comment = yield $.get("getComment",{id:getFirstCommentId(article)});
      var user = yield $.get("getUserInfo",{id:comment.author});
    }
    ```
    **虽然Async 和 Promise之流都在代码层面避免了地狱回调，但是代码组织结构上并没有完全摆脱异步的影子，和纯同步的写法相比，还是有很大的不同，写起来还是略麻烦。**

    参考:

    - [You Don't Know JS读书笔记之Async](http://aqua0706.github.io/2016/05/17/You-Don-t-Know-JS-Async-Performance1/)
    - [使用Generator解决回调地狱](http://www.alloyteam.com/2015/04/solve-callback-hell-with-generator/)

27. `Promise`的原理。

    答案：回调函数的 **问题** 在于他剥夺了我们使用`return`和`throw`，这类关键字的能力，把程序执行的控制权交给回调函数，从而带来反转控制（inversion of control）。而`Promise`很好的解决了这一问题。如下面这个例子，并不是直接给`foo()`传递一个回调函数，而是`return`一个接收回调函数的事件-`promise`。
    `promise`承诺我们一定可以在未来的某个时刻得到某个异步事件的结果，`fulfillment`或者`reject`，所以我们可以对结果安排一些之后要执行的操作，而不必担心这个异步事件什么时候执行完毕（time-independent）。这里p获得了这个promise执行的结果，我们可以通过then方法来决定reslove之后执行什么，来处理这些future value。inversion of inversion从而解决反转控制。
    ```js
    function foo(x) {
        // 这里可以进行一些操作，然后构造并返回一个promise对象
        //promise构造函数接收一个函数作为参数，该函数的两个参数分别是fulfillment方法和reject方法。
        return new Promise( function(resolve,reject){
            if(/*异步操作成功*/){
              resolve(value);//如果异步操作成功，则fulfillment方法将对象的状态从Pengding->Fulfilled
            }else{
              reject(error);//如果异步操作失败，则reject方法将对象的状态Pengding->Rejected
            }
        } );
    }
    var p = foo( 42 );//p是这个promise执行的结果
    p.then(function(value){
      //成功
    }，function(value){
      //failure
    }  );
    ```
    **什么是Promise？**

    * `promise`是一个对象，代表某个未来才知道结果的事件，通常是一个异步操作。future value/asynchronous task
    * 中立协商，promise event，提供统一的API(then之类的方法)，可以进行下一步处理。completion event

    **Promise对象的两个特点：**

    * 对象的状态不受外界影响：Promise对象代表一种异步操作，有三种状态Pengding(进行中)、Fulfilled(已完成)、Rejected(已失败)。只有异步操作的结果了一决定当前是那一种状态，任何其他操作都无法改变这个状态。而这些依赖时间的状态都被封装在promise内部，所以promise是time-independent的。不在乎结果、时间，都可以安排下一步的操作。
    * 一旦状态改变，就永久不会再变，任何时候都可以得到这个结果：Promise对象改变只有两种可能，Pengding->Fulfilled、Pengding->Rejected。当promise对象状态发生改变，再对对象添加回调函数，也会立即得到这个结果，这一点与（Event）完全不同！
    *事件的特点是：如果你错过它，再去监听，是得不到结果的。*

    **promise链–异步流序列–流控制**　(string multiple Promises together to represent a sequence of async steps)：
    * 每次调用then都会返回一个新创建的Promise对象；
    * then()这个函数会接受一个回调函数，这个回调函数返回的值将使then()返回的promise resolved(也就是完成了)，并且这个值会通过promise链传给下一个promise对象。
    * 使得promise在每一步真正异步的关键是我们传递的是一个promise对象或者是一个thenable，而不是一个可以立即获得的值。解包接收到的对象/thenable的过程是一个异步的过程。如果fulfillment、rejected返回的是一个对象，就解包，值传递。
    * error会在promise链中一直传播，知道遇到一个显示定义的拒绝处理程序-rejected。
    * then()方法，省略fulfillment就会默认接受什么值，传递什么值，省略reject就会默认throw这个错误。
    (new promise构造promise时第一个参数用resolve，then方法里面用fulfilled参数)

    参考:
    - [You Don't Know JS读书笔记之Promise](http://aqua0706.github.io/2016/06/06/You-Don-t-Know-JS-Async-Performance2/)
    - [JavaScript Promise迷你书（中文版）](http://liubin.org/promises-book/)

28. 介绍一下什么是`generator`，如何利用`generator`实现异步流程控制？

    答案：Generator Function(生成器函数)和Generator(生成器)是ES6引入的新特新。生成器的本质是一种特殊的迭代器。ES6里的迭代器并不是一种新的语法或者新的内置对象(构造函数)，而是一种协议，所有遵循了这个协议的对象都可以称之为[迭代器对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)。
    *迭代器协议定义了一种标准的方式来产生一个有限或无限序列的值；当一个对象被认为是一个迭代器时，它实现了一个next()方法，返回一个对象，被返回的对象拥有两个属性：done(boolean迭代器已经经过了被迭代序列时为 true,如果迭代器可以产生序列中的下一个值，则为 false)、value(迭代器返回的任何 JavaScript 值。done 为 true 时可省略)*

    **生成器的语法**

    生成器函数也是一种函数，语法上仅比普通function多了个星号* ，即function* ，在其函数体内部可以使用yield和yield* 关键字。
    ```js
    function* generateNaturalNumber() {
        console.log('function start');
        var i = 0;
        // 为了便于观察log，将循环减小到5
        while(i <= 5) {
            console.log('yield start');
            yield i;
            console.log('yield end');
            i++;
        }
        console.log('function end');
    }
    var result = generateNaturalNumber();
    ```
    运行以上代码发现什么log都没打印出来，继续打印result会得到类似这样的输出。
    ```js
    generateNaturalNumber {[[GeneratorStatus]]: "suspended", [[GeneratorFunction]]: function, [[GeneratorReceiver]]: Window}
    ```
    **生成器的原理**

    事实上result就是一个生成器，所以调用生成器函数必定会返回一个生成器，同时不会执行内部的任何代码。生成器函数内的代码在调用生成器的next方法时才执行。
    ![generator](/images/generator.png)

    输出的结果和使用普通函数的结果完全一样，只要`done`的值不是`true`就可以一直调用`next`。很直观的可以看到调用`next`返回一个`object`，包含两个属性`done`和`value`，符合迭代器协议。同时注意`log`，调用第一次`next`打印了`function start`和`yield start`，后续调用打印了`yield end`和`yield start`，最后一次调用`next`打印了`yield end`和`function end`，最后一次`next`是指`done`为`false`。

    **所以运行原理上是这样的:** 调用第一次`next`，从函数开头开始运行，直到遇到第一个`yield`，如果没有`yield`，就直接运行完整个函数。遇到`yield`则暂停运行，将`yield`后面的表达式求值之后返回，当作调用`next`返回的`value`属性值。调用第二次`next`，从上一次暂停处继续运行，直到遇到下一个`yield`，又暂停，以此循环，直到运行到`return`或者函数结尾，最后退出函数。

    **next**

    `generator.next(value)`则用来控制代码的运行并处理输入输出。上面讲了next的返回值，其实next也可以接受一个任意参数，该参数将作为上一个yield的返回值。yield作为一个关键字，也有返回值，其返回值就是下一次调用next传入的参数。**生成器的这个特性非常重要，利用该特性可以用同步的方式写出异步执行的代码，从而解决回调地狱（Callback Hell）的问题**
    ```js
    /**
     * @description 处理输入和输出
     */
    function * input(){
        let array = [];
        while(i) {
            array.push(yield array);
        }
    }
    var gen = input();
    console.log(gen.next("西")) // { value: [], done: false }
    console.log(gen.next("部")) // { value: [ '部' ], done: false }
    console.log(gen.next("世")) // { value: [ '部', '世' ], done: false }
    console.log(gen.next("界")) // { value: [ '部', '世', '界' ], done: false }
    ```
    **所以运行原理上是这样的:** 调用第一次`next`，从函数开头开始运行，直到遇到第一个`yield`，暂停运行，虽然传入了一个参数“西”，但并没有接收的对象，所以被丢弃了。将`yield`后面的表达式求值之后返回。调用第二次`next`，从上一次暂停处继续运行，接收一个参数“部”，“部”就是这次yield后面array的值，直到遇到下一个`yield`，暂停，输出“部”。以此循环，直到运行到`return`或者函数结尾，最后退出函数。

    **`yield`和`yield*`**
    * yield后面可以跟任何表达式，表达式的值将作为调用next返回值的value属性值。
    * yield* 后面只能跟迭代器，所以yield* genFun1会因为genFun1不是一个迭代器而报错。
    * yield* 的官方名字叫做Delegating yield。
    * yield* 的功能是将迭代控制权交给后面的迭代器，达到递归迭代的目的，就好比将genFun1的代码直接写在genFun2里面一样
    ```js
    function* genFun1() {
        yield 2;
        yield 3;
        yield 4;
    }
    function* genFun2() {
        yield 1;
        yield* genFun1();
        yield 5;
    }
    for(var i of genFun2()) {
        console.log(i);
    }//连续输出 1，2，3，4，5
    ```

    **如何利用`Generator`进行异步流程控制？**

    利用`Generaotr`可以暂停代码执行的特性，我们通过将异步操作用`yield`关键字进行修饰，每当执行异步操作的时候，代码便在此暂停执行了。异步操作结束后，通过在回调函数里利用`next(data)`来控制`Generator`的执行流程，并顺便将异步操作的结果`data`回传给`Generator`，执行下一步。到此，整个异步流程得到了完美的控制。
    这里我们需要一个运行器，运行器接受一个`Generator`函数，实例化一个`Generator`对象，然后启动任务，通过`next()`取得返回值，这个返回值其实是一个函数，提供了一个入口可以让我们可以方便的注入控住逻辑，包括：控制`Generator`向下执行、将异步执行的结果返回给`Generator`。
    `co`是一个基于Generator（生成器）的异步流控制器，可以完美实现写出非阻塞的“同步代码”的目的。
    ```js
    function co(genFun) {
        // 通过调用生成器函数得到一个生成器
        var gen = genFun();
        return function(fn) {
            next();
            function next(err, res) {
                if(err) return fn(err);
                // 将res传给next，作为上一个yield的返回值
                var ret = gen.next(res);
                // 如果函数还没迭代玩，就继续迭代
                if(!ret.done) return ret.value(next);
                // 返回函数最后的值
                fn && fn(null, res);
            }
        }
    }
    ```
    ```js
    co(function*() {
        var a = yield asyncFun1();
        var b = yield asyncFun2(a);
        var c = yield asyncFun3(b);
        // do somethin with c
    })();
    ```
    参考:

    - [利用Generator实现JavaScript异步流程控制](http://natumsol.github.io/2016/11/21/use-generator-to-control-asynchronous-code/#利用Thunk来构造generator自动运行器)
    - [ES6 Generator介绍](http://www.alloyteam.com/2015/03/es6-generator-introduction/)

29. 说几个`ES6`的新特性。

    答案：es6增加了箭头函数，增加了解构赋值，增加了generator，增加了一些新的数据类型，比如说map。set等。
    1. 语法糖；
    2. 解构；
    3. let、const；
    4. For-Of以及数组理解；
    5. 箭头函数；
    6. 延伸操作符、剩余变量、以及默认参数；
    7. 类：ES6同样支持继承和扩展类
    8. 模块
    9. 映射和集合
    10. 迭代器：像定义一个函数一样定义一个迭代器，但是在函数名和function关键字之间有一个*号，使用这种方法，作用域被挂起，并且你可以通过在返回值上调用next()来得到下一个值或者迭代它(generator);
    11. 弱映射
    12. proxies
    13. 模板字符串
    14. 标签

    参考:

    - [ES6中的新特性](http://www.html-js.com/article/The-new-characteristics-of-8-ES6-JavaScript-every-day-to-learn)
    - [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/simd)

30. 客户端存储及他们的异同(例如：Cookie, SessionStorage和localStorage、webstorage等)

    答案：共同点：都是保存在浏览器端，且同源的。

    **区别：**

    1. cookie数据在浏览器和服务器间来回传递,和服务器端进行交互的，一般用于标识用户身份。而sessionStorage和localStorage用于浏览器缓存数据，不会自动把数据发给服务器，在本地保存。
    2. 由于Cookie是作为HTTP规范的一部分存在的，每次携带都会在HTTP头中，如果Cookie保存过多数据会带来性能问题，而WebStorage仅在客户端保存，不参与服务器的通信。一般浏览器不会修改cookie,但是会频繁操作cookie.
    2. cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。
    3. 存储大小限制也不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，一般为5M左右。
    4. 数据有效期不同，sessionStorage仅在当前浏览器窗口关闭前有效，刷新页面后数据依旧存在，但关闭页面或关闭浏览器后就清除了,自然也就不可能持久保持；localStorage则是持久化的本地存储，除非被清除掉否则永久保存，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。
    5. 作用域不同，sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的。
    6. Web Storage 支持事件通知机制（storage事件），可以将数据更新的通知发送给监听者。Web Storage 的 api 接口使用更方便。(注：Web Storage实际上由两部分组成：sessionStorage与localStorage。)

      Cookie的操作需要自己封装getCookie,setCookie等，而Web Storage拥有setItem, getItem等方法可以使用，比如：
      localStorage.setItem(“a”,”xxxxxx”); //设置
      localStorage.getItem(“a”); //获取a的值
      localStorage.removeItem(“a”); //删除a的值
      document.cookie = "username=hyc";

    参考:
    - [使用JavaScript操作`Cookie`](http://www.fscwz.com/2015/11/10/JavaScript-cookie/)
    - [`Session`Storage localStorage 和 `Cookie` 之间的区别](https://zhidao.baidu.com/question/753078779171743764.html)

31. `DOM1` `DOM2` `DOM3`都有什么不同。

    答案：文档对象模型是一种与编程语言及平台无关的API(Application programming Interface)，借助于它，程序能够动态地访问和修改文档内容、结构或显示样式。W3C协会早在1988年就开始了DOM标准的制定，W3C DOM标准可以分为DOM1,DOM2,DOM3三个版本。

    DOM1级主要定义的是HTML和XML文档的底层结构。DOM2和DOM3级别则在这个结构的基础上引入了更多的交互能力，也支持了更高级的XML特性。为此DOM2和DOM3级分为许多模块（模块之间具有某种关联），分别描述了DOM的某个非常具体的子集。

    DOM0:事件是元素的方法；DOM2:添加事件绑定与删除函数，有DOM2级事件流；DOM3:在DOM2级事件的基础上重新定义了这些事件，也添加了一些新事件，还添件了自定义事件。

    1、DOM0级(DOM1级之前)事件处理方式：

    通过javascript制定事件处理程序的传统方式。就是将一个函数赋值给一个事件处理属性。第四代web浏览器出现，至今为所有浏览器所支持。优点，简单且具有跨浏览器的优势。
    ```js
    var btn = document.getElementById("btn");
              btn.onclick = function(){
                  alert(this.id);//this指定当前元素btn
    }
    ```
    删除DOM0事件处理程序，只要将对应事件属性置为null即可。`btn.onclick = null;`

    *缺点：一个事件处理程序只能对应一个处理函数。*

    2、DOM2级事件处理方式

    DOM2级事件处理方式指定了，添加事件处理程序和删除事件处理程序的方法。分别是：
    * addEventListener(eventName,func,isPuhuo);
    * removeEventListener(eventName,func,isPuhuo);

    ```js
    var btn = document.getElementById("btn");
    handler = function(){
        ……
    }
    addEventListener("click",handler,false/true);
    removeEventListener("click",handler,false/true);
    ```
    参数分别是，事件处理属性名称，处理函数，是否在捕获时执行事件处理函数。

    a)  eventName的值均不含on,例如注册鼠标点击事件eventName为“click”

    b)  处理函数中的this依然指的是指当前dom元素

    c)  通过addEventListener添加的事件处理程序，只能通过removeEventListener来删除，也就是说通过addEventListener添加的匿名函数将无法被删除。

    d)IE中的DOM2级事件处理使用了attachEvent和detachEvent来实现。这俩个方法接受俩个相同的参数，事件处理名称和事件处理函数。IE8级更早版本只支持冒泡型事件，所以attachEvent添加的事件都会被添加到冒泡阶段。
    ```js
    var btn = document.getElementById("btn");
    btn.attachEvent("onclick",function(){
            alert(this);//此处this是window
    });
    ```
    **注意:** 通过attachEvent添加的事件第一个参数是“onclick”而非标准事件中的“click”。在使用attachEvent方法和DOM0级事件处理程序的主要区别在于事件处理程序的作用域。采用DOM0级处理方式，事件处理程序会在其所属元素的作用域内运行。使用attachEvent，事件处理程序会在全局作用域内运行，因此this等于window。

    3、DOM3事件

    DOM浏览器中可能发生的事件有很多种，不同事件类型具有不同的信息，DOM3级事件规定了一下几种事件：
        1）UI事件，当用户与页面上的元素交互时触发；
        2）焦点事件，当元素获得或者失去焦点时触发；
        3）鼠标事件，当用户通过鼠标在页面上执行操作时触发；
        4）滚轮事件，当使用鼠标滚轮（或类似设备）时触发；
        5）文本事件，当在文档中，输入文本时触发；
        6）键盘事件，当用户通过键盘在页面上执行操作时触发；
        7）合成事件，当为IME（Input Method Editor，输入法编辑器）输入字符时触发；
        8）变动事件，当底层Dom结构发生变化时触发；
        DOM3级事件模块在DOM2级事件的基础上重新定义了这些事件，也添加了一些新事件。包括IE9在内的主流浏览器都支持DOM2级事件，IE9也支持DOM3级事件。
    **DOM中的事件模拟（自定义事件）：**

    DOM3级还定义了自定义事件，自定义事件不是由DOM原生触发的，它的目的是让开发人员创建自己的事件。要创建的自定义事件可以由createEvent("CustomEvent");返回的对象有一个initCustomEvent（）方法接收如下四个参数。
        1）type：字符串，触发的事件类型，自定义。例如 “keyDown”，“selectedChange”;
        2）bubble（布尔值）：标示事件是否应该冒泡；
        3）cancelable(布尔值)：标示事件是否可以取消；
        4）detail（对象）：任意值，保存在event对象的detail属性中；
    可以像分配其他事件一样在DOM中分派创建的自定义事件对象。
    ```js
      var  div = document.getElementById("myDiv");
      EventUtil.addEventHandler(div,"myEvent", function () {
      alert("div myEvent!");
      });
      EventUtil.addEventHandler(document,"myEvent",function(){
      alert("document myEvent!");
      });
      if(document.implementation.hasFeature("CustomEvents","3.0")){
      var e = document.createEvent("CustomEvent");
      e.initCustomEvent("myEvent",true,false,"hello world!");
      div.dispatchEvent(e);
      }
    ```
    这个例子中创建了一个冒泡事件“myEvent”。而event.detail的值被设置成了一个简单的字符串，然后在div和document上侦听该事件，因为在initCustomEvent中设置了事件冒泡。所以当div激发该事件时，浏览器会将该事件冒泡到document。

    参考:

      - [DOM0，DOM2，DOM3事件处理方式区别](http://www.qdfuns.com/notes/11861/e21736a0b15bceca0dc7f76d77c2fb5a.html)

32. 什么是`XSS`，如何在实际项目中防范？

    答案：跨站脚本攻击（XSS，Cross-site scripting）是最常见和基本的攻击Web网站的方法。目标是让其他站点的js文件运行在目标站点的上，这主要发生在页面渲染阶段。在该阶段发生了某些非预期的脚本行为，该脚本可能来自用户的输入，也可能来自域外的其他js文件，不一而足。因此XSS根据用户输入数据以何种形式、何时触发XSS、是否有后端服务器的参与划分为三种类型，分别是反射型XSS、持久型XSS和DOM XSS。

    xss就是“在页面执行你想要的js”，也就是说，只要遵循一个原则——后端永远不信任前端输入的任何信息，无论是输入还是输出，都对其进行html字符的转义，那么漏洞就基本不存在了。

    反射型XSS

    反射型XSS，顾名思义在于“反射”这个一来一回的过程。反射型XSS的触发有后端的参与，而之所以触发XSS是因为后端解析用户在前端输入的带有XSS性质的脚本或者脚本的data URI编码，后端解析用户输入处理后返回给前端，由浏览器解析这段XSS脚本，触发XSS漏洞。因此如果要避免反射性XSS，需要服务端和前端共同预防，在后端解析前端的数据时首先做相关的字串检测和转义处理；针对用户输入的数据做解析和转义，对于前端开发而言，则是善于使用escape，针对data URI内容做正则判断，禁止用户输入非显示信息，如MIME类型为“text/html，text/plain”类型的内容,保证数据源的可靠性。
    ```js
    e.x.localhost/test.php
    <?php echo $_GET['name'] ?>
    如果通过
    localhost/test.php?name=<script>alert(document.cookie)</script>
    访问页面，那么经过后端服务器的处理，就会造成反射性XSS的发生。
    ```
    同理，通过传入data uri编码的字符串也会导致XSS，如
    `localhost/test.php?name=data:text/html;charset=utf-8;base64,PHNjcmlwdD5hbGVydChkb2N1bWVudC5jb29raWUpPC9zY3JpcHQ+``
    会导致同样的问题。该段编码的字串解码后是“<script>alert(document.cookie)</script>”。

    持久型XSS

    持久型XSS仍然需要服务端的参与，它与反射型XSS的区别在于XSS代码是否持久化（硬盘，数据库）。反射型XSS过程中后端服务器仅仅将XSS代码保存在内存中，并未持久化，因此每次触发反射性XSS都需要由用户输入相关的XSS代码；而持久型XSS则仅仅首次输入相关的XSS代码，保存在数据库中，当下次从数据库中获取该数据时在前端未加字串检测和excape转码时，会造成XSS，而且由于该漏洞的隐蔽性和持久型的特点，在多人开发的大型应用和跨应用间的数据获取时造成的大范围的XSS漏洞，危害尤其大。这就需要开发人员培养良好的WEB前端安全意识，不仅仅不能相信用户的输入，也不能完全相信保存在数据库中的数据（即后端开发人员忽视的数据安全检测）。针对持久型XSS没有好的解决方式，只能由开发人员保证。当然规则是由开发者制定，如果忽略用户体验的话，可以制定一套严谨的输入规则，对相关关键词和输入类型（如data URI检测，禁止输入）的检测和禁止，尽可能规避用户发现XSS漏洞的可能性，从源头处理。

    DOM XSS

    DOM XSS完全在前端浏览器触发，无需服务端的参与，因此这是前端开发工程师的“地盘”，理应获得我们的关注。
    ```js
    e.x.localhost/test.html

    <script>
    eval('alert(location.hash.slice("1"))');
    </script>
    ```
    如果访问localhost/test.html#document.cookie ，那么就会触发最简单的危害非常大的DOM XSS。它完全没有服务端的参与，仅仅由用户的输入和不安全的脚本执行造成，当然在本例中仅仅是最简单的情况，如果用户输入字符串‘<script src="http://.../abc.js"></script>’或者text/html格式的data URI，则更难检测，也危害更大，黑客操作起来更为容易。

    因此预防DOM XSS，需要前端开发人员警惕用户所有的输入数据，做到数据的escape转义，同时尽可能少的直接输出HTML的内容；不用eval、new Function、setTimeout等较为hack的方式解析外站数据和执行js脚本；如果在考虑安全性的前提下需要获取外站脚本的执行结果，可以采用前端沙盒（建立空的iframe执行脚本，该iframe无法操作当前文档对象模型）、worker线程的方式完成，保证DOM的安全。

    对于DOM XSS，则需要慎之又慎。由于造成XSS的原因在于用户的输入，因此在前端，需要特别注意以下的用户输入源：`document.URL`,`location.hash`,`location.research`,`document.referrer`(此处应尤为注意，referrer属性虽然可用于避免CSRF，但可触发XSS攻击),`XHR返回值`（跨域返回值）,`form表单及各种input框`,针对以上输入源，需要做相对于的检测和转义。在以上输入源中获取数据后，可能会有各种DOM操作或纯粹的js计算，这些操作则是真正触发XSS的罪魁祸首：

    1,直接输出HTML内容
    ```JS
      document.body.innerHTML = ...
      document.body.outterHTML = ...
      document.write()
    ```

    2,HTML标签内联脚本
    ```JS
      <a href="" onclick="handleClick();"></a>
      <img src='abc' onerror=alert('error')>
    ```

    3,直接执行脚本
    ```JS
      eval
      new Function(){}
      setTimeout()
      window.execScript()
    ```

    4,打开新页面触发XSS（包括反射型XSS和持久型XSS）
    ```js
      window.open()
      location.href = ...
      location.hash = ...
    ```
    在操作DOM时，需要尤其注意上述操作，针对可能造成的XSS需要进行字串转义。当然，有些操作是完全可以避免的：对于innerHTML的拼接操作，需要摒弃jQuery式的链式操作而使用前端模版如artTemplate，也可选择使用由后端渲染好的可靠的数据，这样既保证性能也确保安全；对于HTML标签内嵌js，则需要完全避免，这是一种容错率很低的实现；直接执行脚本和解析数据，则需避免eval和new Funciton等操作，改为JSON.parse、iframe沙盒和webWorker执行；而针对打开新页面触发的XSS则需要开发人员自行把控。

    参考:

      - [XSS分析及预防](https://segmentfault.com/a/1190000005032978)
      - [XSS零碎指南](http://www.cnblogs.com/hustskyking/p/xss-snippets.html)

33. 什么是`XSRF`， 如何在实际项目中防范？

    答案：Cross-Site Request Forgery，跨站点伪造请求(通常缩写为 CSRF 或者 XSRF)是一种网络攻击方式，该攻击通过伪装来自受信任用户的请求来利用受信任的网站,可以在受害者毫不知情的情况下以受害者名义伪造请求发送给受攻击站点，从而在未授权的情况下执行在权限保护之下的操作，具有很大的危害性。

    CSRF 攻击类似 XSS 攻击，都是在页面中嵌入特殊部分引诱或强制用户操作从而得到破坏等的目的，区别就是迫使用户 访问特定 URL / 提交表单 还是执行 javascript 代码。

    具体来讲，可以这样理解CSRF攻击：攻击者盗用了你的身份，以你的名义发送恶意请求，对服务器来说这个请求是完全合法的，但是却完成了攻击者所期望的一个操作，比如以你的名义发送邮件、发消息，盗取你的账号，添加系统管理员，甚至于购买商品、虚拟货币转账等。

    比如你在你的浏览器上登陆了网上银行，这个时候有人通过电子邮件给你发了一个链接，这个链接是对你这个银行的一个操作，比如说一个转账操作，这个链接后赋了几个参数，比如说，from等于你的账号，to等于他的账号，money=1000，因为你已经登陆了，当你点击这个链接，黑客页面上放了一个按钮或者一个表单，引导或迫使甚至伪造用户触发按钮或表单，来触发转账事件。在浏览器发出 GET 或 POST 请求的时候，它会带上当前站点的 cookie，如果网站没有做 CSRF 防御措施，那么这次请求在银行看来会是完全合法的，就会执行转账的操作。这就完成了一个跨站请求伪造的攻击。

    **如何防止 CSRF ？**
    1. 增加攻击的难度。

      GET请求是很容易创建的，用户点击一个链接就可以发起GET类型的请求，而POST请求相对比较难，攻击者往往需要借助JavaScript才能实现；因此，确保form表单或者服务端接口只接受POST类型的提交请求，可以增加系统的安全性。
    2. 在请求地址中添加token并验证

      CSRF攻击之所以能够成功，是因为攻击者可以伪造用户的请求，该请求中所有的用户验证信息都存在于Cookie中，因此攻击者可以在不知道这些验证信息的情况下直接利用用户自己的Cookie来通过安全验证。由此可知，抵御CSRF攻击的关键在于：在请求中放入攻击者所不能伪造的信息，并且该信息不存在于Cookie之中。鉴于此，系统开发者可以在HTTP请求中以参数的形式加入一个随机产生的token，并在服务器端建立一个拦截器来验证这个token，如果请求中没有token或者token内容不正确，则认为可能是CSRF攻击而拒绝该请求。CSRF 主流防御方式是在后端生成表单的时候生成一串随机 token ，内置到表单里成为一个字段，同时，将此串 token 置入 session 中。每次表单提交到后端时都会检查这两个值是否一致，以此来判断此次表单提交是否是可信的。提交过一次之后，如果这个页面没有生成 CSRF token ，那么 token 将会被清空，如果有新的需求，那么 token 会被更新.攻击者可以伪造 POST 表单提交，但是他没有后端生成的内置于表单的 token，session 中有没有 token 都无济于事。

      对请求进行认证，确保该请求确实是用户本人填写表单或者发起请求并提交的，而不是第三者伪造的。正常情况下一个用户提交表单的步骤如下：
      * 用户点击链接(1) -> 网站显示表单(2) -> 用户填写信息并提交(3) -> 网站接受用户的数据并保存(4)而一个CSRF攻击则不会走这条路线，而是直接伪造第2步用户提交信息
      * 直接跳到第2步(1) -> 伪造要修改的信息并提交(2) -> 网站接受攻击者修改参数数据并保存(3)只要能够区分这两种情况，就能够预防CSRF攻击。那么如何区分呢？ 就是对第2步所提交的信息进行验证，确保数据源自第一步的表单。具体的验证过程如下：
      * 用户点击链接(1) -> 网站显示表单，表单中包含特殊的token同时把token保存在session中(2) -> 用户填写信息并提交，同时发回token信息到服务端(3) -> 网站比对用户发回的token和session中的token，应该一致，则接受数据，并保存
      这样，如果攻击者伪造要修改的信息并提交，是没办法直接访问到session的，所以也没办法拿到实际的token值；请求发送到服务端，服务端进行token校验的时候，发现不一致，则直接拒绝此次请求。

    3. 验证HTTP Referer字段

      还可以通过验证HTTP Referer字段，根据HTTP协议，在HTTP头中有一个字段叫Referer，它记录了该HTTP请求的来源地址。在通常情况下，访问一个安全受限页面的请求必须来自于同一个网站。比如某银行的转账是通过用户访问http://bank.test/test?page=10&userID=101&money=10页面完成，用户必须先登录bank. test，然后通过点击页面上的按钮来触发转账事件。当用户提交请求时，该转账请求的Referer值就会是转账按钮所在页面的URL（本例中，通常是以bank. test域名开头的地址）。而如果攻击者要对银行网站实施CSRF攻击，他只能在自己的网站构造请求，当用户通过攻击者的网站发送请求到银行时，该请求的Referer是指向攻击者的网站。因此，要防御CSRF攻击，银行网站只需要对于每一个转账请求验证其Referer值，如果是以bank. test开头的域名，则说明该请求是来自银行网站自己的请求，是合法的。如果Referer是其他网站的话，就有可能是CSRF攻击，则拒绝该请求。

    参考:

      - [CSRF CORS](http://www.lxway.com/482281211.htm)

34. `Object` ，`Array`和`String`对象的常用方法有哪些？

    答案：**Object:**

    JavaScript 原生对象，所有的其他对象都继承自这个对象。Object本身也是有个构造函数，可以直接通过它来生成新对象.

    1. var o = new Object();(同o = {};字面量写法)
      接受一个参数，如果带参数是一个对象，就直接返回改对象，如果是一个原始类型值，就返回该值对应的包装对象。如：new Object(123) instanceof Number //true；
    2. Object()可以将任何值转换为对象，常用于保证某个值一定是对象。
      ```js
      Object() // 返回一个空对象
      Object() instanceof Object // true

      Object('foo') // 等同于 new String('foo')
      Object('foo') instanceof Object // true
      Object('foo') instanceof String // true

      var arr = [];
      Object(arr) // 返回原数组
      Object(arr) === arr // true
      //判断变量是否为对象的函数
      function isObject(value) {
        return value === Object(value);
      }
      isObject([]) // true
      isObject(true) // false
      ```
    3. 静态方法：Object.keys(),Object.getOwnPropertyNames()一般用来遍历对象的属性。它们的参数都是一个对象，都返回一个数组，该数组的成员都是对象自身的（而不是继承的）所有属性名。它们的区别在于，Object.keys方法只返回可枚举的属性（关于可枚举性的详细解释见后文），Object.getOwnPropertyNames方法还返回不可枚举的属性名。
    ```js
    var a = ["Hello", "World"];
    Object.keys(a)// ["0", "1"]
    Object.getOwnPropertyNames(a)// ["0", "1", "length"]
    ```
    4. 实例方法：Object.prototype.valueOf()返回一个对象的“值”，默认情况下返回对象本身。主要用途是，JavaScript自动类型转换时会默认调用这个方法，所以，如果自定义valueOf方法，就可以得到想要的结果。Object.prototype.toString()，toString方法的作用是返回一个对象的字符串形式，默认情况下返回类型字符串。其中第二个Object表示该值的构造函数,因此可以用来判断一个值的类型。比typeof运算符更准确.
    ```js
    var o = {};
    o.toString() // "[object Object]"
    //数组、字符串、函数、Date对象都分别部署了自己版本的toString方法，覆盖了Object.prototype.toString方法。
    [].toString()//"[object Number]"
    ```

    **Array:**

    1. JavaScript 内置对象，同时也是一个构造函数，可以用它生成新数组。
    2. js中数组中的元素不必是同一种数据类型。用`Array.isArray()`来判断一个对象是否为数组。它可以弥补typeof运算符的不足

    **数组的存取函数：(返回目标数组的某种变体)**

    1. `Array.indexOf()` 返回元素索引/-1不存在(如果数组包含多个相同元素，返回第一个与参数相同的元素的索引)；
    2. `Array.lastIdexOf()` 返回相同元素中最后一个元素的索引/-1不存在；
    3. 数组转换为字符串：`Array.join()`、`Array.toString()` 返回一个包含数组所有元素的字符串，个元素之间用逗号分隔开。`Array.join("")`就没有逗号了。
    4. 已有数组创建新数组：`Array.concat()`合并多个数组创建新数组(数组本身不变)、`Array.slice()`提取原数组的一部分，返回一个新数组，原数组不变。`slice`方法的一个重要应用，是将类似数组的对象转为真正的数组。
    ```js
    Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })
    // ['a', 'b']
    ```

    **可变函数：(数组本身发生变化)**

    1. 为数组添加元素：`Array.push()`数组末尾添加、`Array.unshift()`数组开头添加；
    2. 从数组中删除元素：`Array.pop()`删除数组末尾、`Array.shift()`删除数组开头；
    3. 从数组中间位置添加和删除元素：`Array.splice()`即可添加又可删除(参数1：起始索引；参数2：删除的个数--添加元素时为0；参数3：想要添加进数组的元素)；
    4. 为数组排序：`Array.reverse()`反转元素顺序、`Array.sort()`按照字符串类型排序，可以传入比较函数。按照number类型排序；

    **迭代器方法：(对数组中的每个元素应用一个函数，可以返回一个值、一组值、或者一个数组)**

    **不生成新数组：**(执行某种操作，或返回一个值)

    1. `Array.forEach(callback)`:接收一个函数作为参数，对数组中的每个元素使用该函数；
    2. `Array.every()`:接收一个返回值为boolean类型的函数，对数组中的每个元素使用该函数，如果对于所有的函数，该函数均返回true，则该方法返回true；
    3. `Array.some()`:接收一个返回值为boolean类型的函数，对数组中的每个元素使用该函数，只要有一个元素使得该函数返回true，则该方法返回true；
    4. `Array.reduce()`:接收一个函数，返回一个值，从左到右执行，新返回+后一个元素，一起调用callback；
    5. `Array.reduceRight()`:同上，但是从右到左执行。

    **生成新数组：**

    1. `Array.map()`:对数组中的每个元素使用某个函数，并将结果映射到一个新的数组中返回；
    2. `Array.filter()`:传入布尔函数，返回包含结果为true的所有元素的新数组。

    **String:**
    所谓“包装对象”，就是分别与数值、字符串、布尔值相对应的Number、String、Boolean三个原生对象。这三个原生对象可以把原始类型的值变成（包装成）对象。
    * String对象是JavaScript原生提供的三个包装对象之一，用来生成字符串的包装对象
    * String.length属性返回字符串的长度。
    * String.charAt(index)返回指定位置的字符，参数是从0开始编号的位置。
    * String.charCodeAt()方法返回给定位置字符的Unicode码点（十进制表示）。
    * String.concat()方法用于连接两个字符串，返回一个新字符串，不改变原字符串。
    * String.slice()用于从原字符串取出子字符串并返回，不改变原字符串。(开始位置，结束位置)
    * String.substring()用于从原字符串取出子字符串并返回，不改变原字符串。(开始位置，结束位置)
    * String.substr()用于从原字符串取出子字符串并返回，不改变原字符串。(开始位置，长度)
    * String.indexOf()、String.lastIndexOf()确定一个字符串在另一个字符串中的位置，都返回一个整数，表示匹配开始的位置。如果返回-1，就表示不匹配。两者的区别在于，indexOf从字符串头部开始匹配，lastIndexOf从尾部开始匹配。
    * String.trim()用于去除字符串两端的空格，返回一个新字符串，不改变原字符串。
    * String.toLowerCase()、String.toUpperCase()一个字符串全部转为小写/大写，它们都返回一个新字符串，不改变原字符串。
    * String.localeCompare()方法用于比较两个字符串,会考虑自然语言的顺序。举例来说，正常情况下，大写的英文字母小于小写字母。
    * String.match()确定原字符串是否匹配某个子字符串，返回一个数组，成员为匹配的第一个字符串。如果没有找到匹配，则返回null。
    * String.search()用法等同于match，但是返回值为匹配的第一个位置。如果没有找到匹配，则返回-1。
    * String.replace()用于替换匹配的子字符串，一般情况下只替换第一个匹配（除非使用带有g修饰符的正则表达式）。
    *  String.split()字符串生成数组，丢弃分隔符。
    参考:

      - [Object对象](http://javascript.ruanyifeng.com/stdlib/object.html)
      - [Array 对象](http://javascript.ruanyifeng.com/stdlib/array.html)
      - [String对象](http://javascript.ruanyifeng.com/stdlib/string.html)

35. 数组如何实现去重、数组判断、求交集、求并集、求差集和洗牌(`shuffle`)？

    答案：
    **数组去重**

    Array类型并没有提供去重复的方法，如果要把数组的重复元素干掉，那得自己想办法：
    ```js
    //利用数组的indexOf方法
    function unique (arr) {
      var result = [];
      for (var i = 0; i < arr.length; i++)
      {
        if (result.indexOf(arr[i]) == -1) result.push(arr[i]);
      }
      return result;
    }

    //利用hash表,可能会出现字符串和数字一样的话出错，如var a = [1, 2, 3, 4, '3', 5],会返回[1, 2, 3, 4, 5]
    //这里用对象，键值对模拟hash表，如果arr[i]不在我的键里面，我就将结果放进result，这里注意3和“3”作为键是一样的，系统会做强制转换~
    function unique (arr)
    {
        var hash = {},result = [];
        for(var i = 0; i < arr.length; i++)
        {
            if (!hash[arr[i]])
            {
                hash[arr[i]] = true;
                result.push(arr[i]);
            }
        }
        return result;
    }

    //法3：先sort(),然后比较相邻，没有上面那个问题
    //排序后比较相邻，如果一样则放弃，否则加入到result。
    function unique (arr) {
        arr.sort();
        var result=[arr[0]];
        for(var i = 1; i < arr.length; i++){
            if( arr[i] !== arr[i-1]) {
                result.push(arr[i]);
            }
        }
        return result;
    }
    ```
    **数组判断**
    ```js
    //自带的isArray方法
    var o = [];
    Array.isArray(o );//true
    //利用instanceof运算符
    o instanceof Array;//true
    //利用toString的返回值
    function isArray(o) {
      return Object.prototype.toString.call(o) === '[object Array]';
    }
    ```
    **数组求交集**
    ```js
    //利用filter和数组自带的indexOf方法
    array1.filter(function(n) {
      return array2.indexOf(n) != -1
    });
    ```
    **数组求并集**
    ```js
    //方法原理：连接两个数组并去重
    function arrayUnique(array) {
        var a = array.concat();
        for(var i=0; i<a.length; ++i) {
            for(var j=i+1; j<a.length; ++j) {
                if(a[i] === a[j])
                    a.splice(j--, 1);//删除的是j，然后++j继续比较
            }
        }

        return a;
    };
    ```
    **数组求差集**
    ```js
    //利用filter和indexOf方法
    Array.prototype.diff = function(a) {
      return this.filter(function(i) {return a.indexOf(i) < 0;});
    };
    ```
    **数组洗牌 shuffle**
    ```js
    //每次随机抽一个数并移动到新数组中，通过splice来去掉原数组已选项
    function shuffle(array) {
        var copy = [],
            n = array.length,
            i;
        // 如果还剩有元素。。
        while (n) {
            // 随机选取一个元素
            i = Math.floor(Math.random() * n--);
            // 移动到新数组中
            copy.push(array.splice(i, 1)[0]);
        }
        return copy;
    }

    //前面随机抽数依次跟末尾的数交换，后面依次前移，即：第一次前n个数随机抽一个跟第n个交换，第二次前n-1个数跟第n-1个交换，依次类推。
    function shuffle(array) {
        var m = array.length,
            t, i;
        // 如果还剩有元素…
        while (m) {
            // 随机选取一个元素…
            i = Math.floor(Math.random() * m--);
            // 与当前元素进行交换
            t = array[m];
            array[m] = array[i];
            array[i] = t;
        }
        return array;
    }
    ```
    参考:

      - [javascript常用数组算法总结](http://www.cnblogs.com/front-Thinking/p/4797440.html)

36. `JavaScript`的垃圾回收机制

    答案：1. 必要性

    由于字符串、对象和数组没有固定大小，所以当他们的大小已知时，才能对他们进行动态的存储分配。JavaScript程序每次创建字符串、数组或对象时，解释器都必须分配内存来存储那个实体。只要像这样动态地分配了内存，最终都要释放这些内存以便他们能够被再用，否则，JavaScript的解释器将会消耗完系统中所有可用的内存，造成系统崩溃。
    ```js
    var a = "before";
    var b = "override a";
    var a = b; //重写a
    ```
    这段代码运行之后，“before”这个字符串失去了引用（之前是被a引用），系统检测到这个事实之后，就会释放该字符串的存储空间以便这些空间可以被再利用。

    **两种垃圾回收机制**

    1. 标记清除

    这是javascript中最常用的垃圾回收方式。当变量进入执行环境是，就标记这个变量为“进入环境”。从逻辑上讲，永远不能释放进入环境的变量所占用的内存，因为只要执行流进入相应的环境，就可能会用到他们。当变量离开环境时，则将其标记为“离开环境”。
    　　垃圾收集器在运行的时候会给存储在内存中的所有变量都加上标记。然后，它会去掉环境中的变量以及被环境中的变量引用的标记。而在此之后再被加上标记的变量将被视为准备删除的变量，原因是环境中的变量已经无法访问到这些变量了。最后。垃圾收集器完成内存清除工作，销毁那些带标记的值，并回收他们所占用的内存空间。

    2. 引用计数

    另一种不太常见的垃圾回收策略是引用计数。引用计数的含义是跟踪记录每个值被引用的次数。当声明了一个变量并将一个引用类型赋值给该变量时，则这个值的引用次数就是1。相反，如果包含对这个值引用的变量又取得了另外一个值，则这个值的引用次数就减1。当这个引用次数变成0时，则说明没有办法再访问这个值了，因而就可以将其所占的内存空间给收回来。这样，垃圾收集器下次再运行时，它就会释放那些引用次数为0的值所占的内存。存在循环引用，使得垃圾无法回收，导致内存泄漏的问题。(设为null)

    参考:

      - [JavaScript垃圾回收机制](http://www.cnblogs.com/hustskyking/archive/2013/04/27/garbage-collection.html)

37. 常见的`JavaScript`设计模式

    答案：设计模式（Design pattern）:
    >是一套被反复使用，思想成熟，经过分类和无数实战设计经验的总结。使用设计模式是为了让系统代码可重用，可扩展，可解耦，更容易被人理解且能保证代码可靠性。设计模式使代码开发真正工程化；设计模式是软件工程的基石脉络，如同大厦的结构一样。只有夯实地基搭好结构，才能盖出坚壮的大楼。也是我们迈向高级开发人员必经的一步。

    **观察者模式**，**发布订阅者模式**，单例模式，装饰者模式，工厂模式


    单例模式：
    >单例就是保证一个类只有一个实例，实现的方法一般是先判断实例存在与否，如果存在则直接返回，如果不存在就创建了再返回，这就确保了一个类只有一个实例对象。在JavaScript中，单例作为一个命名空间提供者，从全局命名空间里提供一个唯一的访问点来访问该对象。(比如说一座房子只有一扇门，有一扇门就不用再开门了，如果没有门则需要创建一个门。每个门都只属于一个户主（命名空间），门是户与户之间的唯一接口，你要来我家，只能从这个门进来。)

    模式作用：
    1. 模块间通信；
    2. 系统中某个类的对象只能存在一个；
    3. 保护自己的属性和方法(不受外面干扰)；
    单例模式代码实战和总结
    ```js
    var single = (function() {
        var unique;
        function getInstance() {
              if (unique === undefined) {
                  unique = new Construct();
              }
              return unique;
          }
          function Construct() {
              // ... 生成单例的构造函数的代码
          }
          return {
              getInstance: getInstance
          }
      })();
    ```
    工厂（Factory）模式:
    >工厂模式定义了一个用于创建对象的接口，这个接口决定了实例化哪一个类。该模式将其成员对象的实例化推迟到子类中进行。而子类可以重写接口方法以便创建的时候指定自己的对象类型（抽象工厂）。（简单工厂：能找到具体细节）；抽象工厂只留口，不做事，留给外界覆盖；
    这个模式十分有用，尤其是创建对象的流程赋值的时候，比如依赖于很多设置文件等。并且，会经常在程序里看到工厂方法，用于让子类定义需要创建的对象类型。
    **简单工厂模式**：使用一个类（通常为单体）来生成实例。
    **复杂工厂模式**：使用子类来决定一个成员变量应该是哪个具体的类的实例。

    模式作用：
    1. 对象的构建十分复杂；
    2. 需要依赖具体的环境创建不同实例；
    3. 处理大量具有相同属性的小对象；
    4. 动态实现 例如自行车的例子，创建一些用不同方式实现统一接口的对象，那么可以使用一个工厂方法或者简单工厂对象来简化实现过程。选择可以是明确进行的也可以是隐含的。

    **工厂模式之利** 主要好处就是可以消除对象间的耦合，通过使用工程方法而不是new关键字。将所有实例化的代码集中在一个位子防止代码重复。

    **工厂模式之弊** 大多数类最好使用new关键字和构造函数，可以让代码更加简单易读。而不必去查看工厂方法来知道。

    ```js
    //当需要添加新的类型的时候，不需要动 BicycleShop 只需修改工厂单体对象就可以。
    var BicycleFactory = {
        createBicycle : function( model ){
            var bicycle;
            switch( model ){
                case "The Speedster":
                    bicycle = new Speedster();
                    break;
                case "The Lowrider":
                    bicycle = new Lowrider();
                    break;
                case "The Cruiser":
                default:
                    bicycle = new Cruiser();
                    break;
            }
            return bycicle;
        }
    }
    //简单工厂模式
    var BicycleShop = function(){};

    BicycleShop.prototype = {
        sellBicycle : function( model ){
            var bicycle = BicycleFactory.createBicycle(model);     
            return bicycle;
        }
    }
    //抽象工厂模式：不是另外使用一个对象或者类来创建实例（自行车），而是使用一个子类。
    //给接口不做事
    BicycleShop.prototype={
        sellBicycle: function( model ){
            var bicycle = this.createBicycle( model );
            return bicycle;
        },
        createBicycle: function( model ){
            throw new Error( " Unsupported " );
        }
    }
    //不同厂商的shop实例拥有不同的生成几个型号自行车的方法。
    var AcmeBicycleShop = function(){};
    extend( AcmeBicycleShop , BicycleShop );
    AcmeBicycleShop.prototype.createBicycle = function( model ){
        var bicycle;
        switch( model ){
            case "The Speedster":
                bicycle = new AcmeSpeedster();
                break;
            case "The Lowrider":
                bicycle = new AcmeLowrider();
                break;
            case "The Cruiser":
            default:
                bicycle = new AcmeCruiser();
                break;
        }
        return bicycle;
    }

    var GeneralBicycleShop = function(){};
    extend( GeneralBicycleShop , BicycleShop );
    GeneralBicycleShop.prototype.createBicycle = function( model ){
       ...
    }
    ```
    参考:

      - [细谈JavaScript中的一些设计模式](https://segmentfault.com/a/1190000004568177)
      - [常用的Javascript设计模式](http://blog.jobbole.com/29454/)

38. 客户端如何与服务器时间同步？

    答案：客户端的时间不对，以服务端的时间为准，同步时间。比如秒杀的时候，页面显示的倒计时，要以服务器时间为准。思路：简而言之就是发送一个ajax请求，然后获取对应的HTTP Header中的time，由于时延等问题造成时间在JS客户端获取后当前时间已经不再是服务器此时的时间，然后用本地的时间减去获取的服务器的时间，这应该就是时间偏移量。再新建一个时间，加上此偏移量应该就是此时此刻服务器的时间。代码如下：
    ```js
    var offset = 0;
    function calcOffset() {
        var xmlhttp = new ActiveXObject("Msxml2.XMLHTTP");
        xmlhttp.open("GET", "http://stackoverflow.com/", false);
        xmlhttp.send();

        var dateStr = xmlhttp.getResponseHeader('Date');
        var serverTimeMillisGMT = Date.parse(new Date(Date.parse(dateStr)).toUTCString());
        var localMillisUTC = Date.parse(new Date().toUTCString());
        offset = serverTimeMillisGMT -  localMillisUTC;  
    }
    function getServerTime() {
        var date = new Date();
        date.setTime(date.getTime() + offset);
        return date;
    }
    ```
    或者是：
    ```js
    var start = (new Date()).getTime();

    var serverTime;//服务器时间

    $.ajax({
      url:"XXXX",
    success: function(data,statusText,res){
            var delay = (new Date()).getTime() - start;
            serverTime = new Date(res.getResponseHeader('Date')).getTime() + delay;
            console.log(new Date(serverTime));//标准时间
            console.log((new Date(serverTime)).toTimeString());//转换为时间字符串
            console.log(serverTime);//服务器时间毫秒数
        }
    })
    ```

39. 什么是`JavaScript`中的类数组对象(`arguments`对象)？

    答案：JavaScript中类数组和“数组”类似，不能直接使用数组方法，唤作类数组。一个类数组对象：

    * 具有：拥有length属性，其它属性（索引）为非负整数(对象中的索引会被当做字符串来处理，这里你可以当做是个非负整数串来理解)；
    * 不具有：诸如 push 、 forEach 以及 indexOf 等数组对象具有的方法

    javascript中常见的类数组有arguments对象和DOM方法的返回结果。比如 document.getElementsByTagName()。你可以通过以下方法确定函数参数的个数：arguments.length；也可以获取单个参数值，例如读取第一个参数：arguments[0]。如果这些对象想使用数组的方法，就必须要用某种方式“借用”。由于大部分的数组方法都是通用的，因此我们可以这样做。

    类数组判断
    ```js
    //选与《javascript权威指南》判断一个对象是否属于“类数组”
    function isArrayLike(o){
      if(o && // o is not null, undefined, etc.
        typeof o ==="object"&& // o is an object
        isFinite(o.length) &&// o.length is a finite number
        o.length >=0 &&// o.length is non-negative
        o.length ==Math.floor(o.length) &&// o.length is an integer
        o.length <4294967296// o.length < 2^32
      ){return true;}// Then o is array-like
      else{
        return false;// Otherwise it is not
      }
    }

    ```

    所谓的 **通用方法** 就是不强制要求函数的调用对象 this 必须为数组，仅需要其拥有 length 属性和数字索引下标即可。就可以像使用数组那样，使用类数组来用数组的方法。
    ```js
    var a = {'0':'a', '1':'b', '2':'c', length:3};  // An array-like object:a[1]与a["1"]是一样的，键存在强制转换
    Array.prototype.join.call(a, '+');  // => 'a+b+c'
    Array.prototype.slice.call(a, 0);   // => ['a','b','c']: true array copy
    Array.prototype.map.call(a, function(x) {
        return x.toUpperCase();
    })// => ['A','B','C']:
    ```

    将类数组对象转化为数组

    有时候处理类数组对象的最好方法是将其转化为数组。
    ```js
    Array.prototype.slice.call(arguments)
    ```
    然后就可以直接使用数组方法啦。
    ```js
    var a = {'0':1,'1':2,'2':3,length:3};
    var arr = Array.prototype.slice.call(a);//arr=[1,2,3]
    ```

40. 在浏览器地址栏按回车、F5、Ctrl+F5刷新网页的区别

    答案：浏览器地址栏按回车:
    * 首先判断，请求的URI在浏览器缓存中是否过期(cache-control/expires),如果没有过期，直接拿缓存中的资源。拦截请求，不向服务器发送。
    * 如果已经过期，向服务器发送请求，客户端发HTTP请求时，使用If-Modified-Since标签，把上次服务器告诉它的文件最后修改时间返回到服务器端了。URI(URI:同一资源标识符 > URL:统一资源定位符)，如果在这个时间之后，到现在为止，该资源都没有被修改，服务器返回状态码304 Not Modified，没有发送页面的内容，浏览器收到之后，从缓存读出内容，如果资源被修改过，服务器返回的HTTP状态码是200，并发送页面的全部内容。服务器返回的HTTP头标签中有Last-Modified，告诉客户端页面的最后修改时间。

    F5
    请求头中多了一行Cache-Control: max-age=0，意思是说，缓存时间为0，我不管浏览器缓存中的文件过期没有，都去服务器询问一下，相当于上次HTTP响应的Expires暂时失效。

    Ctrl+F5:强制刷新
    If-Modified-Since没有了，Cache-Control换成了no-cache。意思是，不要缓存中的文件了，强制刷新，直接到服务器上重新下载，于是服务器的响应处理与首次请求这个URI一样，返回200 OK和新的内容。这种刷新，使用的网络流量是最大的，也是最耗时的。这就像你虽然发现了一盒牛奶，但是把它扔掉了，直接去买一盒新的。

    **客户端存储与客户端缓存**
    通过`chrome://cache/`可以查看客户端缓存（浏览器文件缓存），文件缓存主要用来减少网络请求，提高页面加载速度的。包括你看的视频啊各种资源。除非手动永久删除，不然过期也还是在。

    客户端存储就是一种存储技术，用来存取用户上网过程中的一些信息数据，和减少网络请求，提高页面加载速度没关系，就是存取状态的，数据可以用JS操作。比如：cookie用来标识用户有没有登陆；用户没有发出去的微博，也可以说是页面的状态数据，存在localStorage中，下次打开微博还在。缓存数据(数据库) ！== 缓存

    参考：

      - [在浏览器地址栏按回车、F5、Ctrl+F5刷新网页的区别](http://blog.csdn.net/yui/article/details/6584401)

41. 如何判断两个对象是否相等？

    答案：JavaScript 在判断相等性的时候对基础类型和对象区别对待。如果是原生类型，比如说字符串和数字，那么 JavaScript 根据值来比较；如果是对象，那么要根据对象的引用来判断，也就是对象在内存中引用的地址。

    所以在 JavaScript 代码中判定对象是否相等之前，必须明确我们需要判断是需要严格的内存引用地址相等还是两个对象的内容相等。有时候，我们仅仅需要判断两个对象的内容是否相等。我们知道，引用类型无法直接使用 == 或=== 取得期待结果。
    ```js
    function isEquivalent(a, b) {
        // 获取对象属性的所有的键
        var aProps = Object.getOwnPropertyNames(a);
        var bProps = Object.getOwnPropertyNames(b);
        // 如果键的数量不同，那么两个对象内容也不同
        if (aProps.length != bProps.length) {
            return false;
        }
        for (var i = 0, len = aProps.length; i < len; i++) {
            var propName = aProps[i];

            // 如果对应的值不同，那么对象内容也不同
            if (a[propName] !== b[propName]) {
                return false;
            }
        }
        return true;
    }
    // Outputs: true
    console.log(isEquivalent(obj1, obj2));
    ```
    上面的代码我们可以看到，判断两个对象内容是否相同，我们需要遍历对象的所有属性，并依次判断每个键对应的值是否相同。但是这里的实现并不严谨，有很多情况还无法处理。比如说NaN和NaN默认是不相等的(`isNaN(a) && isNaN(b)`来处理)、一个对象属性的值是另外一个对象(因此需要一个迭代的compare函数进行比较)等。
    所以在我们使用的时候，可以直接使用 Lo-Dash 或者 Underscore 这样经过亿万产品检验的类库来做这件事，不用重新发明轮子。他们实现的 API 是一样的 isEqual(...)。
    ```js
    // Outputs: true
    console.log(_.isEqual(obj1, obj2));
    ```
    参考：

      - [判定 JavaScript 对象是否相等](http://codethoughts.info/javascript/2015/07/12/javascript-object-equality/)

42. 什么是`IIFE`(Immediately Invoked Function Expression，立即执行函数表达式)？有什么作用？解释下为什么接下来这段代码不是 IIFE(立即调用的函数表达式)：function foo(){ }();要做哪些改动使它变成 IIFE?

    答案：立即执行表达式，是JS中一种可以立即执行的函数，其本质就是个函数表达式。一对圆括号“()”是一种运算符，跟在函数名之后，表示调用该函数。有很多种写法，常见如下：
    ```js
    // 在匿名函数外面套一个()，然后再用()来调用（方式B）
    (function(){ console.log("test");})(); // test
    // 在方式A的外层套一个()（方式C）
    (function(){ console.log("test");}()); // test
    ```
    JavaScript解释器会在默认的情况下把遇到的function关键字当作是函数声明语句(statement)来进行解释的。解释器把()中的内容当作表达式（expression）而不是语句(statement)来执行。所以重点是JavaScript解释器以「函数表达式」而不是「函数声明」来处理匿名函数的立即执行就可以了.

    这里只是声明一个叫foo的function，直接用()执行这样是不成功的，想要变成IIFE就要把声明变成表达式，就可以立即执行了，可以这样(function foo(){})()或者(function foo(){}())，这就是用括号把定义强转成表达式，当然还有其他方法，关键就是声明不可以执行，表达式才可以执行。

    通常情况下，只对匿名函数使用这种“立即执行的函数表达式”。它的目的有:
    * 一是不必为函数命名，避免了污染全局变量；
    ```js
    // 写法一
    var tmp = newData;
    processData(tmp);
    storeData(tmp);

    // 写法二完全避免了污染全局变量
    (function (){
      var tmp = newData;
      processData(tmp);
      storeData(tmp);
    }());
    ```
    * 二是IIFE内部形成了一个单独的作用域，可以封装一些外部无法读取的私有变量。解决闭包冲突
    * 模拟单例
    ```js
    // 创建一个立即调用的匿名函数表达式
    // return一个变量，其中这个变量里包含你要暴露的东西
    // 返回的这个变量将赋值给counter，而不是外面声明的function自身

    var counter = (function () {
        var i = 0;

        return {
            get: function () {
                return i;
            },
            set: function (val) {
                i = val;
            },
            increment: function () {
                return ++i;
            }
        };
    } ());

    // counter是一个带有多个属性的对象，上面的代码对于属性的体现其实是方法

    counter.get(); // 0
    counter.set(3);
    counter.increment(); // 4
    counter.increment(); // 5

    counter.i; // undefined 因为i不是返回对象的属性
    i; // 引用错误: i 没有定义（因为i只存在于闭包）
    ```

    参考：

      - [JavaScript中的立即执行函数表达式](https://segmentfault.com/a/1190000000327820)

43. 变量的`null`，`undefined`和`undeclared`的区别是什么？如何检测它们？

    答案：undefined和null是基本数据类型中的两种。undeclared不是一种数据类型，也不是一个值，只是一种说法，叫未声明的意思。

    在JavaScript中，将一个变量赋值为undefined或null，老实说，几乎没区别。undefined和null在if语句中，都会被自动转为false，相等运算符甚至直接报告两者相等undefined==null。

    undefined和null的含义与用法都差不多，只有一些细微的差别。

    null是一个表示"无"的对象，转为数值时为0；undefined是一个表示"无"的原始值，转为数值时为NaN。

    null表示"没有对象"，即该处不应该有值。

    undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义。典型用法是：

    （1）变量被声明了，但没有赋值时，就等于undefined。

    （2) 调用函数时，一个并不存在的对象属性，该参数值等于undefined。

    （3）对象没有赋值的属性，该属性的值为undefined。

    （4）函数没有返回值时，默认返回undefined。

    检测：
    使用typeof：typeof null//"object";typeof undefined//"undefined"；typeof undeclared//"undefined"
    ```js
    var exp = undefined;
    if (typeof(exp) == "undefined")
    {
        alert("undefined");
    }
    var exp = null;
    if (!exp && typeof(exp)!="undefined" && exp!=0)
    {
        alert("is null");
    }　//null、undefined、0

    try{p//undeclared
    }catch(e){
      console.log(e)//p is not defined.
    }
    ```


44. 原生对象(native object)和宿主对象(host object)有什么区别？

    答案：![JS对象系统](./images/JS对象系统.png)

    1.native object
    >ECMA-262 把原生对象（native object）定义为“独立于宿主环境的 ECMAScript 实现提供的对象”。

    它是语言本身实现和提供的对象，和语言运行在哪个环境无关。也就是说，不管你的JS代码在哪里跑，你都可以new出 native object 并使用它。包括：Object、Array、Date、RegExp、Function、Error(Error、EvalError、RangeError、ReferenceError、SyntaxError、TypeError、URIError 等错误类型的对象)、包装类型(String Number Boolean)、内置对象(Global和Math这两个对象在脚本程序初始化时被创建，不必实例化这两个对象)。

    2.host object

    宿主对象就是执行JS脚本的宿主环境提供的对象。对于浏览器环境而言，我们显示一个页面需要HTML，所以浏览器实现了DOM对象 —— window.document；我们还需要浏览器本身给我们提供一些必要的东西，比如URL地址相关的location、设备屏幕相关的screen等，所以浏览器又为我们提供了BOM对象 —— window。这些对象，就是host object。(DOM对象即是window.document，而window.document就是DOM的根节点，从这点来讲，我们可以理解为BOM包含了DOM 。)

    参考：

    - [浅析JavaScript的对象系统](http://www.jianshu.com/p/d0930dc0f95d)

45. 浏览器多个标签间如何通信(阿里)？

    答案：（假设有个人访问了你的网站。他依次登录，打开第二个标签页并在那个标签页里选择了注销。希望第一个页面可以判断用户是否已注销，并对页面做相应的改变。）

    1. localStorage

    localStorage 会触发一个事件。不论某个标签页在何时添加、修改或删除了 localStorage，都会对其余的所有标签触发事件，所有其它的标签页都能通过 window 对象监听到这个事件。这就意味着我们只要为 localStorage 赋值，通过监听事件，控制它的值，就能够跨浏览器标签通信了。使用`localStorage.setItem(key,value);`添加内容，使用storage事件监听添加、修改、删除的动作。
    注意quirks：Safari 在无痕模式下设置localstorge值时会抛出 QuotaExceededError 的异常。
    ![localStorage_event对象属性](./images/localStorage_event对象属性.png)
    ```js
    window.addEventListener('storage', function (event) {
      console.log(event.key, event.newValue);
    });
    ```
    例子：用户打开了两个标签页，在其中一个里执行了注销操作后返回另一个时，页面将重新载入，（如果可以的话）服务器端逻辑将把用户重定向到其它位置。这个检查只在当前标签页获得焦点时执行，这是因为用户可能在注销后立刻重新登录，这种情况下不应将其余标签页的状态全部设为已注销。
    ```js
    var loggedOn;
    // call when logged-in user changes or logs out
    logonChanged();
    window.addEventListener('storage', updateLogon);
    window.addEventListener('focus', checkLogon);
    function getUsernameOrNull () {
      // return whether the user is logged on
    }
    function logonChanged () {
      var uname = getUsernameOrNull();
      loggedOn = uname;
      localStorage.setItem('logged-on', uname);
    }
    function updateLogon (event) {
      if (event.key === 'logged-on') {
        loggedOn = event.newValue;
      }
    }
    function checkLogon () {
      var uname = getUsernameOrNull();
      if (uname !== loggedOn) {
        location.reload();
      }
    }
    ```
    2. 使用cookie+setInterval

    来周期性地通过 setInterval 检查用户是否登录。但这个方案并不好，因为这样会把许多 CPU 周期耗费在检查一个可能自始至终都不会满足的条件上。服务器端事件或者 WebSockets 。
    ```js
    //获取Cookie天的内容  
    function getKey(key) {  
        return JSON.parse("{\""+ document.cookie.replace(/;\s+/gim,"\",\"").replace(/=/gim, "\":\"") +"\"}")[key];  
    }
     //每隔1秒获取Cookie的内容  
    setInterval(function(){  
      console.log(getKey("name"));  
     },1000);  
    ```

    总结：

    WebSocket、SharedWorker
    也可以调用localstorge、cookies等本地存储方式。

    参考：

    - [浏览器跨标签通讯](http://web.jobbole.com/82225/)

46. `attribute`和 `property`的区别是什么(核心)？

    答案：property（属性）是DOM对象自身就拥有的属性，而attribute（特性）是我们通过设置HTML标签而给之赋予的特性，attribute和property的同名属性/特性之间会产生一些特殊的数据联系，而这些联系会针对不同的属性/特性有不同的区别。
    ![property和attribute](property和attribute.png)
    ```js
    //有“id”和“value”两个基本的属性，但没有“sth”这个自定义的属性
    <input id="in_1" value="1" sth="whatever">
    console.log(in1.sth);             // property -->undefined
    console.log(in1.attributes.value);  // attributes
    //'sth="whatever" 注意，打印attribute属性不会直接得到对象的值，而是获取一个包含属性名和值的字符串.
    ```
    创建

    * DOM对象初始化时会在创建默认的基本property；
    * 只有在HTML标签中定义的attribute才会被保存在attributes属性中；
    * attribute会初始化property中的同名属性，但自定义的attribute不会出现在property中；
    * attribute的值都是字符串；

    数据绑定

    * attributes的数据会同步到property上，然而property的更改不会改变attribute；
    * 对于value，class这样的属性/特性，数据绑定的方向是单向的，attribute->property；
    * 对于id而言，数据绑定是双向的，attribute<=>property；
    * 对于disabled而言，property上的disabled为false时，attribute上的disabled必定会并存在，此时数据绑定可以认为是双向的；

    使用

    * 可以使用DOM的setAttribute方法来同时更改attribute；
    * 直接访问attributes上的值会得到一个Attr对象，而通过getAttribute方法访问则会直接得到attribute的值；
    * 大多数情况（除非有浏览器兼容性问题），jQuery.attr是通过setAttribute实现，而jQuery.prop则会直接访问DOM对象的property；

    参考：

    - [DOM 中 Property 和 Attribute 的区别](http://www.cnblogs.com/elcarim5efil/p/4698980.html)

47. `document load event` 和 `document DOMContentLoaded event`之间的区别是什么？

    答案：他们的区别是，触发的时机不一样，先触发DOMContentLoaded事件，后触发load事件。load事件：页面资源全部载入（JS，CSS，图片等全部加载完）触发.DOMContentLoaded事件:当DOM加载完成触发，此时引用的资源未必已加载完成.

    DOM文档加载的步骤为

    1. 解析HTML结构。
    2. 加载外部脚本、样式表文件。(放最后，或者设置 async、defer)
    3. 解析并执行脚本代码。
    4. DOM树构建完成。//DOMContentLoaded
    5. 加载图片等外部文件。
    6. 页面加载完毕。//load
    在第4步，会触发DOMContentLoaded事件。在第6步，触发load事件。
    ```js
    //用原生js可以这么写
    // 不兼容老的浏览器，兼容写法见[jQuery中ready与load事件](http://www.imooc.com/code/3253)，或用jQuery
    document.addEventListener("DOMContentLoaded", function() {
       // ...代码...
    }, false);

    window.addEventListener("load", function() {
        // ...代码...
    }, false);

    //用jQuery这么写
    // DOMContentLoaded
    $(document).ready(function() {
        // ...代码...
    });
    //load
    $(document).load(function() {
        // ...代码...
    });
    ```
    参考：

    - [DOMContentLoaded](https://developer.mozilla.org/zh-CN/docs/Web/Events/DOMContentLoaded)
    - [事件DOMContentLoaded和load的区别](http://www.jianshu.com/p/d851db5f2f30)

48. `== `与` ===`的区别。

    答案：前者隐式类型转换，后者严格对比。简单来说就是使用“==”时，如果两边类型不同，js引擎会把它们转换成相同类型然后在进行比较，而“===”则不会进行类型转换，因此当两边不是属于同一个类型，肯定不相等。

    1、对于string,number等基础类型，==和===是有区别的

        1）不同类型间比较，==之比较“转化成同一类型后的值”看“值”是否相等，===如果类型不同，其结果就是不等
        2）同类型比较，直接进行“值”比较，两者结果一样

    2、对于Array,Object等高级类型，==和===是没有区别的

        进行“指针地址”比较
    3、基础类型与高级类型，==和===是有区别的
        1）对于==，将高级转化为基础类型，进行“值”比较
        2）因为类型不同，===结果为false

    === 判断规则

    * 如果类型不同，就[不相等]
    * 如果两个都是数值，并且是同一个值，那么[相等]；(！例外)的是，如果其中至少一个是NaN，那么[不相等]。（判断一个值是否是NaN，只能用isNaN()来判断）
    * 如果两个都是字符串，每个位置的字符都一样，那么[相等]；否则[不相等]。
    * 如果两个值都是true，或者都是false，那么[相等]。
    * 如果两个值都引用同一个对象或函数，那么[相等]；否则[不相等]。
    * 如果两个值都是null，或者都是undefined，那么[相等]。
    == 判断规则：

    * 如果两个值类型相同，进行 === 比较。
    * 如果两个值类型不同，他们可能相等。根据下面规则进行类型转换再比较：
    * 如果一个是null、一个是undefined，那么[相等]。
    * 如果一个是字符串，一个是数值，把字符串转换成数值再进行比较。
    * 如果任一值是 true，把它转换成 1 再比较；如果任一值是 false，把它转换成 0 再比较。
    * 如果一个是对象，另一个是数值或字符串，把对象转换成基础类型的值再比较。对象转换成基础类型，利用它的toString或者valueOf方法。js核心内置类，会尝试valueOf先于toString；例外的是Date，Date利用的是toString转换。

    参考：

      - [ js ==与===区别](http://blog.csdn.net/wxdzxl/article/details/8502119)


49. 解释`JavaScript`异步实现的原理。
50. `escape()`, `decodeURIComponent()`, `decodeURI()`之间的区别是什么？
51. NodeJS如何利用CPU的多核，增强性能？
52. `ExpressJS `中间件原理。
53. script的三种加载方式。

    http://natumsol.github.io/2015/11/11/loading-script/
    
54. js中switch语句中，用来判断的表达式可以是任意类型，而不仅限与整型。js中函数的参数传递方式都是按值传递，没有按引用传递的参数。但js中有保存引用的对象，比如数组。
变量作用域：值一个变量在程序中的那些地方可以被访问。js中的变量作用域被定义为函数作用域。这是值变量的值在定义该变量的函数内是可见的，并且定义在该函数内的嵌套函数中也可以访问该变量。在主程序中，如果在函数外定义一个变量，那么这个变量拥有全局作用域，这是值可以在包括函数体内的程序的任何部分访问该变量。递归并不是异步回调，还是同步执行。
typeof和instanceof                                                                                  
