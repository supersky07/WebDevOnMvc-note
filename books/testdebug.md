# 测试和调试

========

## 单元测试

单元测试是更低层次的测试，确保特定的后台代码片段能正常运行。单元测试更多的是为了发现浏览器兼容性bug。

可将很多单元测试集合起来就可以做到连续的集成测试了——每次代码有更新时都重新执行一遍所有的单元测试。

### 断言

断言是测试的核心，它们决定了哪些测试会通过，哪些测试会失效。断言是一些表述代码期望执行结果的语句。

一个简单的断言函数：
	
	var asset = function (val, msg) {
		if (!val) {
			throw(msg || val + " does not equal true");
		}
	}

### QUnit

曾经最流行的测试类库，最初用来测试jQuery。

### Jasmine

同QUnit不同，Jasmine定义了用以描述应用中特定对象的额行为的额测试片段。实际上这些片段和单元测试非常类似，只不过换了一种表述方式。

Jasmine的优势是不依赖任何第三方类库，甚至不依赖DOM，也就是说它可以在任何能执行javascript的地方运行。

## 驱动

整合不同的浏览器，可以自动运行Javascript测试代码。

Watir是一个基于Ruby的驱动类库，整合了Chrome，Firefox，Safari和IE。

Selenium浏览器驱动工具提供了一种域脚本语言（DSL），用这种语言可以为多种编程语言编写测试代码。

## 无界面的测试

Zombie是一个无界面的Javascript类库，专门为Node.js设计。它提供了一个Browser对象，它和真实的web浏览器中的BOM非常类似。

Ichabod是另一个用于无界面测试的类库。除了能模拟DOM和解析引擎外，它的优势还在于它使用了Webkit解析引擎。但它的缺点是只能运行在OS X中。

## 分布式测试

采用TestSwarm类似的工具，或者业内成熟的公司提供的服务进行。

## 调试工具

### Web Inspector
	
	- Elements : 监控HTML元素，编辑样式
	- Resources : 页面的源文件和所需的资源
	- Network : HTTP请求
	- Scripts : Javascript文件和调试器
	- Timeline : 浏览器渲染过车个的详细预览
	- Audits : 代码和内存统计
	- Console : 执行Javascript，并可查看结果

### Firebug

	- Console : 执行Javascript，并查看结果
	- HTML : 查看DOM元素，编辑样式
	- CSS : 查看并编辑页面的CSS
	- Script : Javascript文件和调试器
	- DOM : 查看全局变量
	- Net : HTTP请求

## Profile和函数运行时间

可以利用Profile在调试器中检查程序运行效率和时间。

只需要在想统计的代码段两端加上console.profile()和console.profileEnd()即可：
	
	console.profile();
	//要统计的代码
	console.profileEnd();

此外，也可以使用调试器Profile的record特性，它的功能和直接嵌入Console语句是一样的。

使用Profile的snapshot功能可生成页面当前的堆的快照。可以显示出当前使用了多少对象，占用了多少内存。这是查找内存泄漏的好方法。
