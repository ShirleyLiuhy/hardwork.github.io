---
title: 基于web 2.0爬虫技术
---
## web1.0 vs web2.0

### web 1.0

1.网站生成固定的内容

2.静态页面

3.桌面浏览器

4.简单 同步

### web 2.0

1.用户生成内容

2.Mashup和web服务

3.复杂的客户软件

4.复杂 异步



## web2.0动态爬虫：基于无界面浏览器

### 动态爬虫

很多网页使用了js和ajax的技术，利用js或者ajax渲染的页面，基于web1.0的爬虫并不能很好地呈现出来。

爬虫架构：

`指定的url --> 经过下载器-->分析页面-->去重(url)-->放入url队列-->放入调度器-->再回到下载器`循环步骤



### 先说webkit

webkit的主要包括 `WebCore` (负责html的解析和渲染)   `JavaScriptCore`   (负责JavaScript的解析)  `Ports`

相关学习资料，

https://security.tencent.com/index.php/blog/msg/12

https://security.tencent.com/index.php/blog/msg/30

https://security.tencent.com/index.php/blog/msg/34

挖个坑，对webkit完全不懂，以后补充



### 了解最新的headless chrome 无界面浏览器

https://www.v2ex.com/t/380014

https://www.cnblogs.com/TianFang/p/6979543.html

https://baijiahao.baidu.com/s?id=1604301796644784216&wfr=spider&for=pc



## 爬虫基础

### BOM

browser object model 浏览对象模型

Bom提供独立于内容，而与浏览器窗口进行交互的对象

主要用于管理窗口与窗口之间的通讯，因此其核心对象为window

BOM可以:移动和调整浏览器大小的window对象，用于导航的history和location对象，获取浏览器，操作系统与用户屏幕信息的navigator与screen对象

### DOM

Doucment Object Model

### CDP

Chrome Devtools Protocol

#### 获取页面信息

Devtools是Chrome的开发者工具(F12可调出)，它是通过`远程调试协议`和浏览器内核进行交互，集成在浏览器中

`远程调试协议`是基于WebSocket，利用WebSocket建立连接DevTools和浏览器内核的快速数据通道

1.开启server服务

打开浏览器的远程调试支持，并且指定端口号9222

`./chrome --remote-debugging-port=9222`

remote-debugging 远程调试模式

###### #启动headless

![pic1](E:\WebSecurity\基于web2.0\pic1.png)

2.获取server地址

`http://localhost:9222/json`

![3](E:\WebSecurity\基于web2.0\3.png)

会获取到webSocketDebuggerUrl的地址

那么获取到的该地址(即相当于打开Devtools的地址-->页面管理api的地址)

`"webSocketDebuggerUrl": "ws://127.0.0.1:9222/devtools/page/6d4f925f-7220-47cd-a4f9-800686445ffb" `

#### 用户与网络的交互

Chrome远程调试模式是遵循Chrome DevTools Protocol的 

##### 消息格式

Chrome DevTools Protocol的协议消息格式比较简单，是文本形式的，用json表示对象，它主要分为如下三种：请求、响应和通知。

###### 1.请求

它内容主要包括id，method， params三个部分：

`id`是自己建立的命令编号，

`method`是发送的请求，

`params`则是请求参数。

###### 2.响应

响应内容包括两个部分：id和result，

`id`为请求命令编号，

`result`为请求结果。

###### 3.通知

通知为chrome主动通知给客户端的消息。通知主要包括method和params两个部分。　

##### 常用指令

###### Page.navigate

跳转到指定页面：Page.navigate

###### Runtime.evaluate

执行JS函数：Runtime.evaluate，相当于在Console控制台执行指令

可以执行任何js指令，模拟输入，输出渲染后的html

###### Page.getResourceTree & Page.getResourceContent

获取资源树：Page.getResourceTree
获取资源：Page.getResourceContent

用于获取原始请求数据，用于构建Develop Tools的Source树



## 爬虫处理流程

(挖坑)



## Headless Chrome

### puppeteer

#### 安装puppeteer

puppeteer是一款基于chrome的自动化测试以及爬虫工具，通过DevTools协议控制headless chrome的Node库，通过puppeteer库可以控制chrome模拟大部分用户操作来进行爬虫。

在下载puppeteer之前，我是自行安装了 headless chrome的，百度有多种方法可供安装。

puppeteer库下载的过程中我遇到了很多的坑，网上也不是所有的方法在我的centos7上都适用，

最后我参照

https://blog.csdn.net/jonatha_n/article/details/75271050 升级node版本(puppeteer要求node版本6.30以上)

最后我是用了cnpm将puppeteer安装好的，可能大佬们有更好的方法，百度上还是挺多的。

`npm install -g cnpm --registry=https://registry.npm.taobao.org`

`cnpm i puppeteer`



#### page对象

##### 客户端模拟

page.setViewport: 设置视图大小

page.setUserAget: 设置UserAgent

page.SetCookie： 设置Cookie

##### 页面跳转

