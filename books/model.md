# 模型和数据

========

## MVC和命名空间
MVC保证各层分离，数据管理归入模型层。与数据操作和行为相关的逻辑都应当放入模型中，并且通过命名空间进行管理。
比如：
	
	var User = function (attrs) {
		this.attributes = attrs || {};
	};
	
	User.prototype.destory = function () {};
	
	//与user不直接相关的函数和变量，直接赋值给User对象
	User.fetchRemote = function () {};

## 构建对象关系映射（ORM）
对象关系映射（Object-relational mapper，简称ORM）是在其他语言中常见的一种数据结构。ORM就是一个包装了一些数据的对象层，我们可以给它添加自定义的函数和属性来增强基础数据的功能。比如数据的验证、监听、数据持久化及服务器端回调处理等。

### Ajax
Ajax的一个限制是同源策略，它限制所有的请求都必须来自同一个域名、子域名和端口。

CORS打破同源限制，即跨域Ajax。只需要在HTTP协议的响应头中加入如下两行：
	
	Access-Control-Allow-Origin: some.com
	Access-Control-Reques-Method: GET, POST

### JSONP
另一种从远程服务器抓取数据的方式，很久以前已被标准化。原理是通过创建一个`script`标签，所辖的外部文件包含一段JSON数据，数据由服务器返回，作为参数包含在一个函数调用中。

## 本地存储数据
`localstorage`和`sessionstorage`。

数据均存储为字符串，所以如何你想保存的数据是对象或数字，则需要做类型转换。

