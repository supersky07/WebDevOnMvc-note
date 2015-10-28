# 视图和模板

========

## 动态渲染视图

- document.createElement
- html片段塞到节点中
- 显示、隐藏某些节点

## 模板
采用模板引擎来管理视图。

## 模板存储
- 在Javascript中以行内形式存储
	- 违背了MVC架构的原则，将V和C耦合 
- 在自定义script标签里以汗内形式存储
- 远程加载
	- 依赖模板的加载速度
- 在HTML中以行内形式存储
	- 增加首包体积
	
将模板放入自定义的script标签的例子：
	
	<script type="text/x-tmpl" id="tmpl1">
		<span> ${getName()} </span>
	</script>
	
	<script>
		var data = {
			getName: function () {return "test";}
		};
		var node = jQuery("#tmpl1").tmpl(data);
		node.appendTo($("body"));
	</script>

## 绑定
绑定使得视图元素和模型挂接到一起，一旦数据变化时，视图会根据新修改后的对象做适时更新。

也就是说通过绑定，若数据变化，也不需要控制器去通知视图变化了。