page.goto(url, options)

page.goBack(options)

page.goForward(options)

page.reload(options)

##### 选择

page.${selector}

page.${expression}

##### 模拟输入

page.mouse

page.keyboard

page.click(selector[, options]) 在被选择元素上模拟点击

page.type(selector, text[, options])  在被选择的输入框中输入

page.hover(selector)  模拟鼠标移动到被选择元素上

page.select(selector, ...values)  在被选择元素上模拟选择select选项

page.tap(selector)  在被选择元素上模拟触摸

##### 等待

page.waitForNavigation(options)

page.waitFor(selectorOrFunctionOrTimeout[, options[, ...args]])

page.waitForSelector(selector[, options])

page.waitForXPath(xpath[, options])

page.waitForFunction(pageFunction[, options[, ...args]])

##### 执行脚本

page.evaluate（一般在控制台执行指令）

page.evaluateHandle(pageFunction, ...args)

page.evaluateOnNewDocument(pageFunction, ...args)

page.$$eval(selector, pageFunction[, ...args])

page.$eval(selector, pageFunction[, ...args])

##### 信息查看

page.url()

page.content()

page.frames()

page.mainFrame()

page.metrics()

page.target()

page.title()

page.viewport()

##### 请求中断

page.setRequestInterception 

其中interceptionRequest三种响应模式 abort continue respond

##### 内容注入

page.addScriptTag(options)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

page.addStyleTag(options)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

##### 事件

