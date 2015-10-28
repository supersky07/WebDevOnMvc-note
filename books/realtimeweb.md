# 实时Web

========

随着互联网技术的快速发展，用户的要求也越来越高，实时性就变得越发重要。将来一定回事最热门的话题之一。

## WebSocket

WebSocket是HTML规范的一部分，提供了基于TCP的双向的、全双工的socket连接。

一些浏览器实现了WebSocket，但对于不支持的浏览器，还可以降级使用笨方法来实现，比如Comet或轮询。

Comet相对WebSocket最大的问题就是基于HTTP，而HTTP的体积太大，每个请求都带有完成的HTTP头信息。而且包含很多没有用的TCP握手，因为HTTP是比TCP更高层次的网络协议。

使用WebSocket时，一旦服务器和客户端之间完成握手，信息就可以畅通无阻的随意往来于两端，而不用附加那些无用的HTTP头信息。极大的降低了带宽的占用，提高了性能。

	var socket = new WebSocket("ws://example.com");

	socket.onopen = function () {};
	socket.onmessage = function (data) {
		console.log("New data - " + data);
	};
	socket.onclose = function () {
		socket.send("Why, hello there!");
	};

发送和接收的消息只支持字符串格式，但在字符串和JSON数据之间可以很轻松的相互转换，这样就可以创建自己的协议：
	
	var rpc = {
		test: function () {}
	};

	socket.onmessage = function (data) {
		var msg = JSON.parse(data);

		rpc[msg.method].apply(rpc, msg.args);
	};

此时服务器可以发送带有method的JSON数据来调用客户端的函数：
	
	{"method": "test", "args": [1, 2]}

为了更好更成功的使用WebSocket，这里给出一些建议：

	- 使用安全的WebSocket连接（wss）。
	- 在WebSocket服务器前面使用TCP负载均衡器，而不要使用HTTP负载均衡器，除非某个HTTP负载均衡器大肆宣扬自己支持WebSocket。
	- 不要假设浏览器支持WebSocket，需要判断并适当降级处理

## Node.js 和 Socket.IO

Socket.IO是一个Node.js库，实现了WebSocket。Socket.IO的目标是在每个浏览器和移动设备中构建实时app，这缩小了多种传输机制之间的差异。

若环境支持WebSocket，那么Socket.IO就会尝试使用WebSocket，若有必要也会降级使用其他的传输方式。这里列出了所支持的传输方式，基本支持所有浏览器。

	- WebSocket
	- Adobe Flash Socket
	- ActiveX HTMLFile (IE)
	- 基于multipart编码发送XHR
	- 基于长轮询的XHR
	- JSONP轮询

具体写法如下：
	
	var socket = new io.Socket();

	socket.on("connect", function () {
		socket.send("hi!");
	});

	socket.on("message", function (data) {
		alert(data);
	});

	socket.on("disconnect", function () {});

## 感知速度

- Amazon
	- 页面节哀在时间每增加100ms，就会造成1%的销售损失
- Google
	- 页面加载时间每增加500ms，就会造成20%的流量损失
- Yahoo
	- 页面加载时间每增加400ms，在页面加载完成之前就单机“后退”按钮的人会增加5%～9%

感知速度和真实的速度同等重要，因为感知速度关系到用户感官体验。

Web应用中最耗时的部分是新数据的加载，最明智的做法是在用户请求数据之前预测用户的行为并预加载数据。

当用户和你的应用交互时，需要适时给用户一些反馈，通常使用一些可视化的进度指示来给出反馈。用行业术语来讲就是“期望管理”——要让用户知道当前项目的状态和估计完成时间。“期望管理”同样适用于用户体验领域，适时给用户一些反馈，告知用户发声了什么事情，会让用户更有耐心等待程序的运行。
