# 依赖管理

========

Javascript缺少很多计算机高级程序语言所应有功能特性，其中之一就是依赖管理和模块系统。为了解决依赖管理，我们不得不一层一层的嵌套方法调用；为了解决模块系统，我们不得不在一个页面中按顺序引入多个script标签。这不仅让html变得臃肿，也让Javascript代码变得很难维护。

## CommonJS

它不是一个技术问题，而是需要共同讨论并达成一致的规范。

### 模块的声明

	//math.js
	exports.per = function (val, total) {
		return ((val / total) * 100);
	};

	//application.js
	var Maths = require("./application.js");
	Maths.per(1, 100);

### 模块和浏览器

在服务器端可以实现同步的加载方式，但在浏览器端实现CommonJS就变得很困难，因为它需要阻塞UI，去执行script加载。

CommonJS团队为此提出了一个规范”模块转换格式“，这种方式可以讲模块包装在一个回调函数中，以便更好的在客户端处理加载。

修改上面的代码，将它包含在一个转换格式中，来启用异步加载。

	//math.js
	require.define("math", function (require, exports) {
		exports.per = function (val, total) {
			return ((val / total) * 100);
		};
	});

	//application.js
	require.define("application", function (require, exports) {
		var per = require("./math").per;
		per(1, 100);
	}, ["./math"]);

## 模块加载器

为了在客户端实现CommonJS规范，我们需要引入模块加载器类库。

### Yabble

可以支持通过XHR加载模块或者使用script标签来加载模块。通过XHR来抓取模块的优势是不必再用转换格式把模块多包装一层。然而缺点就是需要在客户端使用`eval`执行，调试不方便，还带来安全问题。

### RequireJS

遵循异步模块定义（AMD）格式。主要的不同之处在于AMD的API是即时计算依赖关系，而不是延迟计算。

RequireJS和CommonJS模块相互兼容，只是包装转换的写法格式不同。

	<script>
		require(["lib/application", "lib/utils"], function (application, utils) {
			//do something else
		});
	</script>

RequireJS采用完全不同的与来定义模块——不使用`require.define()`，而是直接使用`define()`方法。
	
	define(["underscore", "./utils"], function (_underscore, utils) {
		return "";
	});

## 包装模块

虽然现在有了模块依赖和命名空间的解决方案，但加载的时候还是每个文件独占一个HTTP请求。我们依赖的所有模块都从远程加载，尽管异步，还会造成很严重的性能问题——启动延迟。

使用打包工具，静态分析文件，递归的计算它们的依赖关系。

## 模块的按需加载

### LABjs

一款简单的模块依赖管理器，它不需要服务器支持和CommonJS模块支持。使用它可以减少页面资源加载的阻塞。

	<script>
		$LAB
		.script('/js/json2.js')
		.script('/js/jquery.js').wait()
		.script('/js/jquery-ui.js')
		.script('/js/vapor.js');
	</script>

## 无交互行为内容的闪烁

如果依赖Javascript来操作样式，则需要将样式提取出来放入初始化CSS之中。否则会出现内容闪烁情况。