page.on(....

|      close       | frameattached  |    pageeror     |
| :--------------: | :------------: | :-------------: |
|     console      | framedetached  |     request     |
|      dialog      | framenavigated |  requestfailed  |
| domcontentloaded |      load      | requestfinished |
|      error       |    metrics     |    response     |

##### 内容保存

page.pdf(options)

page.screenshot([options])



##### 其他

[page.authenticate(credentials)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.bringToFront()](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.browser()](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.close(options)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.coverage](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.exposeFunction(name, puppeteerFunction)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.queryObjects(prototypeHandle)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.setBypassCSP(enabled)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.setCacheEnabled(enabled)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.setContent(html)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.setDefaultNavigationTimeout(timeout)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.setJavaScriptEnabled(enabled)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.setOfflineMode(enabled)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page.tracing](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)

[page对象]转载于https://www.cnblogs.com/TianFang/p/9059978.html 

#### 基本命令使用

`const puppeteer = require('puppeteer')`

`puppeteer.launch()`启动一个chrome实例

`browser.newPage() `创建一个新的页面

`page.goto `进入指定网页

`page.screenshot`截图

```javascript
//引入puppeteer库
const puppeteer = require('puppeteer');

#主函数() //也可以命名--> async function abc() [主函数为abc]
#async函数使用了async/await特性，是异步的
#async函数会返回一个promise，如果返回了值就reslove，如果无法返回值就reject
(async() => {
    //puppeteer.launch()启动一个chrome实例
    #此处使用await用于暂停函数的执行，直到返回一个promise(也就是此处函数会被暂停直到promise解析完成-->成功创建实例/抛出错误)
 const browser = await puppeteer.launch({
     //使用headless chrome ，带参数
     //chrome --no-sandbox --disable-setuid-sanbox
    headless: true,
    args: ['--no-sandbox', '--disable-setuid-sandbox'],
});
    //输出browser的version
    #console用于监控浏览器的console事件
 console.log(await browser.version());
    //将浏览器关闭
 browser.close();
})
();
```

![4](E:\WebSecurity\基于web2.0\4.png)





```javascript
const puppeteer = require('puppeteer');
(async() => {
const browser = await puppeteer.launch({
    headless: true,
    args: ['--no-sandbox', '--disable-setuid-sandbox'],
});
    //创建一个新的页面
const page = await browser.newPage();
    //前往www.baidu.com
await page.goto('https://www.baidu.com/', {waitUntil: 'networkidle2'});
    //将该page打印成pdf，以A4的形式
await page.pdf({path: 'screen.pdf', format: 'A4'});
browser.close();
})();
```

其中上述代码中的`{waitUntil: 'networkidle2'}`涉及到爬虫的工作过程。后面会讲到。

#### 爬虫事件管理流程

以下内容参考了2018先知白帽大会猪猪侠基于web2.0的爬虫实践pdf

该图取自2018先知白帽大会猪猪侠基于web2.0的爬虫实践pdf中

![5](E:\WebSecurity\基于web2.0\5.png)



##### 页面加载

也就是何时代码注入

1.页面内容加载完成 `page load`

page.once('load', () => {});

2.等待页面加载完成`networkidle2`

等待网络状态空闲时才继续执行

3.DOM树解析完成 `DOMContentLoaded` 

初始DOM，加载并解析完成 

document.readystatechange	

document.readyState	===	"complete"



##### 请求拦截

解决页面被意外跳转或者关闭

`window.open();`	

`window.location="/123";`



##### 函数劫持

###### 弹框 新页面打开 超时阻塞等待

(1 dialog --> alert() confirm() prompt()

用于监控页面的JavaScript dialog事件。比如 alert, prompt, confirm or beforeunload。 

`page.on('dialog'，dialog=>{dialog.accept()});`



(2 framenavigated 

监控frame的attach/navigated/detached事件 

`page.on('framenavigated',frameTo=>{console.log(frameTo.url())})`



(3 window.close window.setTimeout window.setInterval



###### 捕获AJAX的请求信息

(1 启动请求拦截过滤处理

```javascript
await page.setRequestInterception(true);
page.on('request',request => {
    console.log(request.url())
});
```

(2 劫持原生类XMLHttpRequest



##### 事件监听

###### prototype对象

JavaScript的每个对象都继承另一个对象，后者称为“原型”（prototype）对象。只有`null`除外，它没有自己的原型对象。原型对象上的所有属性和方法，都能被派生对象共享。

![6](E:\WebSecurity\基于web2.0\6.png)

###### 绑定的事件均需要调用addEventListener

绑定事件方法1：

```html
<div id="test1" onclick="event()"></div>

<script type=text/javascript>
    var test1=document.getElementById("test1")
    //行内绑定
    function event(){
        test1.innerHTML="..."
    }
```

方法2、3：

```html
<div id="test2"></div>
<div id="test3"></div>
<div id="test4"></div>

<script type=text/javascript>
    //方法2：节点绑定事件
    test2.onclick = function(){
        //code
    }
    //方法3：2级DOM事件绑定
    test3.addEventListener("click",function(){
        //code
    },false);
```



###### 获取新增绑定事件变更信息

```html
<button id="y">TEST</button>
<script type=text/javascript>
    y.addEventListener('click',function(element){
        console.log(element)
    },false)
```

```javascript
_addEventListener=Element.prototype.addEventListener;
Element.prototype.addEventListener=function(){
    console.log(argument,this)
    //apply()方法 接收两个参数，一个是函数运行的作用域（this），另一个是参数数组
    _addEventListener.apply(this,arguments)
}
```

###### 获取事件被触发后的节点属性变更信息

DOM4新增MutationObserver方法，用于监听DOM树结构变化的事件 

[MutationObserver]: https://www.jianshu.com/p/b5c9e4c7b1e1

```javascript
var	observer=new WebKitMutationObserver(function(mutations){		
    console.log('eventLoop	nodesMutated:',	mutations.length);			mutations.forEach(function	(mutation)	{
        //观察目标节点的子节点的新增和删除。
        if	(mutation.type	===	'childList'){
            for	(let i = 0;	i <	mutation.addedNodes.length;	i++){
                let	addedNode = mutation.addedNodes[i];	
                console.log('Node added:', addedNode.nodeType, mutation.addedNodes[i]);							}
            //观察目标节点的属性节点
        }else if (mutation.type	===	'attributes'){
            let	element	= mutation.target;
            var	element_val	= element.getAttribute(mutation.attributeName)
            console.log(mutation.attributeName,	'->', element_val)
        }			
    });	
});	
observer.observe(window.document.documentElement,{
    childList:	true,			
    attributes:	true,
    characterData:	false,
    subtree:	true,			
    characterDataOldValue:	false,			
    attributeFilter:	['src',	'href'],	
});

```

###### 遍历节点以及事件

页面加载和渲染完成。

`document.all`

```javascript
nodes = document.all;
for(j=0;j<nodes.length;j++){
	//获取节点属性的集合
    attrs=nodes[j].attributes;
    for(k=0;k<attrs.length;k++){
        if(attrs[k].nodeName.startsWith('on')){
            console.log(attrs[k].nodeName,attrs[k].nodeValue);
        }
    }
}
```



`document.createTreeWalker`

```javascript
var	treeWalker = document.createTreeWalker(
    document.body,			
    NodeFilter.SHOW_ELEMENT,
    { acceptNode:function(node)	{ return NodeFilter.FILTER_ACCEPT;}},
    false
);	
while(treeWalker.nextNode()){
    var	element = treeWalker.currentNode;
    for(k=0; k<element.attributes.length; k++)	{
        attr = element.attributes[k]
        if	(attr.nodeName.startsWith('on')) {
            console.log(attr.nodeName,	attr.nodeValue);
        }			
    }	
};

```

###### 触发节点上已绑定的事件信息

![7](E:\WebSecurity\基于web2.0\7.png)

`dispatchEvent()`

`eval()`

获取事件类型 -> 选择节点对象 -> 触发事件

```javascript
//	<button	id="elem"	onclick="alert('Click!');">Click</button>
let	event = new	Event("click");	elem.dispatchEvent(event);
for(var	i=0;i<elem.attributes.length;i++){
    var	element	= elem.attributes[i]
    if	(element.nodeName.startsWith('on'))	{
        console.log(element.nodeName);
        eval(element.nodeValue);
    }
}
```

#### 数据爬取

https://segmentfault.com/p/1210000011804279/read 贴上学习的标签。。