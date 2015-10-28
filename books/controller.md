# 控制器和状态

========

## 模块模式
模块模式是用来封装逻辑并避免全局命名污染的好方法。可以使用匿名函数做到这一点。

### 全局导入
将全局对象作为参数传入匿名函数，可以将他们导入我们的代码中，这种实现方法比隐式的全局对象更加简洁而且速度更快：
	
	(function ($) {
		/***/
	})(jQuery);

### 全局导出
使用类似的技术实现全局导出，即将`window`对象传递进去：
	
	(function ($, exports) {
		exports.foo = 'bar';
	})(jQuery, window);

## 添加少量上下文
模块中的上下文都是全局的：
	
	(function () {
		assert(this, window);
	})();

如果想在模块中自定义上下文，则需要将函数添加至一个对象中：
	
	(function () {
		var mod = {};
		
		mod.test = function () {
			assert(this, mod);
		};
		
		mode.test();
	})();
	
## 状态机
使用状态机可以轻松的管理很多控制器，根据需要显示和隐藏视图。

什么是状态机？本质上，状态机由两部分组成：状态和转换器。它只有一个活动状态，但也包含很多非活动状态。当活动状态之间相互切换时就会调用状态转换器。

## 路由选择
应用若以单个页面的形式运行，也就是URL不会改变。但用户却习惯根据不同的URL来获取不同的资源，同时还会用到前进、后退的功能。

所以我们需要把页面的状态反应到URL上，建立状态与URL的对应关系。当应用的状态发声改变时，URL也随之改变，反之亦然。

### 使用URL中的hash
改变URL却又不会重新请求的方法是改变它的hash。
	
	http://weibo.com/#!/home
	
通过location对象来读取或修改页面hash：
	
	window.location.hash = "home"

太过频繁的设置hash也会影响性能，特别是在移动终端里的Webview中。

### 检测hash的变化
以往采用的方法是启用一个定时器来监控。现代浏览器基本都支持`hashchange`事件，这是一个`window`的事件，如果想检测hash的变化，只需监听该事件即可。
	
	window.addEventListener("hashchange", function () {});

### 搜索引擎抓取Ajax
很多搜索引擎爬虫无法运行Javascript，所以无法得到动态创建的内容，当然页面的hash路由也不会起作用。

最笨的方法是将Javascript动态创建的内容再重新拼一个静态html页面，这样就可以抓到了。

Google对它的搜索引擎做了改进，提出了“Ajax抓取规则”。

对于
	
	http://weibo.com/#!/home
	
Google的爬虫程序看到感叹号就知道当前一诶按遵从“Ajax抓取规则”，然后它会把这个URL解析成另外一个模样：
	
	http://weibo.com/?_escaped_fragment_=/home

这样就可以把参数带到服务器去获取真正的内容了。

### 使用HTML5 History API
History API是HTML5规范的组成部分，利用它可以实现将当前地址替换为任意URL。也可以控制是否将新的URL添加至浏览器的历史记录中，从而根据需要来控制浏览器的“后退”按钮。和设置地址的hash类似，页面也不会重新加载，页面的状态会一直保留下来。

	var dataObject = {
		createdAt: "2015-10-10",
		author: "supersky07"
	};
	
	var url = "/test/haha";
	history.pushState(dataObject, document.title, url);
	
	history.replaceState();
	histroy.popState();