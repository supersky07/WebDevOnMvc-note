# 事件和监听

========

## 事件顺序
根据W3C模型，事件首先被目标元素捕捉，然后向上冒泡。在使用`addEventListerner`时可以指定事件处理程序的调用类型，捕捉或冒泡。true为事件捕捉，false为事件冒泡。

## 切换上下文
当使用`addEventListener`绑定事件时，上下文从局部变量切换为目标HTML元素：
	
	new function () {
		this.appName = 'test';
		
		document.body.addEventListerner("click", function () {
			//上下文发声改变，因为appName为undefined
			console.log(this.appName);
		}, false);
	};

若要保持原上下文，则需要将回调函数包装进一个匿名函数，采用闭包实现。

## 委托事件
由于事件冒泡，我们可以直接给父元素绑定事件监听，俗称事件代理。

## 自定义事件
自己定义除浏览器默认事件外的其他事件。

## 广播事件
通过发布/订阅消息模式实现。此种方式可以让你的应用程序易于拓展，而不必引入额外的交叉依赖和耦合，从而提高了应用的可维护性，添加额外功能也非常容易。
